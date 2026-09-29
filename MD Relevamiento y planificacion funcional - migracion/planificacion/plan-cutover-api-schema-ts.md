# Plan de cutover — Hospital-Api → schema `ts` migrado

`phase_id:` **`sdd.hospital.plan-cutover-api-schema-ts`**  
Fecha: **2026-08-20** · actualizado 2026-08-25  
Estado: **Cutover completo — Fases 2–4 TERMINADAS** (anunciador, recepción/cola, AGI, catálogo en `ts`; piloto `public.*` retirado)  


Regla: [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md)  
Tipos: [`catalogo-tipos-oracle-pg.md`](../relevamiento/catalogo-tipos-oracle-pg.md)  
Pendientes no-tabla: [`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md)

---

## Situación actual

| Capa | Hoy | Canónico destino |
|------|-----|------------------|
| BD piloto Api (`hospital_api` local) | Flyway todavía crea `public.llamado_paciente`, `public.anunciador`, … (UUIDs, columnas reducidas) — **ya NO activa para anunciador/recepción/AGI/catálogo** | `ts.llamado_anunciador`, `ts.anunciador`, … en PG migrado |
| BD migrada | `grupogea-hospital_dev` / schema `ts` (2279 tablas, paridad nombres) | Fuente de verdad DDL |
| Oracle | Oráculo / legacy vía VPN | No escribir desde stack nuevo en régimen |

D-ANU-01 (diferir mirror) queda **superseded** para schema: la tabla ya está en PG.

> **Avance 2026-08-24/25:** el runtime de anunciador/recepción/AGI/catálogo ya opera sobre
> `ts.*` (adapters JDBC reescritos, Fase 2 terminada). Fase 3 (retiro del shape piloto
> `public.*` vía V33) y Fase 4 (auditoría — no quedan CUs de dominio) también completadas.

---

## Objetivo del cutover

1. Datasource de desarrollo/UAT del Api apunta (o incluye) al PG migrado con
   `search_path` / refs a **`ts`**.
2. Adaptadores JDBC de anunciador/recepción leen y escriben
   `ts.llamado_anunciador` (y hermanas), no `llamado_paciente`.
3. Seeds/demos de anunciador usan IDs y columnas legacy (`leyenda_paciente`,
   `llamar`, `tipo_atencion`, …).
4. **Migraciones del Api (histórico 2026-08-21, no repetir sobre el dump):** enfoque
   por GRUPO FUNCIONAL para ambientes **sin** el dump de 2279 (`hospital_api` / `-test`).
   Sobre la PG compartida (estructura Oracle, sin datos) **no** se vuelven a crear esas
   tablas. Canon vigente: [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md)
   § Flyway. Las V propias de entonces fueron:
   - `V27__ts_anunciador.sql` → tablas `ts` de anunciador (6)
   - `V28__ts_recepcion.sql` → tablas `ts` de recepción/cola (5)
   - `V29__ts_anunciador_assets_bytea.sql` → assets anunciador (logo/`img_fondo`) a `BYTEA` (idempotente; P-ORA-006)
   - `V30__ts_ocupacion_ambientes.sql` → ocupación de ambientes (`ocupacion_ambiente_amb`, `det_ocupacion_ambiente_amb`, `persona`, `servicio`)
   - `V31__ts_agi_maestros.sql` → maestros/identidad AGI (`paciente`, `turno`, `convenio`, `plan_convenio`, `validador_online_df`, `prestacion`, `terminal_ag`, `opcion_terminal_ag`, `sec_nro_ticket_terminal`)
   - `V32__ts_agi_recepcion.sql` → materialización recepción AGI (`recepcion_amb`, `det_prest_recep_amb`, `tmp_recepcion_amb`, `tmp_det_prest_recep_amb`, `ord_serv_amb`, `tmp_ticket_autorecepcion`)
   Reglas: nombres/columnas = Oracle; tipos según catálogo; FKs internas aplicadas,
   FKs a tablas aún no migradas **diferidas** (`pendientes-solo-oracle.md` P-ORA-009).
5. Retiro documentado de tablas `public.*` piloto que duplican dominio.

---

## Fases del plan

### Fase 0 — Documentación ✅

- Regla canónica + catálogo + pendientes + este plan.
- Inventario Oracle↔PG archivado.
- **Avance 2026-08-21:** enfoque por grupo funcional definido (no baseline monolítico);
  V27/V28 creados y validados.

### Fase 1 — Conectividad y lectura ✅ (parcial)

- Config local/UAT: URL al PG migrado vía `HOSPITAL_PG_URL` (credenciales fuera de git).
- **Avance 2026-08-21:** V27 `ts_anunciador` + V28 `ts_recepcion` creados desde el
  `pg_dump` real de 10.0.0.35 y **aplicados/validados** en `grupogea-hospital_dev-test`.
  Conectividad del Api (dev) y de Identity al PG comprobada; stack E2E arriba
  (Identity :8080 · Api :8081 · Web :4200).
- Mapear `BLOB`→`BYTEA` (P-ORA-006) para logo/fondo: **resuelto** — V29 convierte
  `ANUNCIADOR.LOGO` / `IMG_FONDO_ANUNCIADOR` a `BYTEA` (media type fijo `image/png`).

### Fase 2 — Escritura anunciador/recepción ✅ **TERMINADA**

> **Avance 2026-08-24 — Fase 2 completa (implementado y validado en vivo):**
> - Dominio anunciador migrado de `public.*` (UUID, `llamado_paciente`) a `ts.*` con ids
>   **NUMERIC** serializados como **STRING** en JSON.
> - Migraciones nuevas: `V29` (`ts_anunciador_assets_bytea`, reescrita **idempotente**:
>   convierte `logo`/`img_fondo_anunciador` a `BYTEA` solo si son `text`; en el remoto ya
>   eran `bytea`), `V30` (`ts_ocupacion_ambientes`: `ocupacion_ambiente_amb`,
>   `det_ocupacion_ambiente_amb`, `persona`, `servicio`).
> - Adapters reescritos a `ts.*`: `JdbcAnunciadorReadAdapter`, `JdbcAnunciadorWriteAdapter`,
>   `JdbcRecepcionColaAdapter`, `JdbcRecepcionEsperaServAdapter`.
> - Decisiones de fidelidad:
>   * anunciador **"activo"** = existe vínculo en `ts.anunciador_ambiente_amb` (Oracle no
>     tiene flag activo);
>   * sector = primer ambiente por `nro_orden`;
>   * ocupación de ambientes = `ts.ocupacion_ambiente_amb` + `det_ocupacion_ambiente_amb` +
>     `persona` (no `atencion_amb`);
>   * ventana de llamados por `fecha_hora_llamado`;
>   * assets media type fijo `image/png`.
> - Criterio C1 cumplido: cero `llamado_paciente` en código de producción (grep confirma).
> - ITs adaptados + smoke verde.

> **Avance 2026-08-24 — AGI + catálogo migrados a `ts` (a petición del usuario, fidelidad al legacy):**
> - El piloto (`recepcion_agi`, `paciente_agi`, `turno_agi`, `terminal_ag`, `servicio_agi`,
>   `convenio_agi`) era **INVENTO del read-model**; se reemplazó por el shape `ts` canónico.
> - Migraciones nuevas: `V31` (`ts_agi_maestros`: `paciente`, `turno`, `convenio`,
>   `plan_convenio`, `validador_online_df`, `prestacion`, `terminal_ag`,
>   `opcion_terminal_ag`, `sec_nro_ticket_terminal`), `V32` (`ts_agi_recepcion`:
>   `recepcion_amb`, `det_prest_recep_amb`, `tmp_recepcion_amb`,
>   `tmp_det_prest_recep_amb`, `ord_serv_amb`, `tmp_ticket_autorecepcion`).
> - `JdbcAgiAdapter` reescrito a `ts.*` replicando los packages `RECEPCIONES.f_*`:
>   `f_get_turnos_pendientes` (turno **OTORGADO**), `f_gen_autorecep_turnos/prestacion`
>   (materializa `recepcion_amb` + `det_prest` + marca turno **RECEPCIONADO** +
>   `tmp_ticket_autorecepcion`), `f_gen_cola_espera_recep` (espera humana en
>   `cola_espera_recep`), `f_llamar_paciente_recepcion` (throttle + update `cola` +
>   `llamado_anunciador`). Nro de ticket fiel vía `ts.sec_nro_ticket_terminal` (PREF NNNN).
>   También materializa `ord_serv_amb` (orden de servicio ambulatoria, estado **GENERADA**,
>   tipo **NORMAL**) replicando `f_genera_recepcion`.
> - Catálogo de convenios migrado a `ts.convenio` (DTO propio `Convenio`, desacoplado del
>   `ConvenioAgi`): `codigo`=`cod_conv_validador`, `nombre`=`convenio`,
>   `req_flags` de `convenio` + `validador_online_df`.
> - Deudas de AGI resueltas: `ticketCodigo` consistente POST/GET (nro_espera_recep de
>   `tmp_ticket` vía `sec_nro_ticket_terminal`); `ord_serv_amb` materializada.
> - ITs AGI + catálogo adaptados (22 tests verdes).

### Fase 3 — Retiro del shape piloto ✅ TERMINADA (2026-08-25)

- Dejar de usar `llamado_paciente` en runtime: **YA CUMPLIDO** (criterio C1 — cero
  referencias en código de producción).
- **Decisión 2026-08-25:** se eligió **dropear** las tablas piloto `public.*`.
  Creada y validada la migración **V33__retiro_tablas_piloto_public.sql** que dropea
  las ~19 tablas del dominio huérfanas (`llamado_paciente`, `anunciador`, `sector`,
  `ambiente`, `ocupacion_ambiente_actual`, `anunciador_ambiente`, `diccionario_anunciador`,
  `paciente_agi`, `recepcion_agi`, `turno_agi`, `convenio_agi`, `servicio_agi`,
  `terminal_ag`, `centro_ate`, `cola_espera_recep`, `cola_espera_serv_amb`,
  `puesto_recepcion`, `recepcion`, `elegibilidad_seed`→*ver nota*).
- **Nota:** `elegibilidad_seed` se MANTIENE (helper del validador seed de AGI, no existe
  en ts) — la quitamos del drop para no romper `SeedValidadorElegibilidadAdapter`.
- Se conservan las tablas de infra/soporte: `sec_id_*` (secuencias V10), `sec_id_tabla`
  (fallback ids), `sec_id_internacion` + refs de internación (V13), `audit_logs`,
  `file_metadata`, `table_a`.
- **Corrección de auditoría:** los ids AGI (`COLA_ESPERA_RECEP`, `RECEPCION_AMB`,
  `ORD_SERV_AMB`) salen de SECUENCIAS PG de V10 (no de `sec_id_tabla`, que es solo fallback).
- Falls de V33 quedan a cargo; aplicar en entornos con data piloto verificando antes.
- Actualizar SDD (`ciclo-vida-llamado-anunciador`, `paridad-recepcion-cola`) — se pide aparte.

### Fase 4 — Ampliar superficie ✅ AUDITORÍA COMPLETADA (2026-08-25)

- **Auditoría realizada:** todos los CUs de dominio del Api operan 100% sobre `ts.*`
  (AGI, Anunciador read/write, Recepción Cola A y B, Catálogo de convenios).
- Lo que queda en `public.*` es **infra/helper deliberado, NO dominio pendiente**:
  `sec_id_*` (generación de ids), `sec_id_tabla`/`sec_id_internacion` (ids genéricos/
  internación), `centro_atencion_ref`/`tipo_admision_ref` (refs V13), `elegibilidad_seed`
  (helper demo AGI), `table_a` (starter), `audit_logs`/`file_metadata` (auditoría/storage).
- **No quedan CUs de dominio por migrar.**
- Gaps ts posibles V34+ (FKs/joins diferidos, NO bloquean CUs): `ts.personal`,
  `ts.especialidad`, `ts.atencion_amb`, `ts.localidad`, `ts.provincia`, etc.
  (registrados en V31/V32 como diferidos).

---

## Fuera de alcance de este plan

- Portar packages PL/SQL enteros (ver plan CQRS).
- Recrear 1815 triggers en PG de un golpe.
- Cutover de producción / big-bang de datos clínicos.

---

## Criterios de aceptación (implementado / validado)

| # | Criterio | Estado |
|---|----------|--------|
| C1 | Ningún SQL de dominio anunciador referencia `llamado_paciente` | ✅ Cumplido (grep en código de producción) |
| C2 | Columnas usadas ⊂ columnas de `ts.llamado_anunciador` inventariadas | ✅ Cumplido |
| C3 | Display TV / WS siguen funcionando con el shape legacy | ✅ Cumplido (smoke verde + ITs) |
| C4 | Documentado P-ORA-006 para assets | ✅ Cumplido (V29 idempotente a `BYTEA`) |
| C5 | SDD del slice enlazan esta regla y no D-ANU-01 diferido | ✅ Cumplido (retiro en Fase 3) |

---

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Entornos locales sin acceso al PG migrado | Documentar VPN/red; opcional snapshot reducido `ts` solo anunciador |
| Seeds UUID vs IDs numéricos | Reseed con IDs compatibles o secuencias `ts` |
| Lógica de triggers/packages ausente | Registrar P-ORA-002/003; implementar en app por CU |
| FKs a tablas aún no migradas (corte por grupo) | Diferir FK (P-ORA-009); agregar con `ADD CONSTRAINT` cuando la tabla referenciada exista |
| Migraciones coexistiendo con piloto `public` | Schemas distintos (`ts` vs `public`); ver retiro en fase 3 |

---

## Próximo paso de implementación

1. ~~Spike Fase 1 (conexión + SELECT)~~ — **hecho 2026-08-21** (conectividad + migraciones V27/V28 validadas).
2. ~~Reescribir adaptadores JDBC anunciador/recepción a `ts.*`~~ — **hecho 2026-08-24** (Fase 2 + AGI/catálogo a `ts`, ITs verdes).
3. ~~Fase 3: retiro del shape piloto `public.*`~~ — **hecho 2026-08-25** (V33 dropea las piloto; `elegibilidad_seed` se mantiene).
4. ~~Fase 4: ampliar superficie~~ — **hecho 2026-08-25** (auditoría: no quedan CUs de dominio; lo que sigue en `public.*` es infra/helper deliberado).
5. **Opcional / deuda menor:** aplicar V33 en los entornos con data piloto verificando antes; completar tablas ts V34+ (personal, especialidad, atencion_amb, etc.) si un CU futuro lo exige; actualizar SDD de los slices (ciclo-vida, paridad) enlazando la regla ts.
