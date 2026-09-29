# Playbook — migrar un reporte BIRT al sidecar

Guía operativa **por `reportId`**. Pensada para repetirse cientos de veces sin
reescribir el motor.

Canon de principios: [`regla-migracion-reportes-birt.md`](../canon/regla-migracion-reportes-birt.md).  
Alta mecánica: [`register-report-sidecar.md`](register-report-sidecar.md).

Código: **Hospital-Reports**. Bitácora de programa: este doc (sección pitfalls = viva).

### Dónde vive cada regla (una sola verdad)

| Tema | Canon SDD | Cursor (Hospital-Reports) |
|------|-----------|---------------------------|
| Un `reportId` / params / listado `[x]` | este playbook + [`regla-migracion-reportes-birt.md`](../canon/regla-migracion-reportes-birt.md) | `migrate-report-incremental.mdc` |
| `urlLogo` (3 casos) | §2 abajo | `migrate-report-incremental.mdc` · `report-preview-and-turno-lessons.mdc` |
| Tablas vs schema de callable | [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md) §1 | `packages-pg-ts-qualify.mdc` |
| SoT packages Oracle | `Legacy-DB/Packages` solamente | `oracle-packages-sot.mdc` |
| Smoke PDF / no inventar filas | §4 | `report-preview-and-turno-lessons.mdc` · `smoke-data-ask-before-invent.mdc` |
| Hallazgos (dato/layout raro, no se arregla en el corte) | [`listado-reportes-birt-hallazgos.md`](../relevamiento/listado-reportes-birt-hallazgos.md) | — |

Si un README o una fila vieja de bitácora choca con esta tabla, **manda la tabla**.

---

## 0. Antes de tocar archivos

| Check | Acción |
|-------|--------|
| ¿Qué `.rptdesign` usa el Java legacy? | Grep del basename en **todos** los WAR (`ReportManager`, beans `BB*`, etc.) — no quedarse con el primer hit |
| ¿Hay variantes (GYE, MATERDEI, old)? | GYE módulo guardia = corte propio (`EpicrisisGYE`). MATERDEI / UNIONPERSONAL / SANJUANDEDIOS / ALPI / CEMIC / CPI / DUHAU = **no portar** ([`listado-reportes-birt-otro-cliente.md`](../relevamiento/listado-reportes-birt-otro-cliente.md)). No fusionar layouts |
| ¿Dónde se imprime en la UI? | Por **cada** call site: bean/método, pantalla/menú/URL `.faces`, ramas; al menos un camino para que el humano **descargue el PDF de referencia** |
| ¿Params / nulls por sitio? | Matriz: key × call site → valor tipado / `null` / `""` / ausente. Unión = contrato; SQL debe tolerar nulls reales (no solo el smoke feliz) |
| ¿Hay PDF de referencia del mismo reporte? | Preferir el descargado desde legacy con el flujo anterior |
| ¿Corte SDD? | Un slug por reporte o familia acordada; no “todo R5” |
| ¿Qué `ts.*` toca el print-path? | Listar FROM/JOIN/subqueries del `.rptdesign` **y** del `packages-pg` (contrato). El vs-Flyway del piloto lo cierra el corte que cablea: [`regla-migracion-reportes-birt.md`](../canon/regla-migracion-reportes-birt.md) § Restricciones punto 5. |

**Al cerrar:** smoke en `target/preview/`. **No** marcar `[x]` en
[`listado-reportes-birt-legacy.md`](../relevamiento/listado-reportes-birt-legacy.md) sin pedido
o confirmación explícita del humano.

Si el `.rptdesign` **no tiene caller** (`printReport` = 0): no portar al
sidecar; anotar en
[`listado-reportes-birt-legacy-sin-uso.md`](../relevamiento/listado-reportes-birt-legacy-sin-uso.md)
y preguntar si igual se marca `[x]` en el listado principal.

Si el basename es de **otro cliente** (sufijo UNIONPERSONAL, SANJUANDEDIOS,
MATERDEI, ALPI, CEMIC, CPI, DUHAU): **no** abrir corte; ya está en
[`listado-reportes-birt-otro-cliente.md`](../relevamiento/listado-reportes-birt-otro-cliente.md).
Universo = base + GEA (GEA no tiene diseño propio). `EpicrisisGYE` sí.

---

## 1. Diseño

1. Copiar a `Hospital-Reports/src/main/resources/designs/<reportId>.rptdesign`.
2. Oracle → PG (SQL datasets): `NVL`/`DECODE`/`ROWNUM`/`SYSDATE`/schemas.
3. Bindings a **minúsculas** ([`regla-birt-columnas-minusculas.md`](../canon/regla-birt-columnas-minusculas.md); tool `lowercase-birt-column-bindings.py`).
4. **No** “mejorar” el layout. Si el PDF de referencia difiere del `.rptdesign` elegido, **preguntar** cuál manda antes de ocultar columnas o firmar bloques.
5. Assets: watermark/logos → rutas que el sidecar reescribe (`assets/logos/…`), no paths WAR legacy.

### Scripts peligrosos en el diseño

| Tema | Regla |
|------|-------|
| `borrador` / watermark | Comparar con `Number(borrador)` (BigDecimal rompe `!= 1`) |
| Visibilidad de sección | Contrastar expresión exacta del legacy (base vs GYE) |
| Columna oculta (`visibility` true) | Revisar `colSpan` de títulos en esa columna |
| `pageBreak* = avoid/always` | No agregar sin evidencia; preferir layout legacy |
| Blob / firma en SELECT | Si el layout bindea `firma_*` y la columna no existe, BIRT 4.24 puede matar **toda** la table → `cast(null as bytea) as firma_…` o traer el blob real |

---

## 2. Sidecar / contrato HTTP

1. `hospital.reports.registered=…,<reportId>`.
2. `examples/api-requests/<reportId>.json` — **un solo archivo** (sin variantes).
3. Params = **inventario completo** de lo que legacy envía (cada `put` del bean
   + inyecciones de `ReportManager` que el contrato Api deba cubrir). **Nada
   afuera; no inventar.** Infra `DB_*` la completa el sidecar.
   **`urlLogo`:** `printReport` **siempre** lo setea (file `logo_prt_<cliente>`
   y/o pack de sesión). Si el layout es `params["urlLogo"]` file, el JSON
   **debe** mandarlo (`SDLC_VM` en PoC Cañada). **No** omitir porque el bean no
   hace `put`: el default sidecar `GEA` no es paridad. Omitir solo si el logo
   sale de BLOB SQL (RecetaPac / algunos Epicrisis) o el layout no usa la imagen.
   Filas viejas de bitácora “bean no put → omitir → GEA” valen **solo** si el
   layout es caso 2 o 3; el caso file se corrigió en EntradasPorDia (2026-09-11).
4. IT opt-in: `*BirtIT extends AbstractBirtRenderIT`.
5. Fixture de params si aplica: `src/test/resources/fixtures/…`.

### Params recurrentes (no confundir)

| Param | Uso |
|-------|-----|
| `DB_USER` | JDBC |
| `usuario` | Login de impresión en pie (si el diseño lo pide) |
| `cierreEpicrisis` | Texto de cierre del request; no asumir solo columna DB |
| `borrador` | `1` = watermark; tipar como número en scripts |

---

## 3. Packages y datos

1. Inventariar callables del diseño → `sql/packages-pg/CHECKLIST.md`.
2. **SoT Oracle = solo `Legacy-DB/Packages`** (spec + `*_BODY.sql`). **Prohibido**
   usar `Hospital-Legacy/RDBMS/**` como fuente de packages/procedures/functions.
3. Port real desde esa carpeta → `sql/packages-pg/`; **prohibido** mock que
   devuelva literales para “que pinte”.
4. **Forma del dataset:** legacy `{call PKG.x}` → migrado `{call schema.x}` (PG);
   **no** reemplazar el call por un `SELECT` embebido nuevo en el `.rptdesign`.
   SQL inline solo si el legacy ya traía SQL inline (port dialéctica del mismo texto).
   **Excepción BIRT+JDBC PG:** si el SP Oracle usa OUT cursor y
   `SPSelectDataSet`/`{call}` no entrega ResultSet en PG, admitir
   `SELECT * FROM schema.func(...)` con `func` = port en `packages-pg`
   (`RETURNS SETOF`/`TABLE`). Prohibido inventar el SQL de negocio en el diseño.
5. **Calificar tablas `ts.<tabla>`** en el SQL del port. El schema del
   callable (`personas.f_*`) no es el de las tablas (`ts.persona`). Nunca
   `ts.personas.f_*`. CTE/alias y `TRIM(BOTH FROM …)` no se prefijan `ts.`.
   `search_path` / JDBC `currentSchema=ts` **no** sustituyen el qualify.
   Install: `check_function_bodies=off` si el target aún no tiene todas las
   tablas (CREATE ok; runtime falla hasta el DDL).
6. Seed: maestros + hechos mínimos; export Oracle → `sql/seeds/` cuando haga falta paridad de datos.
7. Tras seed incompleto (faltan `det_*`, admin, etc.) las secciones “con flag S” salen vacías: completar seed, no inventar UI.
8. Publicar en CHECKLIST las `ts.*` del package (contrato). El vs-Flyway del piloto: [`regla-migracion-reportes-birt.md`](../canon/regla-migracion-reportes-birt.md) § Restricciones punto 5.

`MigratedCallableCheck`: default `fail` en runtime serio; `warn` solo para destrabar red/RDS en local.

---

## 4. Verify (smoke)

Checklist mínimo por reporte:

- [ ] Health UP + `POST /reports/run` → `%PDF` 200
- [ ] Secciones del PDF legacy aparecen (o documentar vacío con evidencia DB)
- [ ] Watermark solo si `borrador=1`
- [ ] Pie: usuario impresión correcto (no `postgres`)
- [ ] Sin columnas/firmas inventadas respecto al legacy elegido
- [ ] `examples/api-requests/<reportId>.json` actualizado
- [ ] Pitfall nuevo (si hubo) anexado abajo

Rebuild Docker / worker si cambió diseño o `birt-worker`.

---

## 5. Cierre del corte

1. Actualizar checklist packages si entraron callables.
2. Apuntar avance en bitácora R5 / SDD del slug.
3. **No** mezclar el siguiente reporte en el mismo cambio sin acuerdo.

---

## Bitácora de pitfalls (append-only)

> Agregar al **final**. Formato: fecha · reportId · síntoma · causa · fix.

### 2026-08-28 · Epicrisis (ex EpicrisisGYE)

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Secciones vacías con datos en DB | `row["ROW_NUMBER() OVER () as rownum"]` / `PRT_*` mayúsculas | Bindings `row["rownum"]` + minúsculas |
| Watermark nunca aparece | `borrador != 1` sobre BigDecimal; path WAR | `Number(borrador)`; rewrite a `assets/logos/report_watermark.png` |
| EVOLUCIONES rompe toda la table | Binding a `firma_personal` inexistente en SELECT | `cast(null as bytea) as firma_personal` (+ matrícula) |
| Título SIGNOS/EVOLUCIONES desaparece | `visibility=true` en columna Amb./Int. con título `colSpan` anclado ahí | Quitar Amb (layout base) o celda vacía + título en cols visibles |
| Firma Enfermero/Médico “inventada” | Venía de GYE; PDF ref = base | Alinear a `Epicrisis.rptdesign` base; renombrar `reportId` |
| Salto raro / página en blanco antes de evoluciones | `pageBreak*=avoid` + HTML alto | No forzar avoid; título+detalle según legacy |
| INTERCONSULTAS no sale con flag S | Visibilidad GYE `\|\| rownum==null` + tabla vacía no renderiza | Header en grid sin dataset (como CIERRE); detalle solo si hay filas |
| Pie muestra `postgres` | Diseño usaba `DB_USER` | Param `usuario` + default en engine |
| `cierreEpicrisis` vacío | Solo leía columna DB | Preferir `params["cierreEpicrisis"]` |
| Varios JSON de ejemplo | Ruido / params divergentes | Un solo `examples/api-requests/<reportId>.json` |
| Medicamentos/estudios sin filas | Seed sin `det_item_int` / `det_prest_int` | Completar exportador/seed |

---

## Plantilla para el próximo pitfall

```markdown
### YYYY-MM-DD · <reportId>
| Síntoma | Causa | Fix |
|---------|-------|-----|
| … | … | … |
```

### 2026-08-28 · Turno

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Docs requeridos vacíos con filas en DB | En PG `a \|\| null` es NULL; Oracle trataba null como '' en `\|\|` | `\|\| COALESCE('. ' \|\| NULLIF(obs,''), '')` |
| Teléfono vacío | Falta port `PERSONAS.f_get_persona_telefono` / `te_persona` | Package + seed `te_persona` |
| Preparación previa | `utl_i18n`/`dbms_lob` sobre BLOB | `general.blob_to_clob` + `substr` (UTF8→WIN1252) |
| Param boolean tipado como String | Worker solo tipaba decimal/date | `boolean` → Boolean en `BirtWorker.inferType` (true/false/1/0/S/N) |
| PDF de smoke en ruta ad‑hoc | Destino inconsistente | Siempre `target/preview/<reportId>-birt-docker.pdf` |
| Pie muestra `postgres` | Layout usaba `params["DB_USER"]` | Param `usuario` + binding `params["usuario"]` |
| `CA??ADA` / Ñ rota en PDF | Seed UTF-8 OK; apply vía PowerShell pipe rompió encoding | Aplicar seed con `-v` mount + `client_encoding=UTF8`; no es bug BIRT |
| Falta sección Documentos Requeridos Plan | Alias SQL `do` (reservado en PG) → dataset vacío → visibility hide | Renombrar alias (`dr`); `a \|\| '. ' \|\| COALESCE(obs,'')` (paridad Oracle null-as-empty) |
| Logo distinto al legacy (GEA vs Cañada) | Sidecar default `urlLogo=GEA`; legacy usa pack_logos del centro | Pasar `urlLogo` del pack (ej. `SDLC_VM` → `assets/logos/logo_prt_SDLC_VM.png`) |

### 2026-09-04 · ConsultaAgendaGeneradas

| Síntoma | Causa | Fix |
|---------|-------|-----|
| PDF 200 ~2 KB vacío; log `function … does not exist` / “does not return a ResultSet” | `SPSelectDataSet` + `{call}` con OUT `refcursor` Oracle no porta a JDBC PG (arity OUT + tipos `numeric`/`unknown`) | Port `RETURNS SETOF rowtype` en `packages-pg` + `JdbcSelectDataSet` / `SELECT * FROM turnos.p_get_turnos_fecha(...)` (lógica sigue en package; no SQL inventado en el diseño) |
| `invalid input syntax for type numeric: "831,624"` | Dataset param `string`: BIRT formatea miles | `regexp_replace(..., '[^0-9-]', '', 'g')` antes de `::numeric` |
| `invalid input … timestamp: "Sep 4, 2026 12:00 AM"` | Fechas del dataset como `string` → locale BIRT | Dejar `dataType=dateTime` → bind TIMESTAMP |
| 0 filas con fecha pasada pese a seed en `turno` | Print-path: día `< trunc(hoy)` lee `turno_vencido` | Smoke con fecha ≥ hoy en `turno`, o poblar `turno_vencido` |

### 2026-09-15 · ConsultaAgenda (T5.5 hijo PDF)

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Grilla con filas; PDF con columnas y **sin valores** | Reports portó y fumó contra PG **completa**. Api/Web piloto **no** tenía `ts.turno_vencido` (Flyway por cortes). `p_get_turnos_fecha` hace `UNION ALL` fijo → PG corta toda la función (también hoy). BIRT arma cabeceras. Siguiente palo: `equipo_serv_centro` (D-TUR-17) | **DoR del cable Api/Web:** cruzar lista `ts.*` del reporte vs Flyway de **esta** PG. Sidecar `[x]` no alcanza. DDL canónico (tabla vacía desbloquea; job T6) o rama omitida (T4). Params BIRT (`44983` / `""`→0) después de que el SELECT corra |

### 2026-09-04 · ConsultaAgendasReemplazadas

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Callable/seed vacíos o error tras move PoC | Port y seed seguían en public.* | Calificar dominio 	s.*; reinstall + smoke PDF 	arget/preview/ConsultaAgendasReemplazadas-birt-docker.pdf (200, ~4.8 KB) |

Confirmado listo: listado [x] ConsultaAgendasReemplazadas (HOSPITAL_2).

### 2026-09-08 · ConsultaTurnosPaciente

| Síntoma | Causa | Fix |
|---------|-------|-----|
| BIRT: design XML invalid / `oda-data-set` unclosed | Tras recortar `cachedMetaData` a 17 cols, quedaron estructuras Oracle huérfanas (sin `list-property`) | Borrar el bloque huérfano; validar XML parse antes del smoke |
| Footer `postgres` / JDBC user | Diseño legacy usaba `params["DB_USER"]` | Scalar `usuario` + pie `params["usuario"]` (como Turno) |
| `{call …}` + OUT cursor no entrega ResultSet en JDBC PG | SP Oracle con refcursor | `JdbcSelectDataSet` → `SELECT * FROM turnos.p_get_turnos_paciente(...)` (port print-path SoT `TURNOS_BODY`) |

Smoke: `target/preview/ConsultaTurnosPaciente-birt-docker.pdf` (HTTP 200, ~19 KB, con filas) contra RDS PoC + `quarkus:dev`. Confirmado listado [x] ConsultaTurnosPaciente (HOSPITAL_2 + HOS-APP).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| PDF con cabecera (paciente) pero **0 filas** pese a SP con datos | `boundDataColumns` del table aún referían ~66 cols Oracle (`duracion_turno_mtos`, …) ausentes en `turno_paciente_row` → BIRT aborta el detail | Recortar bindings a las 17 cols PG + calculados (`prof_equipo`, `fecha_cancela`, …) |
| Con `fechaDesde` null/blank el PDF filtraba todo | `birt-worker` `toDate("")` → `new Date()` (hoy) → `fecha_hora_tur_ini > hoy` | `toDate` blank/null → `null` JDBC; en el JSON canónico **dejar la key** con `null` (no omitir params) |

### 2026-09-08 · ConsultaTurnosAsignadosXOperador

| Síntoma | Causa | Fix |
|---------|-------|-----|
| `Failed to find out data source of data set TURNOS_OPERADOR` | En el Oracle design, `dataSource` vive **entre** `cachedMetaData` y el `resultSet` hermano; un replace que corta `cachedMetaData`→`resultSet` borra `dataSource` | Reinsertar `<property name="dataSource">HOSVER9</property>`; el migrate script debe incluirlo en el bloque generado |
| PDF header-only (200 ~17 KB) pese a 59 filas en PG | Worker tipa decimal blank/null → `0`; filtros `id_personal_otorga = 0` / `id_call_center = 0` no matchean | En el port: `NULLIF(an_id_*, 0)` para opcionales (paridad Oracle null = sin filtro) |
| Smoke centro 14 / servicio 21 / Ago 2026 = 0 filas TELEFONICO | Seed PoC no tiene otorgados telefónicos en ese rango | Usar centro/servicio/fechas con filas reales (ej. 6/48, Mar–Jun 2026) |

Smoke: `target/preview/ConsultaTurnosAsignadosXOperador-birt-docker.pdf` (HTTP 200, ~23 KB, 3 págs, TELEFONICO/AUSENTE). Listado marcado migrado.

### 2026-09-08 · PreparacionPrevia

Inline SQL (JdbcSelectDataSet). BLOB vía `general.blob_to_clob`.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Seed Oracle id 17 no entra en PG con grafo completo | `cod_prestacion`/`prestacion` arrastran FKs (`seccion_nomen`, NOT NULL `extra`, …) | Upsert `preparacion_prest` id=17 con BLOB real Oracle + parent `prestacion` ya existente en RDS (ej. COLANGIORESONANCIA); no inventar maestros |
| Layout sin pie de usuario | Legacy footer vacío (no `usuario` en design) | No agregar binding de pie; JSON puede llevar `usuario` (ReportManager) sin usarlo en layout |
| `urlLogo` en design pero bean no lo put | Callers solo `idPreparacionPrest` | Omitir `urlLogo` en JSON → engine default GEA (estilo Epicrisis) |
| PDF vacío con id 487 (hay filas en PG) | HTML en `preparacion` es WIN1252 (`ñ`=`0xf1`); `convert_from(…,'UTF8')` aborta el dataset | `general.blob_to_clob` UTF8→WIN1252; chunk con `substr` sobre texto (no `substring` bytea) |

Smoke: `target/preview/PreparacionPrevia-birt-docker.pdf` contra RDS + `quarkus:dev`. Listado marcado migrado.

### 2026-09-08 · ConfirmacionDatosPaciente

Inline SQL (JdbcSelectDataSet ×4). Sin packages. SoT HOSPITAL_2 (no variante SANJUANDEDIOS).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Binds Oracle `:id_paciente` fallan en PG JDBC | Named binds Oracle-only | Positional `?` en los 4 datasets |
| Columnas vacías / visibility oculta tablas | Bindings UPPER vs PG lowercase | `lowercase-birt-column-bindings.py` + migrate script |
| CORREOS sin filas en metadata | Legacy dejó `resultSet`/`cachedMetaData` vacíos | Rellenar `mail` en metadata antes del lowercase |
| `urlLogo` declarado pero no usado en layout; bean no lo put | Callers solo `idPaciente` | Omitir `urlLogo` en JSON → engine default GEA; no inventar logo en layout |
| Layout sin pie de usuario | Footer legacy solo `new Date()` | No agregar binding; JSON lleva `usuario` (ReportManager) sin usarlo en layout |
| Teléfonos solo `envio_sms='S'` | Paridad SQL legacy (no `f_get_persona_telefono`); HOSPROD casi no tiene `S` | Conservar filtro; seed PoC fuerza un `te_persona.envio_sms='S'` (paciente `6700`) |
| PDF sin domicilio/tel/mail con URBANI | RDS sin `domicilio_persona`/`mail_persona` (y te sin SMS) | Copiar paciente `6700` ABREGO + maestros (`tools/CopyPacienteContactosOracleToPg.py`) |

Smoke: `target/preview/ConfirmacionDatosPaciente-birt-docker.pdf` (paciente `6700` ABREGO, domicilio/tel/mail). Listado marcado migrado.

### 2026-09-08 · DurAtenXGrpPrestServ

Inline SQL (JdbcSelectDataSet `DuracionPrest`). Sin packages. SoT HOSPITAL_2.
Call site único: `BBPrestGrpPrestTur.actionBtnImprimir` rama `tipoFiltro == SERVICIO`
(no Pers / Equipo).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Footer `postgres` / JDBC user | Diseño legacy usaba `params["DB_USER"]` | Scalar `usuario` + pie `params["usuario"]` |
| `s.*` en PG trae `hab_turno_call_center` extra | Schema drift vs metadata Oracle del design | SELECT explícito de las 9 cols + `prestacion` (mismo negocio) |
| Worker tipa decimal blank → `0` | Filtros `id_* = 0` no matchean | `NULLIF(?, 0)` en binds; `-1` sigue = todos los grupos |
| `prest_grp_prest_tur_serv` vacío en RDS | Seed PoC sin detalle de duración | `tools/CopyPrestGrpPrestTurServOracleToPg.py` (centro 14 / servicio 22) |
| Padres `prestacion` fallan NOT NULL | PG exige `req_ayuno`/`ctd_hs_ayuno`/… | Upsert mínimo con defaults `N`/`0` (solo para smoke) |
| Bean no put `urlLogo` | Solo params de filtro + labels | Omitir `urlLogo` en JSON → engine default GEA |

Smoke: `target/preview/DurAtenXGrpPrestServ-birt-docker.pdf` (HTTP 200, ~19 KB, 2 págs, TOMOGRAFIA filas + `demo.impresion`). Listado marcado migrado.

### 2026-09-08 · DurAtenXGrpPrestPers + DurAtenXGrpPrestEquipo

Inline SQL (JdbcSelectDataSet `DuracionPrest`). Sin packages. SoT HOSPITAL_2.
Call site único: `BBPrestGrpPrestTur.actionBtnImprimir` ramas `PERSONAL` / `EQUIPO`
(no Servicio — ya migrado; no Excel `actionBtnExportarExcel`).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Footer `postgres` / JDBC user | Diseño legacy usaba `params["DB_USER"]` | Scalar `usuario` + pie `params["usuario"]` |
| `s.*` en PG trae `hab_turno_call_center` extra | Schema drift vs metadata Oracle del design | SELECT explícito (Pers: 9 cols + prestacion; Equipo: 8 cols + prestacion — sin `mtos_duracion_tur_1_vez`) |
| Worker tipa decimal blank → `0` | Filtros `id_* = 0` no matchean | `NULLIF(?, 0)` en binds numéricos; `-1` = todos los grupos |
| Pers: bean put `idGrpPrestTurPers` pero SQL legacy no lo filtraba | Scalar huérfano en SoT (solo label cabecera) | Conservar scalar; **no** inventar WHERE de grupo (paridad queryText) |
| Equipo: design scalar `idGrpPrestTurEqui` vs bean `idGrpPrestTurEquipo` | Truncación BIRT del nombre | Renombrar scalar + `paramName` binds a `idGrpPrestTurEquipo` (JSON = bean) |
| Tablas detalle vacías / incompletas en RDS | Seed PoC | `CopyPrestGrpPrestTurPersOracleToPg.py` (14/3/616073) + `CopyPrestGrpPrestTurEquipoOracleToPg.py` (14/55/4000008) |
| Bean no put `urlLogo` | Solo params de filtro + labels | Omitir `urlLogo` en JSON → engine default GEA |

Smoke: `target/preview/DurAtenXGrpPrestPers-birt-docker.pdf` (HTTP 200, ~18 KB, 1 pág, ECOGRAFIA/DELLAMAGGIORE + seed `persona`/`personal`/`personal_servicio` 616073) +
`target/preview/DurAtenXGrpPrestEquipo-birt-docker.pdf` (HTTP 200, ~17 KB, 1 pág, HOLTER). Listado marcado migrado.

### 2026-09-08 · TurnosAReasignar

Callable SoT `TURNOS.p_get_turnos_a_reasignar` → `f_get_turnos_a_reasignar`
(`Legacy-DB/Packages/TURNOS_BODY.sql` ~11840). Tabla fuente `ts.turno_a_reasignar`
(no `ts.turno`). Call site: `BBTurnosAReasignar.actBtnImprimir` (HOSPITAL_2 + HOS-APP).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Footer `postgres` | Diseño usaba `params["DB_USER"]` | Scalar `usuario` + pie |
| SP OUT cursor no entrega ResultSet en BIRT/JDBC PG | Excepción acotada | `SELECT * FROM turnos.p_get_turnos_a_reasignar(...)` SETOF |
| PDF vacío / sin filas | Dataset apuntaba a `HOSVER9` inexistente (SoT `HOSDESA`) | `dataSource=HOSDESA` |
| Cabecera fechas/labels en blanco | `params["fechaDesde"]` sin `.value` | `.value` en binds de cabecera |
| Bindings huérfanos (grid anidado) | Metadata Oracle completa en 2 consumers | Trim `boundDataColumns` ×2 |
| Bean put `equipo` sin scalar en diseño | Label cabecera | Agregar scalar `equipo` |
| Bean no put `urlLogo` | — | Omitir en JSON → default GEA |
| RDS sin filas | PoC vacío | `CopyTurnoAReasignarOracleToPg.py` (6/47/698091 dic-2026) |

Smoke: `target/preview/TurnosAReasignar-birt-docker.pdf` (HTTP 200, ~36 KB, 6 págs,
NEUROS/PSICOMOTRICIDAD/ALMADA + filas). Listado marcado migrado.

### 2026-09-09 · RecetaPac

Inline SQL ×4 (`RECETA_PAC_AMB`, `DET_RECETA_PAC_AMB`, `ATENCION`, `MARICULA_PERSONAL`).
SoT HOSPITAL_2. Happy path: Recepción → Impresión Masiva → Imprimir
(`BBImpresionMasivaRecetas`, `mostrarFirma=S`). **No** es A4_UNIFICADA / Duplicada /
Oncologica / ticket vía `ReportPrintingManager.imprimirReceta`.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Barcode / callables Oracle | Oracle `TS.GENERAL.f_*` / `TS.PERSONAS.f_*` | Port PG `general.f_get_interleaved_2_5` / `personas.f_*` (no `ts.personas.f_*`; tablas sí `ts.*`) |
| `DECODE`/`NVL` en PG | Dialecto Oracle | `CASE` / `COALESCE` en migrate |
| Cabecera vacía / `_outer` | Lowercase no toca `row._outer["COL"]` | Post-pass lower en migrate |
| Logo “resource not reachable” / BLOB `oracle.sql.BLOB@` | Copy sin `getBytes` + pack vacío | `to_py` BLOB→bytes; upsert `pack_logos` |
| Médico García Ana / MN:12345 | PoC pisó persona id=2 + matrícula leftover | Upsert persona/matricula desde Oracle; borrar leftovers |
| Firma ausente | No se copiaba `FIRMA_PERSONAL` | Copy `tipo_doc_adi`/`doc_adi_adm`/`doc_adi_adm_persona` |
| Barcode vacío | Plugin EAN13 no está en BIRT 4.24 runtime | Text-data DANI 2of5 + `f_get_interleaved_2_5` (R3.1) |
| RDS sin recetas / padres | PoC incompleto | `CopyRecetaPacOracleToPg.py` (persona/convenio/item/firma/logo) |

Smoke: `0000012667123` (1 mitad) + `0000012689781` (2ª mitad INDICACIONES; legacy sin firma/barcode ahí).
Confirmado listado [x] RecetaPac (HOSPITAL_2 + HOS-APP). ECS `grupogea/reports:0.2.6`.

### 2026-09-09 · RecetaPacDuplicada

Misma base SQL que RecetaPac + param `nroDetRecetaItemPac` (DET filtra un ítem) +
label **DUPLICADO**. SoT HOSPITAL_2. Se dispara **después** de RecetaPac cuando
`det.req_duplicado` (Impresión Masiva / consulta entre fechas / entrega web / …).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| (mismo stack callables) | — | Reuse `f_get_interleaved_2_5` + `personas.f_*` |
| Barcode EAN13 | Sin plugin 4.24 | DANI 2of5 (igual RecetaPac) |
| Seed | — | `CopyRecetaPacOracleToPg.py 0001240220000272` |

Smoke: `target/preview/RecetaPacDuplicada-birt-docker.pdf` (HTTP 200, DUPLICADO +
SINCICH / CLONAZEPAM). Confirmado listado [x] RecetaPacDuplicada (HOSPITAL_2 + HOS-APP).
Smoke logo OK con `0000012689835` (centro+pack; la anterior tenía `id_centro_ate_prescribe` null).

### 2026-09-09 · RecetaPacOncologica

SoT HOSPITAL_2. Param **`nroReceta`** (no `nroRecetaPac`); sin `mostrarFirma`.
Logo archivo `logo_prt_onco_<nombreCliente>.png` (no BLOB). Happy path: WAR **RECETAS**
→ Receta Oncológica / Histórico (`BBRecetasDigitales`); **no** Impresión Masiva.
Inline SQL ×4; callables ya portados (`general.f_get_interleaved_2_5`,
`personas.f_get_personal_matricula` / `f_get_especialidad_full`). DET pagina de a 6.
HOSPROD sin filas ONCO → seed clone `0000099000001` + `tipo_receta_df`.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Worker timeout 180s / CPU spin; DET `idle in transaction` ClientRead | Fila detalle `8932` con `height=115mm` fijo → loop page-break BIRT 4.24 | Quitar `height` de esa fila (+ `height=100%` anidados / master `185mm` en migrate) |
| Logo resource | Prefijo relativo WAR | Rewrite `logo_prt_onco_` → `assets/logos/`; assets `logo_prt_onco_GEA` / `UNIONPERSONAL` |
| `ctd_indicada` / `estado` en diseño legacy | Columnas renombradas | `ctd_prescrip` / `o_estado` (como RecetaPac) |
| Sin data onco en Oracle PoC | Solo `TRATAMIENTO_ORDINARIO`/`OFTALMOLOGICA` | `SeedRecetaPacOncologicaSmoke.py` |

Smoke: `target/preview/RecetaPacOncologica-birt-docker.pdf` (HTTP 200 ~20 KB,
PROGRAMA ONCOLOGIA MEDICAMENTO / ROJAS / Ciclo 1 / logo 250×140).
Confirmado listado [x] RecetaPacOncologica (HOSPITAL_2 + HOS-APP + RECETAS).

### 2026-09-09 · ResumenAtencion

SoT **HOSPITAL_2** `ResumenAtencion.rptdesign` (no AGP; sibling `ResumenAtencionAmb`
huérfano sin callers). Happy path: Ambulatoria → Imprimir → **Resumen Atención**
(`BBImpresionAmbulatoria.imprimirResumenAtencion`). Params: `idAtencionAmb`,
`esAmbulatorio`, `logoReporte`, `urlFirmaPersonal` (+ ReportManager `usuario`/`urlLogo`).
17 datasets SQL inline; callables ya en packages-pg:
`general.f_get_edad_anio`, `historia_clinica.p_get_valor_det_form_col`.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Logo WAR `../..` + `logoReporte` | Path relativo no existe en sidecar | Expresión = `params["logoReporte"]`; `resolveNamedLogoParam` materializa `/resources/imagenes/logo_prt_X.png` |
| Pie `Usuario Impresión: postgres` | Layout usaba `DB_USER` | Scalar `usuario` + pie `params["usuario"]` (**no** tocar `odaUser` → JDBC) |
| `ctd_indicada` binding warn | Layout legacy / SQL H2 `ctd_prescrip` | Alias `d.ctd_prescrip as ctd_indicada` |
| `logo_impresion` binding warn | Columna fantasma en ATENCION | `dataSetRow["logo_impresion"]` → `null` (imagen usa `logoReporte`) |
| convert-oracle hang en diseño 650 KB | Regex `decode` catastrófico | Parser de paréntesis en `migrate-resumen-atencion-design.py` |

Smoke: `target/preview/ResumenAtencion-birt-docker.pdf` (HTTP 200 ~8.6 KB,
DIAZ/NESTOR `idAtencionAmb=768590`, evoluciones, `demo.impresion`).
Confirmado listado [x] ResumenAtencion (HOSPITAL_2 + AGP). No marcar `ResumenAtencionAmb`.

### 2026-09-10 · DietaAmb

SoT **HOSPITAL_2** `DietaAmb.rptdesign` (HOS-APP espejo). Happy path: Ambulatoria →
Imprimir → **Plan de alimentación y Recomendaciones**
(`BBImpresionAmbulatoria.imprimirDieta`). Param negocio: `idAtencionAmb`.
SQL inline ×4 (layout usa ATENCION + DIETAS_RECOMENDACIONES). Callable:
`general.f_get_edad_anio` (ya en packages-pg).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| CLOB dieta `utl_i18n`/`dbms_lob` | Oracle-only | `general.blob_to_clob` + `substr` (patrón PreparacionPrevia) |
| Pie `DB_USER` | JDBC user | Scalar `usuario` (no tocar `odaUser`) |
| `nro_afiliado` scalar + `IN (select…)` | Multi-OS en PG | `min(o.nro_afiliado)` via join det/ord |

Smoke: `target/preview/DietaAmb-birt-docker.pdf` (copy Oracle HOSPROD
`CopyDietaAmbOracleToPg.py 2023723` — COUGET AIDA BEATRIZ, 2 filas
`tratamiento_pac_amb` RECOMENDACION, `demo.impresion`).
Confirmado listado [x] DietaAmb (HOSPITAL_2 + HOS-APP).

### 2026-09-10 · InformeEstudio (sin uso)

Diseño huérfano `HOSPITAL_2/.../otrosEstudios/InformeEstudio.rptdesign`:
0 `printReport`. Confirmado listado [x] por pedido humano. Inventario:
[`listado-reportes-birt-legacy-sin-uso.md`](../relevamiento/listado-reportes-birt-legacy-sin-uso.md).
No hay diseño/JSON en Hospital-Reports. No marcar `InformePac` / `InformeIntPac`.

### 2026-09-10 · ResumenAtencionAmb (sin uso)

Diseño huérfano `HOSPITAL_2/.../ResumenAtencionAmb.rptdesign`: 0 callers.
Confirmado listado [x] por pedido humano. Inventario:
[`listado-reportes-birt-legacy-sin-uso.md`](../relevamiento/listado-reportes-birt-legacy-sin-uso.md).
No hay diseño/JSON en Hospital-Reports. No marcar `ResumenAtencion` (ya migrado).

### 2026-09-10 · DietaInt

SoT **HOSPITAL_2** `DietaInt.rptdesign` (no está en HOS-APP). Happy path: Internación →
**Indicaciones Post Internación** → Imprimir → **Plan de alimentación y Recomendaciones**
(`BBIndicacionesPostInternacion.imprimirDieta`). Param negocio: `idInternacion`.
Callables: `general.f_get_edad_anio`, `personas.f_get_personal_matricula` (PERSONAS_BODY).
Variantes UI: GYE / guardia consultorio observación (`BBIndicacionesPostInternacionObsGuardia*`).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| CLOB dieta `utl_i18n`/`dbms_lob` | Oracle-only | `general.blob_to_clob` + `substr` |
| Pie `DB_USER` | JDBC user | Scalar `usuario` |
| `||` / `decode` cabecera | NULL PG / Oracle | `COALESCE` + `CASE` |

Smoke: `target/preview/DietaInt-birt-docker.pdf` (copy Oracle
`CopyDietaIntOracleToPg.py 8030018260` — MORENO ALICIA MARIA, 2 RECOMENDACION,
`demo.impresion`).
Listado: confirmado `[x]` 2026-09-10 (HOSPITAL_2).

### 2026-09-10 · EntregaEstudios

SoT **HOSPITAL_2** `EntregaEstudios.rptdesign` (no está en HOS-APP). Caller único:
`BBEstadoEstudiosFechaProbableEntrega.actBtnImprimirReporte`. Excel
(`actBtnxportarExcel`) **no** es este diseño. Callable SoT
`Legacy-DB/Packages/INFORMES_BODY.sql` `p_informes_fecha_probable` /
`f_informes_fecha_probable` / `pp_comp_tmp_practicas_informar`. Dataset migrado:
`SELECT * FROM informes.p_informes_fecha_probable(?,?,?,?,?)` (excepción BIRT+JDBC PG).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| `{call …}` OUT refcursor | JDBC PG no entrega ResultSet | `SELECT * FROM informes.p_informes_fecha_probable` |
| Pie `DB_USER` | JDBC user | Scalar `usuario` |
| Workspace TMP | GTT/tabla Oracle | `ts.tmp_practicas_informar` + lock transaccional |

Smoke: `target/preview/EntregaEstudios-birt-docker.pdf` (copy Oracle
`CopyEntregaEstudiosOracleToPg.py 12 85` — POLICLINICA PUCARA C+ CBA /
MEDICINA INTERNA, fecha probable 2025-03-26, NARVAJA, `demo.impresion`).
Listado: confirmado `[x]` 2026-09-10 (HOSPITAL_2).

### 2026-09-10 · EntradasPorDia

SoT **HOSPITAL_2** `EntradasPorDia.rptdesign` (no está en HOS-APP). Caller único:
`BBEntradasPorDia.actionBtnImprimir`. Excel (`actionBtnExportarExcel`) **no** es este diseño.
Callable SoT `Legacy-DB/Packages/LABORATORIO_BODY.sql` `p_entradas_dia` / `f_entradas_dia`.
Dataset: `SELECT * FROM laboratorio.p_entradas_dia(...)` (excepción BIRT+JDBC PG).
`marcarImpreso=S` actualiza `ord_lab_pac.impreso_entradas_dia`. **Diferido:**
`admision.f_get_cama_internacion_actual`. BIRT manda `nroOrd*=0` y origen `''` en vez de null
(no-filtro).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| JSON sin `urlLogo` → logo GEA | Atajo “bean no put → omitir → default sidecar GEA” | `ReportManager` siempre inyecta file `urlLogo`; JSON `SDLC_VM` |
| 2ª corrida PDF vacío ~4.8 KB | `marcarImpreso=S` + `ordenesImpresas=N` | Ejemplo `marcarImpreso=N`; reprint `ordenesImpresas=S` |
| `nroOrd*` 0 / origen `''` 0 filas | Worker tipa null decimal→0 / string→'' | `NULLIF(...,0)` / `NULLIF(trim,'')` en el port |
| `idCentroAte` no cambia filas | SP no filtra centro; scalars de sesión no bindeados | Paridad; filtrar fecha/origen/nro |

Smoke: `target/preview/EntradasPorDia-birt-docker.pdf` (copy
`CopyEntradasPorDiaOracleToPg.py 2026-06-04` — BRUHN, ANALISIS, `demo.impresion`,
`urlLogo=SDLC_VM`).
Listado: confirmado `[x]` 2026-09-10 (HOSPITAL_2).

### 2026-09-10 · EvolucionesPorProfesional

SoT **HOSPITAL_2** `EvolucionesPorProfesional.rptdesign` (no HOS-APP). Caller único del
listado: `BBConsultaEvolucionesProfesional.actionBtnImprimir`. La impresora **por fila**
es `EvolucionPacInt.rptdesign`. Excel **no** es este diseño. SQL inline (no SP).
Callable: `personas.f_get_personal_matricula` (SoT `PERSONAS_BODY.sql`). `-1`/`'-1'` = TODOS.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| XML roto al insertar `usuario` | El replace quitaba `</parameters>` | Insertar el scalar **antes** de `</parameters>` |
| Strip `designerValues` se come el pie | Regex `.*?` hasta el CDATA de `new Date()` | Cortar CDATA de `designerValues` por el primer `]]>` de ese bloque |

Smoke: `target/preview/EvolucionesPorProfesional-birt-docker.pdf` (ABREGU internación
`8010022204`, 2026-06-03/04, `urlLogo=SDLC_VM`, `demo.impresion`).
Listado: confirmado `[x]` 2026-09-10 (HOSPITAL_2).

### 2026-09-10 · EvolucionPacInt

SoT **HOSPITAL_2** `EvolucionPacInt.rptdesign` (no HOS-APP). Camino feliz:
`BBEvolucionInt.actBtnImprimirEvolucion` (fila en Evolución / Diagnóstico). También
`actBtnImprimirSelected`, consulta evoluciones (icono fila), cierre turno enfermería,
auditoría, observación GYE/consultorio. El Imprimir de listado
`EvolucionesPorProfesional` **no** es este diseño. SQL inline (no SP). Callables:
`general.f_get_edad_string`, `personas.f_get_personal_matricula`. `idPersonal` solo
si `XMLVersionParser.esClienteALPI()`.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Firma vacía | Nested image `FIRMA_PERSONAL` + `row["doc_adi"]` sobre EVOLUCIONES; PoC sin BLOB | Subquery `doc_adi` en EVOLUCIONES (patrón RecetaPac); copy `FIRMA_PERSONAL` con `blob.getBytes` |
| Copy sin firma | Copy internación no traía `doc_adi_adm` | `CopyEvolucionesPorProfesionalOracleToPg` copia tipo/docs/persona del médico |

Smoke: `target/preview/EvolucionPacInt-birt-docker.pdf` (`idEvolucionPacInt=5047283`,
internación `8010022204`, DUTTWEILER JPEG, `urlLogo=SDLC_VM`, `demo.impresion`).
Listado: confirmado `[x]` 2026-09-10 (HOSPITAL_2).

### 2026-09-10 · ExamenFisico

SoT **HOSPITAL_2** `ExamenFisico.rptdesign` (no HOS-APP). Happy path: tile
**ATENCION MEDICA** → centro/servicio → lista espera → **Pacientes atendidos** →
icono imprimir de fila → diálogo Impresión → fila **Examen físico**
(`BBImpresionAmbulatoria.imprimirExamenFisicoBoolean`). También TF, oftalmología,
domiciliaria, demanda espontánea/GYE/consultorio y adjunto mail. El Imprimir de
turnos en lista de espera **no** es este diseño. Param: `idAtencionAmb`. SQL
inline (ANTRO/SIGNOS eran `SPSelectDataSet` con `SELECT`). Callables:
`general.f_get_edad_anio`, `historia_clinica.p_get_valor_det_form`
(`HISTORIA_CLINICA_BODY.sql`).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| 500 design=null en Docker local | Convención classpath no ve el bind de `designs/` | `hospital.reports.birt.design.ExamenFisico=/app/src/main/resources/designs/ExamenFisico.rptdesign` |
| Pie `DB_USER` | User JDBC | Scalar `usuario` |

Smoke: `target/preview/ExamenFisico-birt-docker.pdf` (copy
`CopyExamenFisicoOracleToPg.py 207516` — PEREYRA MARIA JULIA, `urlLogo=SDLC_VM`,
`demo.impresion`).
Listado: confirmado `[x]` 2026-09-10 (HOSPITAL_2).

### 2026-09-11 · EsquemaTratamiento

SoT **HOSPITAL_2** `EsquemaTratamiento.rptdesign` (no HOS-APP). Happy path: tile
**HOSPITAL DE DIA** → centro atención + centro procedimiento → **Aceptar** →
Configuración → **esquema_tratamiento** → buscar/seleccionar esquema → Acciones
**Imprimir** (`BBEsqTratamiento.actBtnImprimir`, `idEsqTratamiento`). Alterno:
**ADMINISTRACION GENERAL** → **dominios_medicos** → **esquema_tratamiento**.
Los Imprimir de unidad/tipo procedimiento apuntan al **mismo** `.rptdesign` pero
mandan `idUnidadProcedimiento` / `idTipoProcedimiento` (el diseño no los usa).
Agenda `msg.imprimir_esquema` → `EsquemaTratamientoPac` (otro reporte). SQL
inline (sin SP). Logo file: `params["urlLogoSmall"]`.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| PDF con labels y sin nombre de esquema; `bigint = character varying` | Dataset `ESQUEMA_TRATAMIENTO` bindeaba `idEsqTratamiento` como varchar | `CAST(? AS bigint)` + `dataType` float |
| Detalle de protocolo vacío | HOSPROD `det_dia_ciclo_tratamiento` = 0 filas para id=1 | Paridad de datos, no layout |
| 500 design=null en Docker local | Convención classpath no ve el bind de `designs/` | `hospital.reports.birt.design.EsquemaTratamiento=/app/src/main/resources/designs/EsquemaTratamiento.rptdesign` |

Smoke: `target/preview/EsquemaTratamiento-birt-docker.pdf` (copy
`CopyEsquemaTratamientoOracleToPg.py 1` — **PRUEBA ESQ TRAT**, 3 ciclos / 5 días,
revisor ABREGO, `urlLogoSmall=SDLC_VM`, `demo.impresion`).
Listado: confirmado `[x]` 2026-09-11 (HOSPITAL_2).

### 2026-09-11 · EsquemaTratamientoPac (sin uso HIS)

SoT **HOSPITAL_2** `EsquemaTratamientoPac.rptdesign`. Callers vigentes: **0**
(`printReport` comentado; el acto Imprimir/Imprimir esquema dispara
`ProtocoloCitostaticos.rptdesign`). HOSPROD sin filas en `esq_tratamiento_pac`.
Sidecar: diseño PG + JSON/IT (corte ya portado). Inventario:
[`listado-reportes-birt-legacy-sin-uso.md`](../relevamiento/listado-reportes-birt-legacy-sin-uso.md).
No marcar `ProtocoloCitostaticos`. Listado `[x]` 2026-09-11 por pedido humano.

### 2026-09-11 · EstadisticaCenso

SoT **HOSPITAL_2** `EstadisticaCenso.rptdesign` (no HOS-APP). Happy path: tile
**ADMISION INTERNADOS** → centro / puesto de admisión → **Consultas** →
**estadistica_censo** → fecha/convenio/servicio → armar grilla → **Imprimir**
(`BBEstadisticaCenso.generarReportePDFAction`). No es consulta censo general,
censo gráfico ni **consulta censo paciente** (`EstadisticaCensoPaciente`).
Callable `CENSO.p_estadistica_censo` (SoT `Legacy-DB/Packages/CENSO_BODY.sql`
~3251) → fill `f_get_internacion_cama` (~1196) print-path. Logo file
`params["urlLogo"]`. Pie `usuario`.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| PDF vacío / sin internaciones con convenio TODOS | Worker decimal null → `0`; SQL filtraba `id_convenio=0` | Coerce `0`→NULL en convenio/servicio/sector. **El JSON canónico conserva** `"idConvenio": null`, `"idServicio": null`, `"idSectorInt": null` (inventario bean + arg SP). Prohibido borrar keys para que el smoke pinte. |
| `dataSetRow` not defined | Footer bound `LEYENDA_FOOTER` fuera del dataset | expresión `null` (no se pinta) |
| `bigint = varchar` / CAST vacío | BIRT bindea ids como text vacío | `CAST(NULLIF(BTRIM(CAST(? AS text)), '') AS bigint)` + `CAST(? AS timestamp)` fecha |
| 500 design=null en Docker local | Convención classpath no ve el bind de `designs/` | `hospital.reports.birt.design.EstadisticaCenso=/app/src/main/resources/designs/EstadisticaCenso.rptdesign` |
| Header logo “resource not reachable” | Image extra `logo_prt_TS.png` hardcode en pie | Pedido: quitar el `<image>` del footer (no es `urlLogo` del cliente) |

Smoke: `target/preview/EstadisticaCenso-birt-docker.pdf` (copy
`CopyEstadisticaCensoOracleToPg.py 654 2026-09-11 80` — **PAMI SDLC** 60 / Total
General 78, grupos OTROS+CARDIOLOGIA, `demo.impresion`).
Listado `[x]` 2026-09-11 (HOSPITAL_2).

### 2026-09-11 · EtiquetaDatosPaciente

SoT **HOSPITAL_2** `EtiquetaDatosPaciente.rptdesign` (no HOS-APP). Etiqueta 8.5×2.5 cm.
Callers: cola espera recepción, internación (enfermería), modelo **ETIQUETA DATOS PACIENTE**
en modificación admisión. Bean solo `idPaciente`. SQL inline + `personas.f_get_persona_full`
(SoT `PERSONAS_BODY.sql` ~779). Sin `urlLogo` (watermark hardcode). Scalars `where_*`
no se usan.

Smoke: `target/preview/EtiquetaDatosPaciente-birt-docker.pdf` (paciente **6700**
**ABREGO, ALFONSINA CELESTE**). Listado `[x]` 2026-09-11 (HOSPITAL_2).

### 2026-09-11 · etiquetaItem

SoT **HOSPITAL_2** `etiquetaItem.rptdesign` (no HOS-APP). Etiqueta 8.5×2.5 cm.
Happy path: tile **Depósito** (`msg.deposito`) → elegir depósito → **Aceptar** →
**Configuraciones** → **Items** → **Armado Kit** (`msg.armado_kit`) → tipo kit /
ítems / movimientos → **Crear kit** (`msg.crear_kit`, `BBArmadoKit.actionBtnConfirmar`).
Bean solo `codItem` (código del kit armado). No es `etiquetaItemA4` (ABM ítem/kit
Imprimir) ni fraccionamiento (print comentado).

SQL inline `ts.item`. Barcode: sidecar sin OnBarcode EAN-13 → `general.f_get_interleaved_2_5`
+ font DANI 2of5 (SoT `GENERAL_BODY.sql` ~1418). Sin `urlLogo` / pie `usuario`.

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Barcode vacío / plugin missing | BIRT 4.24 sin `Barcode` EAN-13 | `text-data` DANI 2of5 + encode SQL |
| PDF vacío | `codItem` no existe en `ts.item` | seed ítem con `cod_barra` (smoke `53175`) |

Smoke: `target/preview/etiquetaItem-birt-docker.pdf` (ítem **53175**
**SOLUC. FISIOLOGICA JAYOR…**). Listado `[x]` 2026-09-11 (HOSPITAL_2).

### 2026-09-11 · etiquetaItemA4

SoT **HOSPITAL_2** `etiquetaItemA4.rptdesign` (no HOS-APP). Hoja A4 con hasta 32
etiquetas (visibility `ctdEtiquetas`). Happy path: tile **Depósito** (`msg.deposito`)
→ elegir depósito → **Aceptar** → **Configuraciones** → **Items** (`msg.items`) →
**Ítem Farmacológico** (`msg.item_farmacologico`) → buscar/seleccionar ítem →
menú **Ítem Farmacia** (`msg.item_farmacia`) → **Imprimir Etiqueta A4**
(`msg.imprimir_etiqueta_a4`, `BBDatosItemFarm.actBtnImprimirEtiqueta('ETIQUETAA4')`)
→ popup **Cantidad Copias** (`msg.ctd_copias`, default 1) → **Aceptar**.
Alterno: mismo árbol → **Kit** (`msg.kit`) → `BBDatosKit.actionBtnImprimirA4`
(`codItem` = `codKit`). **No** es `etiquetaItem` (ticket/Zebra en Armado Kit /
Imprimir Etiqueta).

SQL inline `ts.item`. Barcode: sidecar sin OnBarcode EAN-13 →
`general.f_get_interleaved_2_5` + font DANI 2of5. Sin `urlLogo` / pie `usuario`.
Params: `codItem` + `ctdEtiquetas` (siempre put; páginas de a 32 si copias>32).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Barcode vacío / plugin missing | BIRT 4.24 sin `Barcode` EAN-13 (36 celdas A4) | `text-data` DANI 2of5 + encode SQL; conservar visibility |
| PDF vacío | `codItem` no existe en `ts.item` | mismo seed ítem `53175` que `etiquetaItem` |

Smoke: `target/preview/etiquetaItemA4-birt-docker.pdf` (ítem **53175**,
`ctdEtiquetas=1`). Listado `[x]` 2026-09-11 (HOSPITAL_2).

### 2026-09-11 · EtiquetasMod

SoT **HOSPITAL_2** `EtiquetasMod.rptdesign` (no HOS-APP). Etiqueta 100×80 mm.
Happy path: tile **Recepción** (`msg.recepcion`) → centro → **Reimpresión de Etiquetas**
(`msg.reimpresion_etiquetas`, `/pages/recepcion/reimpresionEtiquetasMod.faces`) →
fecha / paciente / nro orden / servicio → **Consultar** → icono imprimir de fila
(`msg.imprimir_etiquetas`, `BBReimpresionEtiquetasMod.actionBtnImprimirEtiquetaModalidad`).
También se dispara al recepcionar OS si `reqPrtEtiquetas` y printer **SATO**
(`BBRecepcionPaciente`, wrapper `ETIQUETA_MOD`). **No** es Zebra (`PrintEtiquetas`)
ni reimpresión de orden de servicio / cobro caja (no mandan este `.rptdesign`).
Copias = `ctdEtiquetasModalidad` (especialidad o servicio); 0 = no imprime.

SQL: `general.f_get_interleaved_2_5(?)` (legacy `from dual`). Layout DANI 2of5.
Logo: `params["logoServ"]` (BLOB `ts.servicio_centro.logo_etiquetas` → path WAR).
En Cañada / HOSPROD ese BLOB está **vacío** (0 filas). El sidecar no inventa el
BLOB: el JSON manda código cliente (`SDLC_VM`) y el engine resuelve archivo.

Slots `pack_logos` (pack 16 Cañada) — no son intercambiables:

| Slot | Uso HIS | Cañada |
|------|---------|--------|
| `logo_etiquetas` (servicio) | Este reporte | vacío |
| `logo_impresion` → `logo_prt_*` | Impresión A4 / `urlLogo` | PNG **51×46** (pixelado a 21 mm) |
| `logo_impresion_small` → `logo_prt_small_*` | Pie / small | ~50×45 |
| `logo_hc` → `logo_hc_*` | HC | PNG **215×120** (otro lockup; no cabe en 21×21 sin aplastar) |
| `logo_app` / `logo_term_auto_recep` | App / kiosco | SVG |

Para el recuadro cuadrado: `logo_prt_hi_SDLC_VM.png` (upsample Lanczos del print,
mismo recorte). Engine `logoServ`: `logo_prt_hi_` luego `logo_prt_`. Sin pie
`usuario`. `personal` puede ser null (se oculta).

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Logo `file:///SDLC_VM is not accessible` | Bean manda path WAR; JSON manda código cliente | `resolveNamedLogoParam(..., "logoServ")` (mismo patrón `logoReporte`) |
| Logo Cañada pixelado | `logo_prt_SDLC_VM` es 51×46 estirado a 21 mm | `logo_prt_hi_*` para `logoServ`; no sustituir por `logo_hc` (landscape) |
| Línea `Tel.:` vacía con `"teServ": null` | Worker string null/blank → `""`; visibility legacy `== null` | Ocultar también `== ""` en el diseño |

Smoke: `target/preview/EtiquetasMod-birt-docker.pdf` (RADIOLOGIA Cañada,
ABREGU, barcode seed internación, `logoServ=SDLC_VM`). Listado `[x]` 2026-09-11
(HOSPITAL_2).

### 2026-09-11 · FormAltaPac

SoT **HOSPITAL_2** `FormAltaPac.rptdesign` (no HOS-APP). Formulario de alta
(internación o atención amb) con detalle HC.

Happy path internación: ficha internación → menú **Impresión Epicrisis**
(`msg.impresion_epicrisis`, `/pages/internacion/internacion/impresionEpicrisis.faces`)
→ tildar **Imprimir formulario de alta** (`msg.imprimir_formulario_alta`,
`internacion.prtFormularioAltaBoolean`) → confirmar/imprimir epicrisis
(`BBImpresionEpicrisis`). Params: `idFormAltaPac` + `borrador` (0 al confirmar
`getFileReport`; 1 en viewer/merge). ReportManager inyecta `urlLogo` y pie
`usuario`. **No** es `FormHcPac` / `FormHcPacCirugia`.

Mismos puts: observación de guardia (GYE / consultorio) y merge desde
validación de OS (`BBDetalleOrdenServAmb`). Guardar el form en demanda
espontánea **no** imprime este `.rptdesign`.

HOSPROD / RDS: **0** filas `form_alta_pac` y **0** `form_hc` `TIPO_ALTA`
(tampoco hay fila en `tipo_form_hc_df` en el dump Cañada). Smoke: copy
examen físico internación Oracle `id_examen_fisico_pac_int=21` / internación
`3030000066` MOLINA → `form_alta_pac` `9906`
(`tools/CopyFormAltaPacOracleToPg.py`). No es copy de un form de alta real.

SQL inline + `general.f_get_edad_anio` + `historia_clinica.p_get_valor_det_form_col`
(SoT `GENERAL_BODY.sql` / `HISTORIA_CLINICA_BODY.sql`). Watermark
`report_watermark.png` si `borrador==1`.

Smoke: `target/preview/FormAltaPac-birt-docker.pdf`. Listado `[x]` 2026-09-11
(HOSPITAL_2).

### 2026-09-11 · FormHcPac

SoT **HOSPITAL_2** `FormHcPac.rptdesign` (no HOS-APP). Formulario HC de
paciente (enfermería / complementary internado). **No** es `FormAltaPac` ni
`FormHcPacCirugia`.

Happy path internación enfermería: módulo **ENFERMERIA**
(`msg.ENFERMERIA` / `msg.enfermeria_internados`,
`/pages/enfermeria/inicioEnfermeriaPiso.faces`) → centro + sector →
**Aceptar** → piso (`enfermeriaPiso.faces`) → **Consultar** → icono lista
`msg.evaluacion_enfermeria` (abre ficha
`/pages/atencionEnfermeria/sintesisInt.faces`) → menú lateral dinámico
`#{menu.navegadorTipoEnf}` → tabla `FORM_HC` → icono imprimir
(`msg.imprimir`, `BBNavegadorTipoEnfInt.actBtnImprimirFormHcPac`). Solo
`idFormHcPac`. Mismos puts GYE / consultorio navegador.

Variante HC: Historia clínica internado (`eventoInternado.xhtml`) → pestaña
`msg.formularios_complementarios` → imprimir
(`BBEventoHC.imprimirFormularioCompInternacion`) + `impresionHC=S`.

ReportManager inyecta `urlLogo` (visibilidad dirección MATERDEI) y pie
`usuario`. Logo del encabezado = BLOB `pack_logos` vía
`param_historia_clinica` (no path file).

SQL inline + `general.f_get_edad_anio` +
`historia_clinica.p_get_valor_det_form_col` +
`personas.f_get_matricula_full`. Subquery cirugia Oracle
`(id_internacion AND rownum=1 OR id_atencion_amb)` →
`(internacion OR atencion_amb) LIMIT 1`.

BIRT 4.24: binding muerto `fecha_hora_ini_ate_9` (sufijo de unicidad, no
columna JDBC) y nombre `Column Binding` (espacio) abortan el layout → PDF
solo pie. Quitar/renombrar en el migrate. Reiniciar sidecar: el worker
cachea el `.rptdesign`.

Smoke copy Oracle `form_hc_pac=26` internación `3030000063` PRUEBA CRUZ DEL
EJE (`tools/CopyFormHcPacOracleToPg.py`). PDF:
`target/preview/FormHcPac-birt-docker.pdf`. Listado `[x]` 2026-09-11
(HOSPITAL_2).

### 2026-09-14 · FormularioPedidoMedicamentos

SoT **HOSPITAL_2** `FormularioPedidoMedicamentos.rptdesign`. Formulario de
pedido depósito/servicio/área. **No** es `FormularioTransferenciaMedicamentos`
ni `PreparacionPedidoMedicamentos*` ni `necesidadCompra`.

Happy path depósito: tile **DEPOSITO** (`msg` farmacia,
`/pages/farmacia/inicioFarmacia`) → **Pedidos** → **Pedido manual depósito**
(`pedido_manual_deposito`, `/pages/farmacia/pedidoDeposito.faces`) → depósito
→ ítems → **Confirmar pedido** (`msg.confirmar_pedido`) → popup
`msg.desea_imprimir_el_pedido` → **Sí**
(`BBPedidoDeposito.actAceptarImpresion`). Solo `idPedDep`.

Reimpresión: **Entregas** → **Preparación pedidos depo/serv/área**
(`/pages/farmacia/preparacionDepoServ/pedidos.faces`) → seleccionar pedido →
`msg.reimpresion_de_pedido` (`BBPedidos.actBtnReimprimir`). El **Sí** de
preparar imprime `PreparacionPedidoMedicamentos*`, no este diseño.

SQL inline (sin packages). Logo file `params["urlLogoSmall"]`. Copy HOSPROD
`ped_dep` DEPOSITO `662118` / `662122`, AREA `661616`, INTERNADO `662270`
(`tools/CopyFormularioPedidoMedicamentosOracleToPg.py`). PDF:
`target/preview/FormularioPedidoMedicamentos-birt-docker.pdf` (pedido
`662118` Claritromicina).

### 2026-09-14 · FormularioServiciosCentro

SoT **HOSPITAL_2** `FormularioServiciosCentro.rptdesign`. Listado de servicios
del centro. **No** es el Excel de la misma pantalla (`exportarExcel` /
`XLSParser`) ni ABMs hermanos (especialidad, sectores, logo).

Happy path: **Configuración** (`msg.configuracion`) → **Centro Atención**
(`msg.centro_atencion`, `/pages/configuracion/centroAtencion/datosCentroAtencion.faces`)
→ buscar/seleccionar centro → menú lateral **Servicio Centro**
(`msg.servicio_centro`, `servicioPorCentro.faces`) → **Imprimir**
(`msg.imprimir`, `BBServicioPorCentro.actionBtnPrint`). Deshabilitado si la
grilla está vacía.

SQL inline (sin packages). Logo file `params["urlLogoSmall"]`. Smoke PG
`id_centro_ate=654` Cañada (73 filas ya en RDS). PDF:
`target/preview/FormularioServiciosCentro-birt-docker.pdf`.

### 2026-09-14 · FormularioTransferenciaMedicamentos

SoT **HOSPITAL_2** `FormularioTransferenciaMedicamentos.rptdesign`. Vale de
entrega/transferencia `mov_dep`. **No** es `FormularioPedidoMedicamentos` ni
`PreparacionPedidoMedicamentos*`.

Camino feliz reimpresión: tile **DEPOSITO** (`/pages/farmacia/inicioFarmacia`)
→ elegir depósito → **Aceptar** → **Entregas** → **Reimpresión Formulario
Transferencia** (`msg.reimpresion_formulario_transferencia`,
`/pages/farmacia/reimpresionFormularioTransferencia.faces`) → fechas →
**Consultar** → ícono imprimir de la fila
(`BBReimpresionFormularioTransferencia.actionBtnImprimir`). Sidecar: array
`idsMovDep` (legacy armaba `sqlQuery` con `=` / `IN`).

Alta: **Entregas** → **Transferencia directa dep.**
(`transferenciaDeposito.faces`) → ítems → guardar → popup
`msg.desea_imprimir_form_trans` → **Sí**. Otros call sites: servicio/área,
devoluciones, preparación internados/cirugía/amb, confirmación pendiente
(`mostrarDifRecep=S`), `nroPedido` en entregas multi-mov.

SQL inline + `farmacias.f_get_descripcion_det_ped` /
`indicacion_medica.f_get_medico_indica` / `admision.f_get_cama_internacion_actual`.
Copy HOSPROD pedido `617352` / `mov_dep=488674` (01-ene-2026, remito 26982-27992)
(`tools/CopyFormularioTransferenciaMedicamentosOracleToPg.py` + indica/matrícula).
PDF: `target/preview/FormularioTransferenciaMedicamentos-birt-docker.pdf`.
`lpad(nro_pto_vta,4,'0')` en PG exige texto (`::text`); si no, el dataset
muere y el PDF sale con cabecera vacía. Pie `DB_USER` → param `usuario`.

### 2026-09-14 · grpFormEntregaCirugia (sin uso)

SoT **HOSPITAL_2** `grpFormEntregaCirugia.rptdesign`. **0** `printReport`.
Marcado `[x]` en listado principal (pedido explícito; sin sidecar).
HIS entrega insumos: `ParteInsumosQuirurgico` / `ParteInsumosObstetricos`.

### 2026-09-14 · evaluacionPreanestesica (sin uso)

SoT **HOSPITAL_2** `evaluacionPreanestesica.rptdesign`. **0** `printReport`.
Marcado `[x]` en listado principal (pedido explícito; sin sidecar).
HIS parte anestesia: `parteAnest.rptdesign`.

### 2026-09-14 · eventosTrazablesPaciente

SoT **HOSPITAL_2** `eventosTrazablesPaciente.rptdesign`. Caller único
`BBConsultaItemTrazablePaciente.actBtnImprimir` (`consultaItemTrazablePaciente.faces`,
`msg.consulta_item_paciente`). Params: `apellidoPaciente`, `nombrePaciente`,
`fechaDesde`, `fechaHasta` + `urlLogo` ReportManager. Pie `DB_USER` → `usuario`.
SQL inline (dataset mal etiquetado SP). Oracle HOSPROD y PG tenían **0** filas
de `evento_item_trazable`. Usuario autorizó **seed inventado** (no paridad):
`evento_trazable_df`/`item_trazable`/`evento_item_trazable` 990001–990002,
paciente `SMOKETRAZA`/`PACIENTEUNO`, ítem `28466` con `ts.item` smoke.
Tool `Hospital-Reports/tools/SeedEventosTrazablesPacienteSmoke.py`
(`session_replication_role=replica` porque `sub_tipo_item` está vacío en PoC).
Query: no `t.*` (orden físico PG ≠ Oracle; choca con cachedMetaData / `cod_gs1`).
`CAST(? AS timestamp)` en filtros fecha. Bindings en minúsculas.
Listado **`[x]`**.

### 2026-09-15 · IndicacionesAmbA4

SoT **HOSPITAL_2** `IndicacionesAmbA4.rptdesign`. Happy path: atención ambulatoria
abierta → **Imprimir** (`msg.imprimir`) → diálogo `msg.impresion` → fila
`msg.indicaciones` (visible si `tipoPrtReceta ≠ A4_UNIFICADA`) → ícono imprimir
(`BBImpresionAmbulatoria.imprimirIndicaciones`). Param negocio: `idAtencionAmb`.
SQL inline ×7. Callables: `general.f_get_edad_anio`, `general.blob_to_clob`.
`urlLogo` file (ReportManager). Pie `DB_USER` → `usuario`.
`diagnostico_sec_pac_amb` Oracle no existe en PG migrado → `ts.diagnostico_sec_pac`
(mismo corte DietaAmb). `ctd_prescrip as ctd_indicada`. `rownum` matrícula → `LIMIT 1`.

No es este reporte: `IndicacionesAmbA5` / `TICKET` / `A4_UNIFICADA` /
`A4UNIONPERSONAL`; ticket matricial (`PrintComandos.prtTicketIndicacionesAmb`);
dietas (`DietaAmb`); receta (`RecetaPac`).

Smoke: copy HOSPROD `1357265` JUAREZ NORMA ELISA
(`tools/CopyIndicacionesAmbA4OracleToPg.py`; 5 recetas con `indicacion`, 1
tratamiento, prácticas con `indicaciones`). PDF:
`target/preview/IndicacionesAmbA4-birt-docker.pdf` (datos RDS PoC `ts`).
Listado `[x]` 2026-09-15 (HOSPITAL_2).

### 2026-09-15 · IndicacionesAmbA4_UNIFICADA

SoT **HOSPITAL_2** `IndicacionesAmbA4_UNIFICADA.rptdesign`. Concatena
`IndicacionesAmb` + `Utils.getTipoPrtReceta` → este archivo si el servicio
tiene `tipoPrtReceta=A4_UNIFICADA`. Param negocio: `idAtencionAmb`.
SQL inline ×7 (mismos datasets que `IndicacionesAmbA4`). Callables:
`general.f_get_edad_anio`, `general.blob_to_clob`. `urlLogo` file
(ReportManager). Pie `DB_USER` → `usuario`.
`diagnostico_sec_pac_amb` → `ts.diagnostico_sec_pac`. `ctd_prescrip as ctd_indicada`.
`rownum` matrícula → `LIMIT 1`. Join receta por `nro_receta_pac` (PG).

Happy path Guardia: tile **GUARDIA Y EMERGENCIAS** (`msg.GUARDIA_Y_EMERGENCIAS`)
→ atención DE abierta → **Imprimir/Enviar** (`msg.imprimir_enviar`,
`BBDemandaEspontaneaGYE.actionBtnImprimirPanel`) → diálogo → fila
`msg.indicaciones` (visible también con UNIFICADA) → ícono imprimir
(`BBImpresionDemandaEspontaneaGYE.imprimirIndicaciones`). Condición:
`param_atencion_serv.tipo_prt_receta = A4_UNIFICADA` (Configuración →
Servicio Centro / Ambiente ambulatorio).

Variantes: Guardia Consultorio (`BBImpresionDemandaEspontaneaConsultorio`);
DE ambulatoria (`BBImpresionDemandaEspontanea`); Oftalmología
(`BBImpresionOftalmologia`). Mail GYE **no** usa este diseño: si tipo es
UNIFICADA, `addMailIndicaciones` fuerza `IndicacionesAmbA4`.

**No** sale del diálogo de atención ambulatoria programada
(`impresionAmbulatoria.xhtml`): la fila Indicaciones está
`rendered=tipoPrtReceta ne A4_UNIFICADA`. Ahí el diseño hermano es
`IndicacionesAmbA4` / `A5` / ticket matricial.

No es este reporte: `IndicacionesAmbA4` (tipo ≠ UNIFICADA), `A5`, `TICKET`,
`A4UNIONPERSONAL`, `RecetaAmbA4_UNIFICADA` / `EstudioAmbA4_UNIFICADA`,
`DietaAmb`, `RecetaPac`.

Smoke: mismo copy HOSPROD `1357265` JUAREZ NORMA ELISA
(`tools/CopyIndicacionesAmbA4OracleToPg.py`). PDF:
`target/preview/IndicacionesAmbA4_UNIFICADA-birt-docker.pdf`
(HTTP 200, 6 páginas, ~110 KB; pie `demo.impresion`, no `postgres`).

### 2026-09-15 · IndicacionesAmbA4UNIONPERSONAL

**N/A otro cliente (2026-09-21).** `esClienteUP`. Queda en sidecar por el
corte ya cerrado; no abrir hermanos UNIONPERSONAL. Inventario:
[`listado-reportes-birt-otro-cliente.md`](../relevamiento/listado-reportes-birt-otro-cliente.md).

SoT **HOSPITAL_2** `IndicacionesAmbA4UNIONPERSONAL.rptdesign`. Misma concatenación
`IndicacionesAmb` + `tipoPrtReceta` cuando el valor persistido es
`A4UNIONPERSONAL`. El combo vigente de **tipo impresora receta**
(`paramAtencionServ.xhtml` / `ambienteAmbulatorio.xhtml`) **no** ofrece esa
opción (A4 / A4_UNIFICADA / TICKET / MATRICIAL); el HIS Unión Personal u
otro seed puede dejar el string en `param_atencion_serv` / ambiente.

Happy path (si el tipo está seteado): igual que `IndicacionesAmbA4` —
atención abierta → **Imprimir** (`msg.imprimir`) → diálogo `msg.impresion`
→ fila `msg.indicaciones` → ícono imprimir (`BBImpresionAmbulatoria.imprimirIndicaciones`
u homólogos GYE/Consultorio/DE/Oftalmología). Param bean: solo
`idAtencionAmb`. Layout **sin** imagen `urlLogo` y **sin** pie usuario
(página ~115×185 mm; footer diagnóstico).

SQL inline ×7. Callables `general.f_get_edad_anio` / `blob_to_clob`.
`diagnostico_sec_pac_amb` → `ts.diagnostico_sec_pac`. Medicación: `decode`
ítem/genérico → `CASE`; `ctd_prescrip as ctd_indicada`. ATENCION sin
matrícula/especialidad (así el SoT).

No es: `IndicacionesAmbA4`, `A4_UNIFICADA`, `A5`, `TICKET`,
`RecetaAmbA4_UNIFICADA`.

Smoke: grafo `1357265` JUAREZ. PDF:
`target/preview/IndicacionesAmbA4UNIONPERSONAL-birt-docker.pdf`
(HTTP 200, 11 páginas ~78 KB, página compacta 115×185 mm).

### 2026-09-15 · IndicacionesAmbA5

SoT **HOSPITAL_2** `IndicacionesAmbA5.rptdesign`. Concatena `IndicacionesAmb` +
`tipoPrtReceta=A5`. El combo de ambiente/param receta **no** lista A5
(A4 / A4_UNIFICADA / TICKET / MATRICIAL); `msg.A5` aparece en depósito
(`datosDeposito.xhtml`), no en tipo impresora receta. El diseño se dispara
si el string `A5` quedó en ambiente/param.

Happy path (si tipo=A5): atención abierta → **Imprimir** → diálogo
`msg.impresion` → **Indicaciones** → ícono
(`BBImpresionAmbulatoria.imprimirIndicaciones` y homólogos Guardia/DE/Oftalmología).

Params: bean `idAtencionAmb`; ReportManager `urlLogo` + `urlLogoSmall`
(layout file `params["urlLogoSmall"]`). Página ~115×185 mm. Footer
diagnóstico + dataset `MAT_PERSONAL` (`personas.f_get_matricula_full` /
`f_get_especialidad_full`, SoT `PERSONAS_BODY.sql`). SQL inline ×8.

No es: `IndicacionesAmbA4` / UNIFICADA / UNIONPERSONAL / TICKET / receta.

Smoke: grafo `1357265` JUAREZ. PDF:
`target/preview/IndicacionesAmbA5-birt-docker.pdf`
(HTTP 200, 10 páginas 115×185 mm; footer BROGGI / M.P. 40321).
BIRT 4.24: SoT traía `type=a4`+`landscape` y a la vez `width/height` 115×185;
el worker emitía A4 apaisado (297×210) y el cuerpo quedaba a media hoja.
Fix: `type=custom` (como UNIONPERSONAL) y quitar `width=45%` pensado para
ese A4.

### 2026-09-15 · IndicacionesAmbTICKET

SoT **HOSPITAL_2** `IndicacionesAmbTICKET.rptdesign`. Concatena
`IndicacionesAmb` + `tipoPrtReceta=TICKET`. **MATRICIAL** no usa este archivo:
`PrintComandos.prtTicketIndicacionesAmb`. Combo ambiente sí lista TICKET.

Happy path: Centro **SANATORIO DE LA CAÑADA** → consultorio con
**Tipo Impresora Receta = TICKET** → atención con indicaciones → **Imprimir**
→ diálogo **Impresión** → **Indicaciones** (oculta si param servicio =
`A4_UNIFICADA`). Beans: `BBImpresionAmbulatoria.imprimirIndicaciones` y
homólogos DE/GYE/Consultorio/Oftalmología/TF/Domiciliaria. Mail DE/GYE
también concatena TICKET.

Params: bean `idAtencionAmb`; ReportManager `urlLogo` + `urlLogoSmall`
(layout file small; highlight `urlLogo` GEA/MATERDEI). Página custom
80×297 mm. SQL inline ×4 (`ATENCION` / medicación / tratamientos /
prácticas). Callable `general.f_get_edad_anio` (SoT `GENERAL_BODY.sql`).
No pie `usuario`.

No es: `IndicacionesAmbA4` / UNIFICADA / UNIONPERSONAL / A5; ticket
matricial ESC/POS; receta.

Smoke: grafo `1357265` JUAREZ. PDF:
`target/preview/IndicacionesAmbTICKET-birt-docker.pdf`
(HTTP 200, 1 página 80×297 mm, ~21 KB; REGULANE / DIETA).

### 2026-09-15 · IndicacionesVigentesNutricion

SoT **HOSPITAL_2** `IndicacionesVigentesNutricion.rptdesign`. Un solo
`printReport` (`BBIndicacionesVigentes.actionBtnImprimir`). Menú
`NUTRICION` → `nutricion` → `indicaciones_vigentes`
(`/pages/nutricion/indicacionesVigentes`). Bean: `idCentroAte`,
`idSectorInt`/`idServicioInt` opcionales, `centroAtencion`, `fecha`.
Diseño **no** declara `idServicioInt` (param unbound `AN_ID_SERVICIO`).
ReportManager: `urlLogo`. Pie: `usuario` (no `DB_USER`).

Callables: `censo.pp_get_internacion_cama` (print-path, 11 IN, SoT
`CENSO_BODY.sql` ~2046) como `SELECT * FROM …` (excepción BIRT+JDBC PG;
`RETURNS TABLE` en orden `cachedMetaData` 157 cols).
`indicacion_medica.f_get_indicacion_ajustada` (SoT
`INDICACION_MEDICA_BODY.sql` ~2829) en dataset `DET_INDICA_NUTRI` (no
bind del cuerpo; plan viene del censo). **Diferido:** prácticas, camas
libres, aislamiento. RDS Cañada `654` / `2026-09-15`: internaciones sí,
`det_indica_nutricion_int` 0 — no inventar dietas.

Smoke: `target/preview/IndicacionesVigentesNutricion-birt-docker.pdf`
(HTTP 200, 3 págs, ~50 KB; ABREGU / CARDIOLOGIA / SIN ASIGNAR).

### 2026-09-15 · InformeIntPac

SoT **HOSPITAL_2** `InformeIntPac.rptdesign`. Param `idDetAtencionAmb` =
`id_det_atencion_int`. Pie `usuario`. Logo file `urlLogo`. Callable
`historia_clinica.p_get_form_hc_pac_string_int` (SoT
`HISTORIA_CLINICA_BODY.sql` ~3562) como `SELECT * FROM …`.

PDF vacío (labels, HTTP 200): el dataset ATENCION del diseño **copió SQL de
ambulatorio**. Oracle y PG `atencion_int` tienen el **mismo** set de columnas
(~44); `fecha_hora_*_enf`, `diag_principal`, destino/tipo alta,
`motivo_atencion` viven en `atencion_amb`; `nro_afiliado` en
`ord_serv_amb` / `ts.internacion`, no en `ord_serv_int`. El abort JDBC
(`autoCommit=false`) tumba PRACTICA/HTML. Port: timestamps `*_med`;
`nro_afiliado` vía `ts.internacion`; `CAST(NULL AS text)` en campos
solo-amb; 2º alias `fecha_hora_ini_ate_1` + `cachedMetaData`. Copy HOSPROD
det `2137582` BRUHN — `det_form_hc_pac` 0 (cuerpo HTML vacío = dato, no
layout). No es `HTMLDocument.rptdesign` (tipo HTML puro).
Lección: contrastar DDL de **la** tabla del corte (`int` vs `amb`); no
asumir “faltan columnas en PG”.

Smoke: `target/preview/InformeIntPac-birt-docker.pdf` (HTTP 200, BRUHN /
TOMOGRAFIA / PAMI SDLC; cuerpo form HTML vacío = 0 `det_form_hc_pac`).
Confirmado listado [x] InformeIntPac (HOSPITAL_2).

### 2026-09-15 · InformePac

SoT **HOSPITAL_2** `InformePac.rptdesign`. Param `idDetAtencionAmb` =
`id_det_atencion_amb`. Pie `usuario`. Logo file `urlLogo`. Callable
`historia_clinica.p_get_form_hc_pac_string` (SoT
`HISTORIA_CLINICA_BODY.sql` ~3554 / f_ ~3340) como `SELECT * FROM …`.

Camino feliz: Ambulatoria → atención → `#{msg.realizacion_de_practicas}`
(`practicas.faces`) → ojo `#{msg.ver_informe}` cuando
`formatoInformeEstudio` = FORM_HC (no HTMLDocument; no InformeIntPac
de observación internado; no blob de `imprimirInformesConfirmados`).

SQL ATENCION = `ts.atencion_amb` (sí tiene `*_enf` / `diag_principal` /
destino-tipo alta). Alias duplicado `fecha_hora_ini_ate` →
`fecha_hora_ini_ate_1`. `nro_afiliado` vía `ord_serv_amb` `LIMIT 1`.
Copy HOSPROD det `3714882` GAROFALO / informe `1889714` / 7
`det_form_hc_pac`.

Smoke: `target/preview/InformePac-birt-docker.pdf` (HTTP 200, ~61 KB,
GAROFALO / 7 det_form). Confirmado listado [x] InformePac (HOSPITAL_2).

### 2026-09-15 · IngresosEntreFechas

SoT **HOSPITAL_2** `IngresosEntreFechas.rptdesign`. Caller único
`BBIngresosEntreFechas.imprimir` (HOS-APP 0 hits). Pie `usuario`.
Logo file `urlLogo`. Callable
`admision.p_get_ingresos_entre_fechas` (SoT `ADMISION_BODY.sql`
`p_` ~1690 / `f_get_ingresos_entre_fechas` ~1073) como
`SELECT * FROM …` (excepción BIRT+JDBC PG; `RETURNS SETOF
ts.tmp_altas_entre_fechas`). GTT: `DELETE` al inicio. `NULLIF` 0 =
TODOS. `GOTO` Oracle → `CONTINUE`.

Print **siempre** `tipoAdmision=HOSPITALARIA` (filtra
`tipo_internacion` de la **primera cama**, no
`internacion.tipo_admision`). La grilla `actBtnConsultar` pasa NULL
en tipo. Excel `generarReporteExcel` no es este PDF.
Dataset también bindea `paciente` (print no hace `put` → JSON null).

Camino: tile Admisión internados (MENU_APLICACION) → consultas →
`#{msg.consulta_ingresos_entre_fechas}`
(`/pages/admisionInternados/consultasAdmision/ingresosEntreFechas`) →
filtros + `#{msg.consultar}` → `#{msg.imprimir}`.

Copy HOSPROD `tools/CopyIngresosEntreFechasOracleToPg.py` centro `654`
2026-06-01..03: 172 internaciones, 271 `cama_internacion`, 19 primera
cama `tipo_internacion=HOSPITALARIA` (p.ej. `8020023355` CAMPOS).
Oracle no tiene `TS.CAMA` / `TS.SECTOR_INT`; no se copian.

Smoke: `target/preview/IngresosEntreFechas-birt-docker.pdf` (HTTP 200,
~24 KB; CAMPOS / HERRERA NAVARRO / UTI). `odaAutoCommit=true` + binds
`?` en posición 1–10 (el SP Oracle dejaba el OUT cursor en pos. 1).
Confirmado listado [x] IngresosEntreFechas (HOSPITAL_2).

### 2026-09-16 · Interrogatorio (sin uso)

SoT **HOSPITAL_2** `Interrogatorio.rptdesign`. **0** `printReport`.
Marcado `[x]` en listado principal (pedido explícito; sin sidecar).
HIS: `printFile` de `doc_adi_adm` (`imprimir_interrogatorio`). Config
puede disparar `ConsentimientoRecepcion.rptdesign`. Form HC:
`FormHcPac.rptdesign`.

### 2026-09-16 · ItemTrazable

SoT **HOSPITAL_2** `ItemTrazable.rptdesign`. Caller único
`BBConsultaItemTrazable.actBtnImprimirComprobante` (`consultaItemTrazable.faces`,
`msg.consulta_item_trazable`). Param `idItemTrazable` + `urlLogo` file
ReportManager. Pie `DB_USER` → `usuario`. SQL inline. Callable
`farmacias.f_get_err_ult_evento_traza` (SoT `FARMACIAS_BODY.sql` ~13825).
Dataset EVENTO era `SPSelectDataSet` con SELECT → `JdbcSelectDataSet`.
No `t.*` (orden PG ≠ Oracle; `nro_det_ord_compra`/`nro_det_recepcion_compra`
alias a `id_det_*` del cachedMetaData). Binding layout `realizo_informe`
(columna SQL `realizo_informe_anmat`) — alias o BIRT 4.24 mata la table.
URI logo: `params["urlLogo"]` (el sidecar ya resuelve file; concatenar
`logo_prt_` + path absoluto duplica y pierde el PNG). `lpad(::text)` comprobante.
No es `RemitoR` / `CodBarraItemFraccionado` / `eventosTrazablesPaciente`
(`consultaItemTrazablePaciente`) / `DispensacionItemTrazable`.

HOSPROD `item_trazable` / `evento_item_trazable` = 0. Smoke = grafo
ya autorizado `990001` (`SeedEventosTrazablesPacienteSmoke.py`).

Smoke: `target/preview/ItemTrazable-birt-docker.pdf`.
Confirmado listado [x] ItemTrazable (HOSPITAL_2).

### 2026-09-16 · LibroInternacion

SoT **HOSPITAL_2** `LibroInternacion.rptdesign`. Callers:
`BBLibroInternacion.actBtnPrintLibroConfirmado` (`libroInternacion.faces`,
`msg.libro_internacion`) — params `mes`, `ano`, `idCentroAte`,
`imprimirCabecera` S/N. `BBReimpresionLibroInternacion` imprime el mismo
diseño pero pone `borrador`/`idLibroIntCentro`/`nro_pagina_inicial`/`fechaHasta`
que el SQL no lee (mismatch legacy). `urlLogo` file ReportManager. Pie
`DB_USER` → `usuario`. SQL inline internaciones del mes +
`general.f_get_edad_anio` (SoT `GENERAL_BODY.sql`). `beforeFactory` oculta
cabecera si `imprimirCabecera=N`.

No es `consulta_libro_internacion` (`DetLibroInternacion.rptdesign`) ni
`genera_confirma_libro_internacion` (genera el libro, no este PDF).

HOSPROD: tipos `S` (obstétrica/pediátrica 6/8/9/11) sin `tipo_int_centro`;
internaciones reales usan tipos 1/2/3/5 con `N`. Smoke autorizado:
`tools/SeedLibroInternacionSmoke.py` pone `S` en esos 4 tipos en PG (no
paridad). JSON `654` / mes `6` / año `2026` / `imprimirCabecera=S`.
Smoke: `target/preview/LibroInternacion-birt-docker.pdf`.
Confirmado listado [x] LibroInternacion (HOSPITAL_2).

### 2026-09-16 · listadoPersonalEspecialidad

SoT **HOSPITAL_2** `listadoPersonalEspecialidad.rptdesign`. Caller único
`BBConsultaPersonalEspecialidad.generarReportePDFAction`
(`consultaPersonalEspecialidad.faces`, menú Origin
`personal_especialidad` / `msg.lista_personal_por_especialidad`).
Params `idEspecialidad` + `especialidad` (rótulo). Layout **sin** imagen
file → omitir `urlLogo`. Pie `DB_USER` → `usuario`. SQL inline persona +
EXISTS `personal` / `especialidad_pers`. `CAST(? AS numeric)`.
El mismo xhtml **Exportar Excel** es POI (`XLSParser`), no este diseño.
No es `listadoPersonalPorServicio` / personal por estado / provincia.

Smoke PG: especialidad `3` CLINICA MEDICA (ALMADA, CHABAN).
Smoke: `target/preview/listadoPersonalEspecialidad-birt-docker.pdf`.
Confirmado listado [x] listadoPersonalEspecialidad (HOSPITAL_2).

### 2026-09-16 · listadoPersonalPorServicio

SoT **HOSPITAL_2** `listadoPersonalPorServicio.rptdesign`. Caller único
`BBConsultaPersonalPorServicio.generarReportePDFAction`
(`consultaPersonalPorServicio.faces`, menú Origin `personal_servicio2` /
`msg.consulta_personal_servicio`). Params `idCentro`/`idServicio` (null UI
→ **0** TODOS), rótulos `centro`/`servicio` (`<TODOS>` si combo vacío),
`apellidoRazonSocial` (`%` si vacío). Layout **sin** logo file. Pie
`DB_USER` → `usuario`. SQL `or ? = 0` (paridad HIS TODOS; coerce 0 del
worker encaja). Excel del mismo xhtml es POI. Volver del bean va a
`liquidacionHonorario/inicio.faces`. No es `listadoPersonalEspecialidad`
ni `consultaPersonalServicio` de liquidación de honorarios.

Copy: `CopyListadoPersonalPorServicioOracleToPg.py` (20 filas 654/14).
Smoke: `target/preview/listadoPersonalPorServicio-birt-docker.pdf`.
Confirmado listado [x] listadoPersonalPorServicio (HOSPITAL_2).

### 2026-09-16 · listadoPersonalProvinciaLocalidad

SoT **HOSPITAL_2** `listadoPersonalProvinciaLocalidad.rptdesign`. Caller único
`BBConsultaPersonalProvinciaLocalidad.generarReportePDFAction`
(`consultaPersonalProvinciaLocalidad.faces`, menú Origin
`personal_provincia_localidad` / `msg.lista_personal_por_prov_loc`).
Params `idProvincia`/`idLocalidad` + rótulos (la UI exige localidad para
buscar). Layout **sin** logo. Pie `DB_USER` → `usuario`. SQL último
domicilio PARTICULAR por `fecha_last_update`. Excel POI. No es especialidad
ni servicio. Default diseño loc `1` no existe en Córdoba HOSPROD (usar 1554).

Copy: `CopyListadoPersonalProvinciaLocalidadOracleToPg.py` (20 filas).
Smoke: `target/preview/listadoPersonalProvinciaLocalidad-birt-docker.pdf`.
Confirmado listado [x] listadoPersonalProvinciaLocalidad (HOSPITAL_2).

### 2026-09-16 · ListadoRemitentes

SoT **HOSPITAL_2** `ListadoRemitentes.rptdesign`. Caller único
`BBListadoRemitentes.actionBtnImprimir` (`listadoRemitentes.faces`, menú
Origin `listado_remitentes` id=85708 bajo LABORATORIO / consultas,
`msg.listado_remitentes`). Excel del mismo xhtml es POI, no BIRT.
`urlLogo` file ReportManager. Pie `DB_USER` → `usuario`.

Print SP `TS.LABORATORIO.p_listado_remitentes` (SoT `LABORATORIO_BODY.sql`
~21220 → `f_listado_remitentes` ~21253). Port PG
`laboratorio.p_listado_remitentes` SETOF `ts.tmp_listado_remitentes`.
Filtro Oracle: rango de `fecha_hora_ord_lab`, `id_laboratorio_externo`
NOT NULL, dets `CONFIRMADA` y `impreso_listado_remitentes` = flag.
`fecha_carga*` y flags `as_imp_*` no se usan en el body. Bean print
pone `codOriLabHasta=codOriLabDesde` (mismatch UI). `horaDesde`/`horaHasta`
son scalars del diseño y se usan en la **búsqueda** Hibernate, no en el
SP de impresión. Smoke `marcarImpreso=N` para no mutar dets.

HOSPROD: `ord_lab_pac` 683730 filas, **0** con `id_laboratorio_externo`
NOT NULL. Smoke autorizado (opción 1): `tools/SeedListadoRemitentesSmoke.py`
inventa grafo `990010` SMOKEREMIT / `[SMOKE] PANEL REMITENTES` /
GLUCOSA+UREA (no paridad). JSON `2026-06-04` / `estado=TODAS` /
`marcarImpreso=N` / `urlLogo=SDLC_VM`.
Smoke: `target/preview/ListadoRemitentes-birt-docker.pdf`.
Confirmado listado [x] ListadoRemitentes (HOSPITAL_2).

### 2026-09-16 · LocDispositivo

SoT **HOSPITAL_2** `LocDispositivo.rptdesign`. Caller único
`BBNavegadorTipoEnfInt.actBtnExportarPDF` (`navegadorTipoEnf.xhtml`,
fragmento `tipoForm=LOCALIZACION_DISPOSITIVO`, `msg.exportar_pdf`).
Camino enfermería: ENFERMERIA → piso → ficha internado
`sintesisInt.faces` → menú lateral `#{menu.navegadorTipoEnf}` → bloque
localización de dispositivos. Excel del mismo bloque es POI. No es
`consulta_dispositivos` de infectología.

Params `idPaciente`/`idInternacion`/`fechaDesde`/`fechaHasta`. Layout
imagen file **urlLogoSmall**; ReportManager también inyecta `urlLogo`.
Pie `DB_USER` → `usuario`. SQL inline persona +
`loc_dispositivo_pac_int` + `general.f_get_edad_anio` +
`personas.f_get_persona_full`. `trunc`→`CAST AS date`.

Copy: `CopyLocDispositivoOracleToPg.py` internación `7030000015` SILVA
(19 filas 2024-02-29..03-18).
Smoke: `target/preview/LocDispositivo-birt-docker.pdf`.
Confirmado listado [x] LocDispositivo (HOSPITAL_2).

### 2026-09-16 · LoteDescarteMuestras

SoT **HOSPITAL_2** `LoteDescarteMuestras.rptdesign`. Caller único
`BBLoteDescarteMuestra.confirmar` → `actBtnImprimirLote`
(`loteDescarteMuestra.xhtml`, `msg.confirmar_lote`). Camino laboratorio:
LABORATORIO → trazabilidad de muestras → `msg.lote_descarte_muestra`.
No hay botón Imprimir aparte; no hay Excel BIRT. No es lote de
derivación / envío / recepción / contenedor / archivado.

Params: `idLoteEnvioMuestras` + ReportManager `urlLogo` (layout file,
visibilidad MATERDEI). Pie `DB_USER` → `usuario`. Dataset cabecera
inline `lote_envio_muestras` + `general.f_get_interleaved_2_5`.
Muestras: `LABORATORIO.p_muestras_lote_envio` /
`p_muestras_pendientes_lote` (SoT `LABORATORIO_BODY.sql` ~18829 /
~18885 via `pp_completar_tmp_muestra_pac` ~6661). BIRT+JDBC PG:
`SELECT * FROM laboratorio.p_*`. `chr(numeric)` → `::int`. Concat
análisis `COALESCE` (Oracle `||` null-as-empty).

Copy: `CopyLoteDescarteMuestrasOracleToPg.py` lote `5` (PRUEBA, SDLC;
HOSPROD 0 DESCARTE). Smoke:
`target/preview/LoteDescarteMuestras-birt-docker.pdf`.
Confirmado listado [x] LoteDescarteMuestras (HOSPITAL_2).

### 2026-09-16 · LoteEnvioMuestras

SoT **HOSPITAL_2** `LoteEnvioMuestras.rptdesign`. Camino feliz
`BBLoteDerivaMuestra.confirmar` → `actBtnImprimirLote`
(`loteDerivaMuestra.xhtml`, `msg.confirmar_lote`, título
`msg.lote_deriva_muestra`). Variantes: consulta
(`consultaLoteDerivaMuestra.xhtml` botón imprimir por fila),
`BBLoteDerivacionAnalisisLab`, `BBLoteRecepcionResultadosLabDeriva`.
No es LoteDescarteMuestras. No Excel BIRT.

Params: `idLoteEnvioMuestras` + `urlLogo` file. Pie `usuario`.
Mismos callables que Descarte; el diseño pasa `as_enmascarar` default
`N`. Copy lote `5`. Smoke:
`target/preview/LoteEnvioMuestras-birt-docker.pdf`.
Confirmado listado [x] LoteEnvioMuestras (HOSPITAL_2).

### 2026-09-18 · MedicamentoAConsignacion

SoT **HOSPITAL_2** `MedicamentoAConsignacion.rptdesign`. Caller único
`BBConsignacionItem.actionBtnPrintConsignacion` (`consignacionItem.xhtml`,
popup post-guardar `msg.imprimir`). Menú Origin DEPOSITO
(`inicioFarmacia`) → Ingreso/Egreso Externo → Consignación →
`item_consignacion` / `msg.item_consignacion` (Ítem en Consignación).
No es reposición ni devolución de consigna. No Excel BIRT.

Params: `idPrestamoConsignaItem` + `urlLogo` file ReportManager. Pie
`DB_USER` → `usuario`. SQL inline cabecera `prestamo_consigna_item`;
detalle `mov_stock` `(+)` → `LEFT JOIN ts.item_trazable` + lookup
`item` + subqueries `evento_item_trazable`. `CAST(? AS numeric)`.
HOSPROD: 19 filas, todas `tipo_prestamo_consigna=PROVISION_EXTERNA`,
0 `id_item_trazable`. Copy `19` (3 mov `100044112` AMINOXIDIN).
Smoke: `target/preview/MedicamentoAConsignacion-birt-docker.pdf`.

### 2026-09-18 · Monitoreos

SoT **HOSPITAL_2** `Monitoreos.rptdesign`. Camino feliz internación:
tile internación → paciente internado → menú lateral `msg.monitoreos`
(`menuLateralInternacion.xhtml` → `monitoreos.faces`) →
`monitoreosBody.xhtml` `msg.imprimir` si `tieneMonitoreo`. Bean
`BBMonitoreos.actBtnImprimirMonitoreos` (`idInternacion`, `imprimeEnf=N`).
No es el grid de registros de enfermería del día (`imprimeEnf=S` +
`fechaMonitoreo`) ni `RegistroMonitoreos.rptdesign` del navegador de
tipo enf. ZIP HC/lote facturación reusa el mismo diseño.

Callable `TS.ATENCION.p_get_monitoreos_pac` SoT `ATENCION_BODY.sql`
~16820/`~17047`. Port `atencion.p_get_monitoreos_pac` SETOF
`ts.tmp_monitoreo_pac`; diseño `SELECT * FROM` (excepción BIRT+JDBC PG).
`0`/NULL de `toDecimal` → sentinel `-1`. Pie `DB_USER` → `usuario`.
`urlLogo` file (visibilidad MATERDEI). HC amb typo `idAtencioAmb` es
legacy. HOSPROD 0 `monitoreo_pac` (1 `form_monitoreo`). Smoke **inventado**
`monitoreo_pac=990015` sobre internación `7030000015` SILVA (3 columnas ×
6 params). `target/preview/Monitoreos-birt-docker.pdf`.

### 2026-09-21 · MuestraAnatoPatologica

SoT **HOSPITAL_2** `MuestraAnatoPatologica.rptdesign`. Callers:

1. Intra/postoperatorio: menú CENTRO_PROCEDIMIENTO → `quirofano` /
   `postoperatoria` → cirugía en sesión → menú lateral
   `msg.toma_de_muestras` → icono `msg.imprimir`
   (`BBMuestraAnatoPatologica.actBtnImprimir`). Pone `idQuirofano` de
   `TmpCirugia` (no es columna de `ts.cirugia`; el diseño no lo usa).
2. Consulta: tile CIRUGIA → consultas → `toma_de_muestras` id=96314
   (`consultaTomaMuestras.faces`) → filtrar → seleccionar filas →
   `msg.imprimir` (`BBConsultaTomaMuestras.actBtnImprimir`,
   `idQuirofano=null`).

No es `msg.imprimir_etiquetas` (Zebra `PrintEtiquetas`, no BIRT).

SQL inline (sin SP de negocio). `urlLogo` file ReportManager (oculto si
MATERDEI). `nombreCliente` visibilidad CEMIC (columna `nro_hc_anterior`
no está en el SELECT — vacío en legacy). Pie `DB_USER` → `usuario`.
Barcode DANI 2of5 + `general.f_get_interleaved_2_5`. Copy `12439`
OJEDA / cirugia `83485` / Cañada `654`. Smoke:
`target/preview/MuestraAnatoPatologica-birt-docker.pdf`.
Confirmado listado [x] MuestraAnatoPatologica (HOSPITAL_2).

### 2026-09-21 · RecetaPsicotropico

SoT **HOSPITAL_2** `RecetaPsicotropico.rptdesign`. Ticket 80×100 mm
estupefaciente/psicotropico. No es RecetaPac / Duplicada / Oncologica /
`msg.imprimir_indicaciones` (otro diseño).

Callers: `imprimirReceta` en internación
(`BBIndicacionesInternacion` / `BBIndicaPrestIntInternacion` popup
`msg.impresiones` + check `msg.receta` si `reqDupRecetaPrescribe`),
guardia observación (GYE + consultorio), y editor
`/pages/indicaciones/indicaciones.xhtml` (`BBIndicaciones`, ícono
`msg.receta`). Cirugía intra/post redirige al mismo editor.

Params bean: `paciente`, `monodroga`, `dosis`, `idInternacion`.
ReportManager: `urlLogo` (highlight MATERDEI/GEA) + `urlLogoSmall` file.
Sin pie `usuario`. SQL HAB: `admision.f_get_cama_internacion_actual`
(SoT ADMISION_BODY ~3946; ya portado). Internación sin cama vigente →
HAB vacío (paridad).

Smoke: internación PG `8030021838` RIOS / ANEXO 2; `METILFENIDATO` maestro
Oracle `req_dup_receta_prescribe=S`. PDF
`target/preview/RecetaPsicotropico-birt-docker.pdf`.
Confirmado listado [x] RecetaPsicotropico (HOSPITAL_2).

### 2026-09-22 · ConsultaPacientesAtendidos

SoT **HOSPITAL_2** `ConsultaPacientesAtendidos.rptdesign`. Único
`printReport` de ese basename: DxI
`BBConsultaAtencionImag.actionBtnImprimir`. Tile
DIAGNOSTICO_POR_IMAGENES → centro/servicio → lista espera (única o
sectorizada) → `msg.pacientes_atendidos` (login personal) →
`consultaAtencionesAmbulatoria` → `msg.consultar` + `msg.imprimir`.
Excel no es este PDF. Lab `consulta_pacientes_atendidos` id=85710 y
otras listas de espera (ambulatoria, guardia, oftalmo, etc.) imprimen
`ConsultaAtencionAmbulatoria.rptdesign`, no este.

Callable `TS.ATENCION.p_consulta_atenion` (typo Oracle) SoT
`ATENCION_BODY.sql` f_ ~15880 / p_ ~16133; prestaciones
`PERSONAS_BODY.sql` `f_get_prestaciones_ate` ~14311. Port
`atencion.p_consulta_atenion` SETOF `ts.tmp_consulta_atencion` +
`personas.f_get_prestaciones_ate`. Diseño `SELECT * FROM` 10 IN (OUT
`ar_resultado` último). `0` ids → NULL. `an_id_paciente` / cod / id
prestación no se usan en el body. `ac_finalizada` null = solo
finalizadas; cola si `'N'`. Recepcion `NO_DATA_FOUND` → NULL.

Print siempre manda personal de sesión. Logo `urlLogoSmall` file
(`SDLC_VM`). Pie `usuario`. Binds `idPaciente`/`idPrestacion` del
dataset eran `integer` (legacy SP); JSON null → `""` y BIRT 4.24
`Cannot convert … to Integer` (dataset vacío). Port: `string` +
`CAST(? AS text)` → numeric. Oracle `''` origen = NULL.

Smoke: copy HOSPROD 25/03/2024 centro 654 Cañada, servicio `23`
RADIOLOGIA, personal `663233` CAMPANA (23 filas, p.ej. AYMAL).
`target/preview/ConsultaPacientesAtendidos-birt-docker.pdf`.
Confirmado listado [x] ConsultaPacientesAtendidos (HOSPITAL_2).

### 2026-09-22 · ConsultaPedInterconsulta

SoT **HOSPITAL_2** `ConsultaPedInterconsulta.rptdesign`. Único
`printReport`: `BBConsultaPedInterconsulta.actBtnImprimirConsultaPedInterconsulta`.
ESTADISTICAS → Admisión → `msg.consulta_ped_interconsulta` id=180201
(`consultaPedInterconsulta.xhtml`) → filtros + `msg.imprimir`. Excel no
es este PDF.

Callable `TS.ADMISION.p_get_ped_interconsulta` SoT `ADMISION_BODY.sql`
f_ ~5083 / p_ ~5131. Port `admision.p_get_ped_interconsulta` SETOF
`ts.tmp_ped_interconsulta`. Diseño `SELECT * FROM` 8 IN (OUT último).
`0` ids → NULL. Fecha pedido `>= desde AND < hasta+1`. Internación
`anulada=N` y `alta_medica` S/N del checkbox (print default **N**).
Logo `urlLogo` file. Pie `usuario`.

Copy HOSPROD 02/06/2026 centro 654 Cañada `altaMedica=N` (52 pedidos /
51 filas). Smoke:
`target/preview/ConsultaPedInterconsulta-birt-docker.pdf`.
Confirmado listado [x] ConsultaPedInterconsulta (HOSPITAL_2).

### 2026-09-22 · ConsultaPersonalAdmision

SoT **HOSPITAL_2** `ConsultaPersonalAdmision.rptdesign`. Único
`printReport`: `BBConsultaPersonalesAdmision.imprimir`.
ADMISIÓN DE INTERNADOS → Consultas → `msg.consulta_personal_admision`
id=93025 (`consultaPersonalesAdmision.xhtml`) → filtros + `msg.imprimir`
(habilitado tras consultar con filas). Excel / `imprimir_reporte` comentados.

SQL inline `ts.internacion` (no SP). `DECODE(tipoPersonal)` → `CASE`;
`personas.f_get_persona_full`. Fecha `fecha_int >= desde AND < hasta+1`.
`0` ids opcionales → NULL. Centro obligatorio. Default tipo `ADMISION`.
Layout sin imagen file. Pie `usuario`.

Smoke RDS 03/06/2026 centro 654 Cañada (59 internaciones con personal
admisión, p.ej. COSTILLA).
`target/preview/ConsultaPersonalAdmision-birt-docker.pdf`.
Confirmado listado [x] ConsultaPersonalAdmision (HOSPITAL_2).

### 2026-09-22 · consultaPrecioDeCosto

SoT **HOSPITAL_2** `consultaPrecioDeCosto.rptdesign`. Único
`printReport`: `BBConsultaPrecioDeCosto.actBtnImprimir`.
COMPRAS → Consultas → `precio_de_costo` id=56507
(`consultaPrecioDeCosto.xhtml`) → ítem + consultar + `msg.imprimir`.
Excel POI no es este PDF. Modificación precio de costo no imprime este diseño.

SQL inline `ts.costo_item` (no SP). `personas.f_get_persona_full` del
proveedor vía `recepcion_compra`. Logo `urlLogoSmall` file. Pie `usuario`.

Copy HOSPROD ítem `53175` SOLUC. FISIOLOGICA (455 `costo_item`, 455
recepciones, 267 OC, 13 proveedores p.ej. MULTIESPACIO CONTENER).
Columnas del `.rptdesign` `fecha_hora`/`precio_uni_ult_compra` no
existen en `ts.costo_item`: mapeo SoT `COMPRAS_BODY.sql` ~10177
(`fecha_vigencia` / `costo_item`) y `MIN(fecha_emision)` ~10206.
`target/preview/consultaPrecioDeCosto-birt-docker.pdf`.
Confirmado listado [x] consultaPrecioDeCosto (HOSPITAL_2).

### 2026-09-22 · ConsultaProveedores

SoT **HOSPITAL_2** `ConsultaProveedores.rptdesign`. Único
`printReport`: `BBConsultaProveedores.actBtnImprimir`.
COMPRAS → Consultas → `consulta_proveedores` id=56515
(`consultaProveedores.xhtml`) → filtros + consultar + `msg.imprimir`.
Excel POI no es este PDF.

SQL inline `persona`/`proveedor` (no SP). LIKE apellido/nombre/cuit;
`fecha_alta >= desde` y `<= fHasta` (bean suma 1 día a hasta).
`trunc`→`CAST AS date`. Logo `urlLogo` file. Pie `usuario`.

Copy HOSPROD 51 proveedores (MULTIESPACIO + recientes) y 2 `tipo_prov`.
Smoke LIKE `%MULTIESPACIO%`. Header CUIT `0` = `toDecimal` de null
(no recortar el JSON). `target/preview/ConsultaProveedores-birt-docker.pdf`.
Confirmado listado [x] ConsultaProveedores (HOSPITAL_2).

### 2026-09-22 · ConsultaReqAdi

SoT **HOSPITAL_2** `ConsultaReqAdi.rptdesign`. Único `printReport`:
`BBConsultaReqAdi.actBtnImprimirPDF`.
Cirugía → Consultas → `consulta_req_adi` (`consultaReqAdi.xhtml`) →
filtros + consultar + `msg.imprimir_pdf`. Excel POI no es este PDF.

`{call ts.adm_cirugia.p_report_req_adi(8)}` →
`SELECT * FROM adm_cirugia.p_report_req_adi(7 IN)` SETOF
`ts.tmp_reporte_cirugia`. SoT `ADM_CIRUGIA_BODY.sql` `f_consulta_req_adi`
~6871, `p_report_req_adi` ~7028, `pp_completar_tmp_cirugia` ~2566.
Logo `urlLogoSmall` file. Pie `usuario`.
Copy HOSPROD 8 cirugías PREOPERATORIA 01/07/2026–28/10/2026 centro
`654`/`7` BQ GENERAL (p.ej. PALACIOS / LUCIANI). Oracle 0
`reserva_req_adi` en ese corte (columna vacía como la pantalla).
`target/preview/ConsultaReqAdi-birt-docker.pdf`.
Confirmado listado [x] ConsultaReqAdi (HOSPITAL_2).

### 2026-09-22 · ConsultaReservasInt

SoT **HOSPITAL_2** `ConsultaReservasInt.rptdesign`. Único `printReport`:
`BBConsultaReservasInt.imprimir`.
Admisión internados → `consulta_reservas_entre_fechas`
(`consultaReservas.xhtml`) → filtros + consultar + imprimir.
Excel POI no es este PDF. Sector = puesto de admisión de sesión.

`{call ts.ADMISION.p_consulta_reservas(11)}` →
`SELECT * FROM admision.p_consulta_reservas(10 IN)` SETOF
`ts.tmp_reserva_tipo_int`. SoT `ADMISION_BODY.sql` f_ ~4111 / p_ ~4210.
Logo `urlLogo` file. Pie `usuario`. Copy HOSPROD 17 reservas sector `8`
01/06–28/10/2026 BARRAZA. `target/preview/ConsultaReservasInt-birt-docker.pdf`.
Confirmado listado [x] ConsultaReservasInt (HOSPITAL_2).

### 2026-09-22 · ConsultaTipoMovStock

SoT **HOSPITAL_2** `ConsultaTipoMovStock.rptdesign`. Único `printReport`:
`BBConsultaTipoMovStock.actBtnImprimir`.
Farmacia (tile `DEPOSITO` / `inicioFarmacia`) → Consultas → Stock →
`consulta_mov_stock_por_tipo` (`consultaTipoMovStock.xhtml`) → filtros +
consultar + imprimir. Excel POI no es este PDF. No es `consultaMovStock`
ni `consultaMovStockAgrupadoDep`. Depósito = sesión farmacia.

Java llena `tmp_filtro_mov_stock` y llama `f_consulta_det_mov_stock`; BIRT
agrega tmp `WHERE usuario=SESSIONID`. Sidecar:
`SELECT * FROM farmacias.p_consulta_tipo_mov_stock_agrupado/detalle`
(fill + mismo GROUP BY). SoT `FARMACIAS_BODY.sql` ~12024 +
`ImpBusMovStock.generateUsuarioMovStockFiltrado2`.
Logo `urlLogo` file. Pie `usuario`. Copy HOSPROD 19 mov dep `32` INGRESO
`4:1` PROVEEDOR/COMPRAS 01/06–21/09/2026 VICRYL.
`target/preview/ConsultaTipoMovStock-birt-docker.pdf`.
Confirmado listado [x] ConsultaTipoMovStock (HOSPITAL_2).

### 2026-09-22 · ConsultaTriage

SoT **HOSPITAL_2** `ConsultaTriage.rptdesign`. Único `printReport`:
`BBConsultaTriage.actionBtnImprimir`.
Recepción (tile `RECEPCION` / `inicioRecepcionCentro`) → Consultas →
`consulta_triage` (`consultaTriage.xhtml`) → fechas + buscar + imprimir.
Excel POI no es este PDF. Centro = recepción de sesión.

`{call TS.RECEPCIONES.p_consulta_triage(8)}` →
`SELECT * FROM recepciones.p_consulta_triage(7 IN)` SETOF
`ts.tmp_consulta_triage`. SoT `RECEPCIONES_BODY.sql` f_ ~8930 / p_ ~9035.
Logo `urlLogo` file. Pie `usuario`. Copy HOSPROD CARGIULO 13/05/2022
y ALCARAS 21/03/2023 (con `recepcion_amb`) + 5 `triage_amb` 143–147
24–27/07/2026 sin recepción. JSON de ejemplo: 13/05/2022–21/03/2023.
`target/preview/ConsultaTriage-birt-docker.pdf`.
Confirmado listado [x] ConsultaTriage (HOSPITAL_2).

### 2026-09-22 · CtaCteConvenio

SoT **HOSPITAL_2** `CtaCteConvenio.rptdesign`. Único `printReport` de este diseño:
`BBCtaCteConvenio.actionBtnImprimir`.
Cobranza convenio (tile `COBRANZA_CONVENIO`) → Consultas →
`cta_cte_convenio` (`ctaCteConvenio.xhtml`) → entidad + consultar + imprimir.
Excel POI no es este PDF. No es `consulta_cta_cte_convenio` ni
`consulta_deuda_entidad`. Variantes de menú facturación internado usan
la misma pantalla.

`{ call ts.PERSONAS.p_get_cta_cte_convenio(11) }` →
`SELECT * FROM personas.p_get_cta_cte_convenio(10 IN)` SETOF
`ts.tmp_cta_cte_convenio`. SoT `PERSONAS_BODY.sql` f_ ~1653 / p_ ~1965 /
pp_ ~306 / `f_get_fecha_afecta` ~1935; `CUENTA_CORRIENTE_CONVENIO_BODY.sql` ~91.
Tras quitar `AR_RESULTADO`, renumerar binds dataset `1..10` (legacy `2..11`
deja el primer `?` vacío → tabla en blanco). Quitar `designerValues` ODA.
Logo `urlLogo`/`urlLogoSmall` file `SDLC_VM`. Pie `usuario`.
Smoke entidad `336303` CES / convenio `417` ENSALUD (copy DeudaEntidad).
PDF `target/preview/CtaCteConvenio-birt-docker.pdf` (2 págs; Saldo al / Facturacion).
Confirmado listado [x] CtaCteConvenio (HOSPITAL_2).

### 2026-09-22 · CtaCteGarantiaPaciente

SoT **HOSPITAL_2** `CtaCteGarantiaPaciente.rptdesign`. Único `printReport`:
`BBCtaCteGarantiaPac.actionBtnImprimir`.
Cobranza convenio (`COBRANZA_CONVENIO`) → Consultas →
`cta_cte_garantia_pac` (`ctaCteGarantiaPac.xhtml`) → paciente + consultar + imprimir.
Excel POI no es este PDF. `ampliada` es de pantalla, no va al PDF.

`{ call ts.PERSONAS.p_get_cta_cte_garantia_pac(5) }` →
`SELECT * FROM personas.p_get_cta_cte_garantia_pac(4 IN)` SETOF
`ts.tmp_cta_cte_garantia_pac`. SoT `PERSONAS_BODY.sql` pp_ ~251 / f_ ~1294 / p_ ~1371.
Sin logo file. Pie `usuario`.
HOSPROD `cta_cte_garantia_pac` = 0. Copy **otro dominio** `cta_cte_paciente`
paciente `197869` TORRES DAVID MATIAS (25 mov, FACTURA/NC) → `cta_cte_garantia_pac`.
No es paridad de negocio de garantía. JSON + PDF smoke.
`target/preview/CtaCteGarantiaPaciente-birt-docker.pdf`.
Confirmado listado [x] CtaCteGarantiaPaciente (HOSPITAL_2).

### 2026-09-22 · CtaCtePaciente

SoT **HOSPITAL_2** `CtaCtePaciente.rptdesign`. Único `printReport` de este diseño:
`BBCtaCtePaciente.actionBtnImprimir`.
Cobranza convenio (`COBRANZA_CONVENIO`) → Consultas → `cta_cte_paciente`
(`/pages/caja/ctaCtePaciente`) → paciente + consultar + imprimir.
Otras entradas (caja/paciente, facturación) abren la misma pantalla.
Excel POI no es este PDF. El Imprimir del diálogo de detalle es otro acto.

`{ call ts.PERSONAS.p_get_cta_cte_paciente(5) }` →
`SELECT * FROM personas.p_get_cta_cte_paciente(4 IN)` SETOF
`ts.tmp_cta_cte_paciente`. Procedure fija `excluir_aut_no_cobr='N'`.
SoT `PERSONAS_BODY.sql` pp_ ~195 / f_ ~1185 / p_ ~1362.
Logo `urlLogo` file `SDLC_VM`. Pie `usuario`.
Smoke copy HOSPROD paciente `197869` TORRES DAVID MATIAS.
`target/preview/CtaCtePaciente-birt-docker.pdf`.
Confirmado listado [x] CtaCtePaciente (HOSPITAL_2).

### 2026-09-22 · cuadroOperatorio (sin uso HIS)

SoT **HOSPITAL_2** `cuadroOperatorio.rptdesign`. Callers vigentes: **0**
(`printReport` / string del basename en beans/xhtml/xml = 0). No hay paso a
paso de UI: el HIS no dispara este PDF. Listados de quirófano vigentes:
`CirugiasDelDia` / `CirugiaEntreFechas`; checklist `checkListCirugia`; insumos
`ParteInsumosQuirurgico`; anestesia `parteAnest`. SQL inline (sin SP). No
sidecar. Inventario:
[`listado-reportes-birt-legacy-sin-uso.md`](../relevamiento/listado-reportes-birt-legacy-sin-uso.md).
Listado `[x]` 2026-09-22 (huérfano, mismo criterio que `FormHcPacCirugia` /
`evaluacionPreanestesica`).

### 2026-09-22 · DeterminacionesLab

SoT **HOSPITAL_2** `DeterminacionesLab.rptdesign` (no AGH). Happy path: tile
**LABORATORIO** (`LABORATORIO` id 85000) → elegir centro/servicio
(`inicioServicioCentroLab`) → home
`/pages/laboratorio/laboratorio/inicio.faces` → menú lateral
**Recepción Muestras** (`#{msg.recepcion_muestras}`, id 85100) →
**Consulta Determinaciones** (`#{msg.consulta_determinaciones}`, id 85108)
(`/pages/laboratorio/consultaDeterminaciones.faces`) → fechas + buscar →
**Imprimir** (`BBConsultaDeterminaciones.actionBtnImprimir`).
**No** está bajo **Consultas** (id 85700): ahí está
`estadistica_determinaciones_lab` (otro acto, otro diseño).
**Exportar Excel** (POI) no es este PDF.

`{ call TS.LABORATORIO.p_determinaciones_lab(18) }` →
`SELECT * FROM laboratorio.p_determinaciones_lab(18 IN)` SETOF
`ts.tmp_determinacion_pac`. SoT `LABORATORIO_BODY.sql` f_ ~11282 / p_ ~11657.
18º `an_id_personal` / scalar `idPersonal`: sidecar JDBC no tiene `USER` HIS
(`GENERAL.f_get_id_personal_logeado`). Roles vía `f_get_personal_tiene_rol`
como Oracle. Bean HOSPITAL_2 no hace `put` de ese id; el Api/HIS migrado debe
enviarlo. Bean HOSPITAL_2 pone `idLabExtProcesa` (typo); el diseño bindea
`idLabExternoProcesa`. Logo `urlLogo` file `SDLC_VM`. Pie `usuario` (no `DB_USER`).
Smoke paciente `605170` GARRO 01/08–22/09/2026 + `idPersonal=433` mtorres.
`target/preview/DeterminacionesLab-birt-docker.pdf`.

### 2026-09-23 · DetLibroInternacion

SoT **HOSPITAL_2** `DetLibroInternacion.rptdesign`. Happy path: tile
**ADMISION_INTERNADOS** (id 90000) → puesto/centro → menú **Consultas**
(id 93001) → **Consulta de Libro de Internaciones**
(`#{msg.consulta_libro_internacion}`, id 93022)
`/pages/admisionInternados/consultaLibroInternacion.faces` → mes/año desde-hasta
→ Consultar → click fila libro → popup detalle → checkbox imprimir cabecera →
**Imprimir** (`BBConsultaLibroInternacion.actBtnPrintDetLibro`).
**No** es `LibroInternacion.rptdesign` (`genera_confirma_libro_internacion` /
`BBLibroInternacion`). El Imprimir del pie de la grilla está comentado en el xhtml.

Dataset de filas: SQL inline `internacion` filtrado por `id_libro_int_centro`
+ `general.f_get_edad_string` (SoT `GENERAL_BODY.sql`). El ODA decía
`SPSelectDataSet` pero el `queryText` ya era SELECT (no `{call}`).
Header `centro_atencion` + `urlLogo` file `SDLC_VM`. Pie `usuario`.
`mes`/`ano` del libro (columnas HIS; libro 10: `ANO_LIBRO_INT=12082`,
`NRO_MES_LIBRO_INT` null). Bean también pone `mesDesde`/`anoDesde` que el
diseño no declara.

Smoke copy HOSPROD libro `10` centro `654` (503 internaciones, p.ej. ABRAHAM).
`target/preview/DetLibroInternacion-birt-docker.pdf`.
Hallazgo año encabezado `12082`: [`listado-reportes-birt-hallazgos.md`](../relevamiento/listado-reportes-birt-hallazgos.md).

### 2026-09-23 · devolucionInsumos

SoT **HOSPITAL_2** `devolucionInsumos.rptdesign`. Callers (mismos `put`):
`BBInsumosDevueltos.actionBtnImprimir` en `administracionCirugia` (`bbDevolucionInsumos`)
y en `parteInsumos` (`bbInsumosDevueltos`). Params: `idInternacion`, `idCirugia`,
`idPaciente` + `urlLogo`/`usuario`. SQL inline (sin SP) + `general.f_get_edad_anio`.
Dataset SoT `evento_quirofano` no existe en HOSPROD/PG → port `ocupacion_quirofano`
(hallazgos). Copy `id_cirugia=77743` CASANOVA. `urlLogo=SDLC_VM`. Pie `usuario`.
`target/preview/devolucionInsumos-birt-docker.pdf`.

### 2026-09-23 · devolucionProvExterna

SoT **HOSPITAL_2** `devolucionProvExterna.rptdesign`. Caller único
`BBDevolucionProvExt.actionBtnConfirmar` (`devolucionProvExt.xhtml`): tras
`confimarDevolucionProvisionExterna` imprime `RemitoR` por comprobante (PRINTER)
y luego DOWNLOAD `devolucionProvExterna` con `idDevolucionProvExt`=
`obtenerUltimaDevolucion` y `deposito`=`user.getDepositoFarmacia().getIdDeposito()`.
SQL inline (sin SP). `DECODE`→`CASE`. `urlLogo=SDLC_VM`. Pie `usuario`.
Copy HOSPROD `id_devolucion_prov_ext=1` SCAPECCIA / depósito `32`.
`target/preview/devolucionProvExterna-birt-docker.pdf`.

### 2026-09-23 · DevolucionRechazoItemTrazable (sin uso HIS)

SoT **HOSPITAL_2** `DevolucionRechazoItemTrazable.rptdesign`. Callers vigentes: **0**
(`printReport` / string del basename en beans/xhtml/xml = 0). No hay paso a
paso de UI. El HIS dispara el título parecido en
`DevolucionReservaItemPacInt` / `DevolucionReservaItemCirugia` (param `titulo`
`informe_devolucion_rechazo_item_trazable_cuarentena`) y remito de ajuste
trazable (`Remito.rptdesign`). SQL inline (sin SP). No sidecar. Inventario:
[`listado-reportes-birt-legacy-sin-uso.md`](../relevamiento/listado-reportes-birt-legacy-sin-uso.md).
Listado `[x]` 2026-09-23 (huérfano).

### 2026-09-23 · DevolucionReservaItemPacInt

SoT **HOSPITAL_2** `DevolucionReservaItemPacInt.rptdesign`. Caller único
`BBDevolucionItemPacInt.actionBtnPrintTransferencia` (`devolucionItemPacInt.xhtml`):
popup tras **Realizar devolución**. Params `nroTransferencia`=`id_mov_dep` +
`titulo` (bean; el diseño no lo declara). SQL inline + `personas.f_get_persona_full`.
Copy `id_mov_dep=488674` internación `8010018173`. `urlLogo=SDLC_VM`. Pie `usuario`.

### 2026-09-23 · DispensacionItemTrazable

SoT **HOSPITAL_2** `DispensacionItemTrazable.rptdesign`. Caller único
`BBConsultaDispensacionItemTrazable.actBtnImprimir` (`consultaDispensacionItemTrazable.xhtml`).
SQL inline + `personas.f_get_domicilio_persona` (`PERSONAS_BODY.sql` ~509).
`DECODE`/`sysdate`/`rownum`/`?+1` → `CASE`/`CURRENT_TIMESTAMP`/`LIMIT 1`/`INTERVAL '1 day'`.
Dataset `fechaHasta` string→`dateTime` e `idPaciente` string→`float` (PACIENTE y nested EVENTOS; si no, CAST PG deja vacío).
Print: `estadoItemTrazable=VALIDADO`; `idPaciente`/`codItemTrazable`/`idEventoTrazable`
sentinels `-1`/`"-1"`; `idEventoTrazable` = `getIdEventoItemTrazable()` (null→`-1`), no el `84` del constructor de búsqueda.
HOSPROD 0 `evento_item_trazable` VALIDADO con `id_paciente`. **Smoke inventado**
paciente `990021` SMOKEDISP + eventos `990021`/`990022` VALIDADO sobre ítem
`990001` (`SeedDispensacionItemTrazableSmoke.py`); no muta `990001`/`990002`.
`urlLogo=SDLC_VM`. Pie `usuario`. Excel `actBtnExportar` existe en bean pero no hay botón en el xhtml.

### 2026-09-23 · EstudioAmbA4

SoT **HOSPITAL_2** `EstudioAmbA4.rptdesign`. Callers `BBImpresionOftalmologia.imprimirEstudio`
(`impresionOftalmologia.xhtml`) y `BBImagenologia.actBtnPrintOrdenPedido` (solo ambulatorio).
No es `EstudioAmbA4_UNIFICADA`. SQL inline + `general.f_get_edad_anio` +
`personas.f_get_matricula_full`/`f_get_especialidad_full`. Copy `id_atencion_amb=4731056`
HERRERA COPA centro `654` servicio `6`. `urlLogo=SDLC_VM`. Layout sin pie `usuario`.
Dump PG `det_prescrip_prest_amb` no tiene `id_convenio`/`id_plan_convenio`/`nro_afiliado`
(Oracle sí): ATENCION usa convenio/plan de `atencion_amb` y afiliado de `ord_serv_amb`
(el `NVL` de detalle no aplica). El `LEFT JOIN` a detalle queda para el bind `idDetPrescripPrestAmb`.

### 2026-09-23 · EstudioIntA4

SoT **HOSPITAL_2** `EstudioIntA4.rptdesign`. Callers `EstudioInt` + `Utils.getTipoPrtReceta`
(A4): `BBIndicacionesPostInternacion.imprimirEstudio` (`indicacionesPostInternacion.xhtml`)
y clones obs guardia / consultorio / auditoría; `BBDatosPaciente.imprimirEstudioInternacion`
si S3 falla. No es `EstudioIntTICKET` / `_UNIFICADA` ni ticket MATRICIAL
(`PrintComandos.prtTicketEstudiosInternacion`). SQL inline + `general.f_get_edad_anio` +
`personas.f_get_matricula_full`/`f_get_especialidad_full`/`f_get_personal_matricula`
(`nro_mat_deriva::text`). Copy `id_internacion=8010022156` GONZALEZ centro `654` servicio `2`.
`urlLogo=SDLC_VM`. Layout sin pie `usuario`. Bean `idInternacion`+`cliente`.

### 2026-09-29 · BalanceHidrico

SoT **HOSPITAL_2** `BalanceHidrico.rptdesign` (HOS-APP/AGP más chicos e iguales entre sí).
Callable `ATENCION.p_get_balance_hidrico_pac` (`ATENCION_BODY.sql` ~17060) OUT cursor →
`SELECT * FROM atencion.p_get_balance_hidrico_pac` SETOF `ts.tmp_balance_hidrico_pac`.
`NULLIF(id,0)` = no-filtro (worker decimal null→0). `atencion.f_bh_fmt_num` porque
Oracle `NUMBER || texto` trata null como `''`. Pie `DB_USER` → `usuario`.
`urlLogo` no es imagen file: el layout lo usa en `indexOf("MATERDEI")` (default del
diseño es MATERDEI y mostraría la leyenda). JSON manda `SDLC_VM`.
Copy HOSPROD balance `48814` internación `8010021978` MOLLAR (33 dets, form 1).
Hallazgo: HC ambulatoria hace `put("idAtencioAmb")` (typo); no filtra.
Cabecera ambulatoria: visibilidad `idAtencionAmb == null` no alcanza porque el worker
manda `0`; el grid pinta etiquetas vacías. Ocultar también cuando el valor es `0`.

### 2026-09-29 · CaratulaPresentacionLote

SoT **HOSPITAL_2** `CaratulaPresentacionLote.rptdesign`. Un caller:
`BBLotePresenEnt.generaDocumentacionCaratula`, desde el popup de
`lotePresentacionConfirmado.xhtml` (checkbox Carátula, botón Generar). Va a un ZIP,
no al visor. SQL inline (`comprobante` UNION ALL `comp_interno`), no package.
`DECODE`→`CASE`. `CONCAT` Oracle (null anula)→`||`. Logo file `params["urlLogo"]`
sin scalar: se declara y el JSON manda `SDLC_VM` (el diseño solo tenía `urlLogoSmall`).
Sin pie de usuario. Copy HOSPROD lote `44498` (4 facturas, entidad 11326).

### 2026-09-29 · CensoAuditoriaMedica

SoT **HOSPITAL_2** `CensoAuditoriaMedica.rptdesign`. Un caller:
`BBConsultaCensoAuditoriaMedica.actBtnPrintReport` (visor). Params
`idSectorAdmision`, `idConvenio`, `fechaDesde`, `fechaHasta`. El combo
Paciente (Todos / Internados / Alta) filtra la grilla y no entra al PDF.
SQL inline. `DECODE`→`CASE`. `fecha_int <= trunc(hasta)+1` →
`date_trunc + interval '1 day'`. Pie `DB_USER` → `usuario`. Logo file
`urlLogo=SDLC_VM`. Smoke sector `4` convenio `75` 20–27/05/2026 (11 filas).

### 2026-09-29 · Certificado

SoT **HOSPITAL_2** `Certificado.rptdesign` (HOS-APP tiene otro archivo).
Callers ambulatorio / guardia / oftalmología / terapia / domicilio ponen
`idAtencionAmb` e `idPaciente`. El diseño no declara `idPaciente`.
Internación imprime `CertificadoInt`, no este. SQL inline.
`DECODE`→`CASE`, `ROWNUM`→`LIMIT 1`. Pie `DB_USER`→`usuario`.
Logo file `urlLogo=SDLC_VM`. Firma = BLOB `FIRMA_PERSONAL`.
Copy atención `4731840` GIMENEZ (licencia 48 hs).

### 2026-09-29 · CertificadoAtencionAmb

SoT **HOSPITAL_2** `CertificadoAtencionAmb.rptdesign`. Un caller:
`BBImpresionCertificadoAtencion.actionBtnImprimir` (visor). Solo
`idAtencionAmb`. Paciente y fechas filtran la grilla de recepción.
SQL inline, casi el de `Certificado` sin licencia ni firma: afiliado
desde `det_atencion_amb` y especialidad por `id_especialidad_ate`.
`DECODE`→`CASE`. Pie `DB_USER`→`usuario`. Logo file `urlLogo=SDLC_VM`.
Smoke atención `4731840`. El título que el bean pasa al visor es
`EtiquetaItemA4`; el diseño es este.

### 2026-09-29 · CertificadoImplante

SoT **HOSPITAL_2** `CertificadoImplante.rptdesign`. Un print:
`BBCertificadoImplante.actBtnImprimir` con `idCertificadoImplante`.
GEA abre visor; el resto manda a impresora. Misma pantalla en
intraoperatorio y postoperatorio. SQL inline. `DECODE`→`CASE`.
Sin pie de usuario. Logo file `urlLogo=SDLC_VM`.
Copy certificado `1313` ESCUDERO / CHAMALE.

### 2026-09-29 · CertificadoInt

SoT **HOSPITAL_2** `CertificadoInt.rptdesign`. Tres prints, el mismo diseño y
el mismo param `idInternacion`: internación, observación de guardia y
observación de guardia consultorio. El botón **Certificado** graba
`licencia_medica` y no imprime. Imprime el ícono del panel **Imprimir**,
habilitado solo si ya hay licencia. SQL inline.
`DECODE`→`CASE`, `ROWNUM`→`LIMIT 1`. Pie `DB_USER`→`usuario`.
Logo file `urlLogo=SDLC_VM`. Firma = BLOB `FIRMA_PERSONAL` (esta internación
no tiene firma ni diagnóstico principal válido).
Copy internación `8020023424` VIVAS (licencia 480 hs).

### 2026-09-29 · CirugiaEntreFechas

SoT **HOSPITAL_2** `CirugiaEntreFechas.rptdesign`. Un print:
`BBConsultaCirugias.actBtnExportarPdf` (visor). Excel POI y
`CirugiaEntreFechasHDD` no son este diseño. `CirugiasDelDia` es otra
pantalla. Callable `ADM_CIRUGIA.p_report_consulta_cirugia` (15 IN + OUT)
→ `SELECT * FROM adm_cirugia.p_report_consulta_cirugia(...)`.
`0` y string vacío = sin filtro. `faseCirugia` va en el JSON en null
porque el diseño lo bindea y el bean no lo pone. Pie `DB_USER`→`usuario`.
Logo file `urlLogoSmall` (scalar agregado; el diseño declaraba `urlLogo`
y el layout leía `urlLogoSmall`). Smoke centro `654`/`7`, 03/06/2026,
`TODAS`. `CirugiaEntreFechasHDD`: 0 `printReport`. Inventario
`listado-reportes-birt-legacy-sin-uso.md`. Listado `[x]` 2026-09-29.
No hay diseño en Hospital-Reports.

### 2026-09-29 · CirugiasDelDia

SoT **HOSPITAL_2** `CirugiasDelDia.rptdesign`. Un print:
`BBConsultaCirugiasDelDia.actBtnExportarPdf`. Mismo callable que
`CirugiaEntreFechas`: desde y hasta = `fecha`. `tipoConsulta` null
(el combo está comentado) → `TODAS`. El servicio de la grilla no es
param del PDF. Pie `usuario`. Logo file `urlLogoSmall`.
Smoke mismo grafo Cañada 03/06/2026. Listado `[x]` 2026-09-29.
`CirugiasDelDiaHDD`: 0 `printReport`. No se porta. Inventario
`listado-reportes-birt-legacy-sin-uso.md`. Listado `[x]` 2026-09-29.

### 2026-09-29 · CodBarraItemFraccionado

SoT **HOSPITAL_2** `CodBarraItemFraccionado.rptdesign`. Etiqueta 50×14 mm.
Caller vivo: `BBConsultaItemTrazable.imprimirCodBarra` (Imprimir Completo /
Imprimir Resumido). `BBFraccionamientoItemDeposito` tiene el `printReport`
comentado. No es `ItemTrazable` (Imprimir Eventos) ni `RemitoR`.
SQL `replace('[GS]', chr(29))` sin `from dual`. Sin `urlLogo` / pie `usuario`.
`barcodeType` 6 (el 5 de `etiquetaItem` es EAN-13). Payload GS1 con letras:
no se usa DANI 2of5. Sidecar sin plugin Barcode → el código queda como texto.
HOSPROD `ts.item_trazable` = 0. JSON = default del diseño, marcador `[GS]`.
Listado `[x]` 2026-09-29.

### 2026-09-29 · CodPrestSeccionNomen

SoT **HOSPITAL_2** `CodPrestSeccionNomen.rptdesign`. Un print:
`BBCodPrestSeccionNom.actionBtnImprimir` (visor). Params `codSeccion` (float)
y `nomSeccion`. Excel es POI. SQL inline `ts.cod_prestacion`. Pie
`DB_USER`→`usuario`. Logo `urlLogoSmall` era `source=url`; el sidecar lo
resuelve a archivo (`source=file`). Copy HOSPROD sección `4` ANALISIS
CLINICOS, 1022 prestaciones. Listado `[x]` 2026-09-29.

### 2026-09-29 · ComparacionCotizaciones

SoT **HOSPITAL_2** `ComparacionCotizaciones.rptdesign`. Cinco prints, los
mismos params (`idEmpresa`, `idCentroCompra`, `idSolCotiza`, `idDetSolCotiza`):
consulta de OC, pendientes de entrega, solicitud, autoriza y preautoriza.
El botón está en el popup **Comparar Cotizaciones**. Callable
`COMPRAS.p_get_comparacion_cotizaciones` (OUT + 4 IN) →
`SELECT * FROM compras.p_get_comparacion_cotizaciones(...)`.
El body no carga el centro de compra y no persiste la moneda de la cotización.
Pie `usuario`. Logo file `urlLogo`. Copy sol `11675` det `1`.
Listado `[x]`.

### 2026-09-29 · ConsentimientoRecepcion

SoT **HOSPITAL_2** `ConsentimientoRecepcion.rptdesign`. Sin dataset: el bean
pone `Titulo` y `Texto` (`ConsentimientoInformado.getConsentimientoStr`,
BLOB UTF-8). Logo file `urlLogo`. La leyenda de Mater Dei se oculta cuando
el path del logo no contiene `MATERDEI`. Sin pie `usuario`. El print de
recepción es `BBRecepcionPaciente` / `BBPrincipalRecepcion`; el mismo par
de params sale de internación, guardia, cirugía, ambulatorio y el ABM
`BBConsentimientoInformado`. Texto de ejemplo = HOSPROD id `209`.
PG `ts.consentimiento_informado` = 0; el PDF no consulta la tabla.
Listado `[x]`.

### 2026-09-29 · ConsultaAcompanante

SoT **HOSPITAL_2** `ConsultaAcompanante.rptdesign`. SQL inline sobre
`internacion` con persona de contacto. `DECODE`→`CASE`. Seis binds
(centro, servicio y cada fecha dos veces). La grilla suma un día a
`fechaHasta`; el PDF compara el timestamp tal cual. Pie `usuario`.
Logo file `urlLogoSmall`. Caller `BBConsultaAcompanantes.actBtnAceptarCopias`.
Excel es POI. Copy internación `8030017775`. En HOSPROD el menú SERVICIO
no tiene una fila que abra esta pantalla.
Listado `[x]`.

### 2026-09-29 · ConsultaAdmisionesPorMes

SoT **HOSPITAL_2** `ConsultaAdmisionesPorMes.rptdesign`. Callable
`ADM_CIRUGIA.p_report_ctd_admisiones_mes` delega en `f_get_ctd_admisiones_mes`
→ `SELECT * FROM adm_cirugia.p_report_ctd_admisiones_mes(6 IN)`.
El conteo de cada mes es el mes calendario del año, sin recortar al rango.
`0` de servicio/convenio → NULL. Pie `usuario`. Sin logo. Caller
`BBConsultaAdmisionesPorMes.imprimir`. Excel es POI. Smoke centro `654`,
2025–2026.
Listado `[x]`.

### 2026-09-29 · ConsultaAnalisisLabPac

SoT **HOSPITAL_2** `ConsultaAnalisisLabPac.rptdesign`. El call es
`LABORATORIO.p_muestras_entre_fechas`, el mismo de ConsultaMuestras.
El diseño no bindea `req_almacenamiento` ni `muestra_adicional`: quedan NULL.
Pie `usuario`. Logo file `urlLogo`. El botón Exportar PDF está
`rendered="false"`. Excel es POI. Smoke junio–agosto 2026.
Listado `[x]` y fila en `listado-reportes-birt-legacy-sin-uso.md`.





