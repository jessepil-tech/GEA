---
title: Cursor — rules y skills del programa (equipo)
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.cursor-team
indice_blurb: Texto para pegar en Team Rules + mapa de rules/skills
---
# Cursor — rules y skills del programa (equipo)

<!-- TEAM-STAMP:BEGIN -->

Sello **generado** 2026-09-17 (`python3 tools/generar-team-rules.py`). Pegar en el dashboard las cajas `text` de abajo. No citar número de versión de una regla en la copia; se cita la regla.

| Fuente | version / status |
|--------|------------------|
| `regla-waiver-paridad-legacy.md` | canonical |
| `regla-ddl-postgres-migrado.md` | canonical |
| `regla-paridad-ui-legacy.md` | 1.18.0 |
| `regla-playwright-migracion.md` | 1.0.0 |
| `regla-evidencia-ejecutable.md` | 1.0.0 |
| `regla-paridad-logica-plsql.md` | 1.0.0 |
| `regla-instalacion-referencia.md` | 1.0.0 |
| `regla-paridad-acceso-auditoria.md` | 1.0.0 |
| `regla-no-funcionales-migracion.md` | 1.0.0 |
| `proceso-sdd-paridad-completa.md` | 1.5.0 |
| `loop-migracion-corte.md` | 1.1.0 |
| `datos-canonicos.md` | canonical |
| `glosario-migracion.md` | canonical |

<!-- TEAM-STAMP:END -->

`phase_id:` **`sdd.hospital.cursor-team`**

**Team Rules** = misma lógica para **todos**, en **cualquier** repo del Team.  
**`.cursor/rules` en git** = detalle y globs (Flyway, Angular, SDD). No hace falta copiar el
manual entero al cloud.

Paquete versionado en este repo: [`.cursor/rules/`](../../.cursor/rules/) ·
[`.cursor/skills/`](../../.cursor/skills/).

---

## Qué va en Team (Required) — pegar **8** a **15**

Las 1–14 ya están (o casi). **Agregar la 15** (verificar ≠ reescribir). Refrescar 4
si el texto local aún no menciona Viaje Playwright ni Ledger de evidencia.

### 1) Gobierno / análisis modular

```text
Programa Hospital GrupoGEA. Antes de evaluar o migrar un módulo: seguir
Hospital-Migration/docs/sdd/gobierno-migracion.md (capas 1–5).
DoR: carpeta docs/sdd/relevamiento-<modulo>/ con pipeline.md + maestros.md
(Fases A–C), veredicto en README y sección `## Profundidad de análisis`
(seis capas: pipeline, escritores cruzados, jobs, firmas del package,
FK, reportes/integraciones) con estado cerrado / muestra / diferido(slug)
/ N/A — nada en silencio. `muestra` no autoriza a tratar el stream como
analizado en profundidad: el corte no hereda esa muestra; cierra las
capas que toca (loop paso 3). verificar-sdd.sh audita la sección.
Checklist A1–A5 (personal, perfiles, maestros, habilitación, parámetros)
sin silencio.
Prohibido: viable solo con happy path operativo; tratar seed/demo como
paridad de configuración o como E2E cerrado; omitir maestros/permisos
sin N/A; citar A–C o `indice-legacy` como grafo cerrado si la capa sigue
en muestra. Avance = CUs en ts; si insertan, evidencia = fila nacida del CU
(no seed ni dump Oracle). Copia Oracle = bootstrap de padres, no prueba
de ABM. No MVP / no segundo piloto. Canon: criterio-avance-e2e-datos.md.
Padre de menú = MENU_APLICACION (no carpeta xhtml). Tile → módulo, no primer CU.
Canon orientación: regla-paridad-orientacion-visual.md
Ejecución de un corte (8 pasos, orden fijo): loop-migracion-corte.md —
elegir (backlog) → reservar (BODY, rango Flyway, tablas ts; si está tomado no
arranca) → firmar universo (semilla ACCION/xhtml + 1 hop; tronco afuera; techo
8 xhtml / 15 beans / 20 firmas / 5 rptdesign → partir el corte) → contratar
fixture (qué padres, quién los crea; si falta: diferido(fixture) al abrir) →
implementar → evidenciar → cerrar → publicar y dejar Estado del corte en el
README del slug. Prohibido: carpeta del módulo como semilla; fixture descubierto
en el click-through; dos cortes sobre el mismo BODY o rango Flyway.
Tooling obligatorio (no reproducirlo a mano):
  ./tools/indice-legacy.sh --semilla X  -> candidatos + techo (paso 3)
  ./tools/verificar-sdd.sh <slug>       -> audita el corte; exit 1 = no cierra
Ejemplos: relevamiento-node-anunciador/ · relevamiento-turnos/
· relevamiento-nutricion/ · relevamiento-his-orientacion/
```

### 2) DDL canónico ts

```text
DDL HIS = dump PostgreSQL **ts** (paridad nombres Oracle TS). Flyway del Api
**no** replica ese catálogo ni inserta seeds (`Vnn` solo si falta un objeto
que el dump no trae). Datos de prueba: Hospital-Api/scripts/sql/seeds/
(padres/<tabla>/ · sec-id/ · oraculo/). No db/dev-seed para seeds nuevos.
No seedear la tabla que este corte ABMea.
Identity = otra base, su Flyway. Instalador Flyway del HIS = cierre de
programa, no cada corte. No modelos public de dominio ni *_agi.
Padres: dump PG → copia Oracle (lectura) → mock solo si no hay conexión.
Canon: Hospital-Migration/docs/sdd/regla-ddl-postgres-migrado.md
· regla-fixture-oracle-padres.md
```

### 3) Paridad / WAIVE

```text
Paridad funcional 1:1 con legacy. Si legacy tiene la capacidad: implementar
o diferir(slug) con SDD hijo. WAIVE solo con evidencia de que legacy NO
la tiene. Nunca WAIVE por velocidad de piloto. Seed ≠ paridad ABM/permisos.
Diferido = carpeta + backlog; cola corta (cobrar hijo antes de otro módulo).
No silencio. Canon: regla-waiver-paridad-legacy.md
```

### 4) Documentación / SDD

```text
Documentación de migración: Hospital-Migration/docs/ (canon, relevamiento,
cortes, estado, …). docs/sdd/ solo tiene punteros; no crear cortes ahí.
Flujo: relevamiento (capa 3) → corte de implementación spec/plan/tasks/verify
(capa 4) → código. Un slug por corte, no el módulo entero
(ej. docs/cortes/turnos/turnos-maestros-personal/).
Palabras: corte ≠ stream ≠ carril; gate ≠ compuerta. Narrativa en español.
Canon: glosario-migracion.md · gobierno-migracion.md.
Clarify #7 = Gate UI de arranque (xhtml, geometría, inventarios
copy/validaciones) antes de scaffold Web.
Verify: ninguna capacidad legacy en silencio (done / diferido(slug) / WAIVE),
Paridad UI (xhtml) completa si hay pantallas, y **Viaje Playwright**
(N/A | e2e-migrado | diferido(fixture) — `regla-playwright-migracion.md`).
Actualizar backlog-orden y estado-piloto si cambia un gate. Canon:
proceso-sdd-paridad-completa.md · relevamiento-modular-funcional.md ·
regla-paridad-ui-legacy.md · regla-playwright-migracion.md
```

### 5) Desarrollo (starter)

```text
Código en repos producto: respetar el scaffold del starter, no inventar capas.
Hospital-Api: core → application (CQRS Dispatcher) → infrastructure →
presentation-api. Resource delgado; sin SQL/Panache en el Resource.
Hospital-Web: core/domain → application/use-cases → pages. Sin HttpClient
de negocio en el component. Pantallas migradas: Gate UI arranque (xhtml
primero; geometría DoD; API sola ≠ UI done). Canon en cada repo:
docs/architecture/ARQUITECTURA_QUARKUS_STARTER.md
docs/architecture/ARQUITECTURA_ANGULAR_STARTER.md
Rules: hospital-api-scaffold · hospital-web-scaffold ·
hospital-web-gate-ui-arranque (alwaysApply)
Identity = único IdP. Reports = sidecar BIRT, no embeber en Api.
```

### 6) Commits git

```text
Commits en repos GrupoGEA Hospital: un autor humano del equipo.
Prohibido en el mensaje: Co-authored-by de Cursor/agente, Made-with: Cursor,
trailers de herramienta. Cada uno desactive en Cursor Settings → Git la
opción de atribuir commits a Cursor. No versionar secretos.
```

### 7) Gate UI de arranque

```text
Pantallas migradas Hospital: Gate UI de arranque ANTES de template o “API first”.
Orden: abrir xhtml → inventarios copy/validaciones + geometría (anchos, misma fila)
→ UI del slice → cablear API → verify → smoke.
Prohibido: tratar geometría/disposición como polish; cerrar UI porque el API responde
o el form guarda; pedir smoke sin paridad xhtml (salvo diferido(slug) en SDD).
Canon: Hospital-Migration/docs/sdd/regla-paridad-ui-legacy.md
Rules alwaysApply: hospital-gate-ui-arranque · hospital-web-gate-ui-arranque
Skill: Hospital-Migration/.cursor/skills/gate-ui-arranque
```

### 8) Viaje Playwright

```text
Cada SDD Hospital declara Viaje Playwright en verify (sin silencio):
N/A (motivo) / e2e-migrado / diferido(fixture) / cerrado-pre-regla
(gate ya PASS antes de la regla: no reabrir e2e).
Pantalla + acto de usuario → e2e en Hospital-Web (CI). Legacy HIS = opt-in
con fixture vigente; prohibido escanear Oracle para completar el spec.
Mocks PASS ≠ G6. Universo de CU = matriz SDD, no la batería E2E.
Canon: Hospital-Migration/docs/sdd/regla-playwright-migracion.md
Rule alwaysApply: hospital-playwright-viaje
Skill: Hospital-Migration/.cursor/skills/playwright-viaje-slice
```

### 9) Evidencia ejecutable

```text
El verify se prueba, no se redacta: afirmación sin artefacto reproducible =
silencio (no PASS). Ledger de evidencia en verify-report con una fila por
afirmación: build/test = comando + conteo; endpoint = método+ruta+status;
escritura = id de la fila + query + qué CU la creó; e2e = paso + valor medido
+ trace; PDF = path + motor real; G6 = quién/fecha/frase del operador.
Verdictos: verificado | no verificado | no ejecutado. Prohibido inferir
verificado. Si se toca el código del corte, las filas vuelven a no verificado.
Capacidad done con todas sus filas no verificado = FAIL. Corte que escribe sin
id vigente en PG = FAIL (toast y 201 no alcanzan; fila borrada tampoco).
Canon: Hospital-Migration/docs/sdd/regla-evidencia-ejecutable.md
Rule alwaysApply: hospital-evidencia-ejecutable
Skill: Hospital-Migration/.cursor/skills/cerrar-gate-antigap
```

### 10) Paridad de lógica PL/SQL

```text
Portar una rutina PL/SQL no es traducirla: es demostrar con casos que devuelve lo
mismo. Oráculo = Oracle 11.2 (copia de prod) por captura JDBC; el código nuevo no
apunta al 11.2. Toda firma del universo termina en una de tres decisiones firmadas
en el spec: portar (golden master en el ledger) / puente (registrado en
pendientes-solo-oracle.md) / rediseñar (firma de producto). "Traducida y compila"
no es decisión. Las rarezas del legacy se preservan (plural incorrecto, anclaje de
ADD_MONTHS, bytes WE8, mensaje ORA-* como contrato): corregirlas es decisión de
producto, no de quien migra. Casos mínimos: NULL, vacío, cero y borde de fecha.
Captura sin mutar el oráculo y sin PII; credenciales fuera del repo.
Canon: Hospital-Migration/docs/sdd/regla-paridad-logica-plsql.md
```

### 11) Capacidades sin pantalla

```text
Pantallas migradas ≠ circuito cerrado. El relevamiento parte del menú, y hay
capacidades que ninguna pantalla muestra: 82 procesos programados y las interfaces
con terceros. La matriz anti-gap no las detecta porque no hay pantalla desde donde
preguntar. En el paso 3, antes de firmar: ./tools/indice-legacy.sh --jobs <dominio>,
y cada job del circuito termina en se porta / diferido(slug) / N/A con motivo.
Silencio = gap. Dos trampas verificadas: el nombre del job miente
(MigrarTurnoVencidoJob también purga la cola del mostrador y apaga el anunciador), y
la frecuencia no está en el código (vive en ts.tarea_programada con flag ACTIVA, así
que sin ese dato no se sabe si el job está vivo).
Canon: Hospital-Migration/docs/sdd/relevamiento-procesos-programados/
       Hospital-Migration/docs/sdd/relevamiento-integraciones-externas/
```

### 12) Instalación de referencia

```text
"Paridad con el legacy" no tiene sujeto: el HIS es multi-instalación y tiene 313
ramas esClienteX() sobre 16 clientes, también dentro de circuitos ya cerrados
(recepción, agenda). La rama se decide por el atributo cliente de acercade.xml,
empaquetado en el WAR. Los once acercade.xml del repo dicen cliente="TS", que no
coincide con ningún helper: leyendo o ejecutando el legacy del repositorio TODAS las
ramas están apagadas y se ve el camino genérico de fábrica. Por eso el spec declara
la instalación de referencia y cada rama del universo termina en se porta / N/A (es de
otro cliente) / diferido(multi-instalacion). Un helper puede agrupar varias
instalaciones (esClienteGEA cubre cinco). No replicar el if por nombre de cliente en
la plataforma nueva: si hay que variar, es configuración, no código.
Canon: Hospital-Migration/docs/sdd/regla-instalacion-referencia.md
```

### 13) Acceso y trazabilidad

```text
El gate no pregunta por el sujeto y el legacy no controla acceso en la pantalla: lo
hace el menú por perfil (aplicación de seguridad aparte: perfil_acceso, rol_acceso,
menu_rol_acceso) y el rol funcional dentro del PL/SQL, que consulta
ts.rol_funcional_pers y aborta con Raise_application_error. Ese mensaje es contrato y
no se cambia. Cada CU declara qué perfiles veían la entrada de menú y qué rol
funcional exige, y lo prueba con un actor SIN el rol: el rechazo observado es la
evidencia. PASS logrado con el usuario administrador no dice nada sobre acceso.
Trazabilidad: el legacy audita 264 tablas con packages TBL_AUD_* campo por campo
(valor viejo / valor nuevo); si el corte escribe en una auditada y el destino no
registra, es diferido(auditoria) con el riesgo escrito, no silencio. fecha_last_update
y actualizado_por son el último cambio, no el historial.
Canon: Hospital-Migration/docs/sdd/regla-paridad-acceso-auditoria.md
```

### 14) No funcionales en el gate

```text
El presupuesto se declara al abrir el corte (paso 3) y se mide al cerrarlo (paso 6).
Tres ejes: tiempo (percentil, no promedio), volumen (dataset del orden del legacy;
si no hay, "medido en vacío" + diferido(perf-volumen), nunca PASS) y concurrencia
(prueba de dos actores por recurso disputado: turno, cama, numerador, stock).
Si el legacy usaba FOR UPDATE sobre ese recurso, la concurrencia no puede ser
"no aplica": se replica el control o se difiere con el riesgo escrito. CU que
escribe en varias tablas evidencia el rollback, no solo el camino feliz.
Prohibido resolver un problema de desempeño cambiando comportamiento funcional
(menos datos, otro orden, rango por defecto recortado) sin pasar por paridad UI.
Canon: Hospital-Migration/docs/sdd/regla-no-funcionales-migracion.md
```

### 15) Verificar propuesta (no reescribir)

```text
Verificar no significa que la propuesta anterior esté mal ni que haya que
reescribirla. «¿Estás seguro?», «validá», «es lo más adecuado?» = juzgar
ESA propuesta contra la TAREA PEDIDA, desde otro ángulo, sin sobrecorrección
ni doble-down.

Orden fijo:
0) Tarea pedida en una línea (alcance y zoom). Si el turno anterior usó
   otro zoom, declararlo: no inventar un plan a esa escala distinta.
1) Citar la propuesta (objeto bajo examen). No arrancar otro plan.
2) Ángulo nuevo = otra lente sobre la MISMA afirmación (datos, costo,
   tablero, evidencia). Prohibido cambiar alcance o grano y llamarlo
   «estrategia nueva».
3) A favor y en contra de ESA propuesta respecto de la tarea del paso 0.
4) Veredicto: CONFIRMA | CONFIRMA CON AJUSTE | NO SE SOSTIENE.

CONFIRMA: es la más adecuada para esa tarea; sin ranking nuevo.
CONFIRMA CON AJUSTE: el tronco es el anterior; el cambio se etiqueta delta.
NO SE SOSTIENE: qué evidencia la falsea frente a la tarea; recién ahí
reemplazar. Tras CONFIRMA, no añadir una «versión refinada».

Mal: cuatro «mejores estrategias» porque cada verificación cambió el zoom.
Bien: la misma estrategia, confirmada o no con un lente distinto.

Sin dato nuevo que falsee, la respuesta es la anterior, más nítida.
Al proponer: invariantes vs contingentes.
Rule alwaysApply: hospital-verificar-propuesta
```

---

## Qué **no** va a Team (queda en git)

| Tema | Dónde |
|------|--------|
| Skills (relevamiento, abrir SDD, verify, onboarding, **gate-ui-arranque**, **playwright-viaje-slice**) | `Hospital-Migration/.cursor/skills/` |
| Runbook de ejecución (8 pasos: reservas, universo firmado, fixture, estado del corte) | `docs/sdd/loop-migracion-corte.md` + rule `hospital-gobierno-migracion` |
| Linter del canon e índice del legacy | `tools/verificar-sdd.sh` · `tools/indice-legacy.sh` (skill `migrar-corte` los encadena) |
| Compuerta automática (hook `stop`) | `.cursor/hooks.json` **versionado** en este repo + `tools/hook-verificar-sdd.sh`; si se abre una carpeta padre, `tools/instalar-hook.sh` |
| Rule SDD al editar `docs/{canon,cortes,relevamiento,…}/**` | `hospital-sdd-docs.mdc` (glosario + taxonomía) |
| Detalle paridad UI (globs pages) | `Hospital-Web/.../hospital-web-paridad-ui-legacy.mdc` |
| Flyway HIS / seeds | `Hospital-Api/.cursor/rules/hospital-flyway-ts.mdc` · `hospital-seeds.mdc` · seeds `Hospital-Api/scripts/sql/seeds/` |
| Flyway Identity | `Hospital-Identity/.cursor/rules/hospital-identity-flyway.mdc` |
| Checklist T1/T2…, P0–P5 | SDD de cada slice |
| Verificar propuesta (detalle alwaysApply) | `hospital-verificar-propuesta.mdc` — el texto corto va a Team (caja 15) |

Workspace multi-repo: conviene tener **Hospital-Migration** abierto para skills.

## Mantenimiento

Cambios de proceso → docs SDD primero → acortar Team (este archivo) → **pegar en el
dashboard Team (Required)** las rules 8 a 15, y refrescar 4. Sin secretos ni nombres de
herramientas ajenas al producto.
