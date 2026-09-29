---
title: Pendientes solo-Oracle
status: active
owner: grupogea
last_updated: 2026-08-20
phase_id: sdd.hospital.pendientes-solo-oracle
---
# Pendientes solo-Oracle (aún no en PostgreSQL migrado)

`phase_id:` **`sdd.hospital.pendientes-solo-oracle`**  
Fecha: **2026-08-20**  
Estado: **vivo** — registro obligatorio bajo
[`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md)

Si algo existe en Oracle `TS` y **no** (o solo parcial) en PG `ts`, **no** se
sustituye en silencio con un modelo inventado: se anota aquí y se planifica.

---

## Snapshot de objetos (2026-08-20)

| Tipo | Oracle `TS` | PG `ts` | Estado |
|------|-------------|---------|--------|
| `TABLE` | 2279 | 2279 | Paridad nombres/columnas (ver inventario) |
| `INDEX` | 7579 | 7575 | Casi paridad; delta 4 a investigar |
| `SEQUENCE` | 117 | 117 | Presente en PG |
| `VIEW` | 60 | 0 | **Pendiente** |
| `MATERIALIZED VIEW` | 1 | 0 | **Pendiente** |
| `TRIGGER` | 1815 | 0 | **Pendiente** (lógica → Quarkus / PG triggers selectivos) |
| `PACKAGE` / `PACKAGE BODY` | 326 / 325 | 0 | **Pendiente** (estrategia CQRS / ports) |
| `FUNCTION` / `PROCEDURE` | 4 / 11 | 0 | **Pendiente** |
| `JOB` | 1 | 0 | **Pendiente** |
| `LOB` (segmentos) | 152 | (vía columnas TEXT) | Revisar semántica BLOB→TEXT |

Constraints en PG (referencia): PK 1708, FK 4128, CHECK 8335, UNIQUE 5.

---

## Registro de pendientes

### P-ORA-001 — Vistas Oracle (`VIEW` × 60)

| | |
|--|--|
| Impacto | Reportes, consultas Hibernate nativas, BIRT que lean vistas |
| Acción | Inventariar `ALL_VIEWS` → decidir recrear en PG o absorber en queries Quarkus |
| Prioridad | Media (al tocar CUs que las usen) |
| Estado | Abierto |

### P-ORA-002 — Triggers (`TRIGGER` × 1815)

| | |
|--|--|
| Impacto | Auditoría (`TAUD_*`), reglas al INSERT/UPDATE/DELETE |
| Acción | Por CU: portar a aplicación o trigger PG mínimo; no asumir “mismo trigger” |
| Prioridad | Alta cuando el CU escribe tablas con `TAUD_*` / lógica crítica |
| Estado | Abierto |

### P-ORA-003 — Packages PL/SQL (≈326)

| | |
|--|--|
| Impacto | Núcleo de negocio legacy (`ts.ANUNCIADORES`, `ATENCION`, `GENERAL`, …) |
| Acción | Seguir [`plan-migracion-packages-cqrs.md`](../arquitectura/plan-migracion-packages-cqrs.md); un CU = un corte |
| Prioridad | Según backlog de CUs |
| Estado | Abierto, con flujos clave ya replicados en la app |
| Avance | **2026-08-24:** la lógica clave de `RECEPCIONES.f_*` para AGI se replicó **EN LA APP** en `JdbcAgiAdapter` (`f_gen_autorecep_turnos/prestacion`, `f_gen_cola_espera_recep`, `f_llamar_paciente_recepcion`, `f_genera_recepcion` → `ord_serv_amb`). El ciclo de vida de `ANUNCIADORES` se replicó **parcialmente** en `JdbcAnunciadorWriteAdapter`. El package sigue en Oracle, pero la lógica de los flujos migrados ya vive en Quarkus (ver plan-cutover Fase 2). **2026-09-16:** `ATENCION.f_set_paciente_anun_cola` portada en app (`SetPacienteAnunColaEngine`); GM PASS (errores capturados 11.2; CALL prohibido porque INSERTA). |

### P-ORA-004 — Funciones / procedimientos sueltos

| | |
|--|--|
| Impacto | BIRT / SQL nativo / golden master |
| Acción | Inventario + port o stub documentado |
| Prioridad | Según reportes del slice |
| Estado | Abierto |

### P-ORA-005 — Materialized view + JOB

| | |
|--|--|
| Impacto | Procesos batch / refrescos |
| Acción | Identificar objeto y dueño funcional; recrear o reemplazar en app |
| Prioridad | Baja hasta que un CU lo necesite |
| Estado | Abierto |

### P-ORA-006 — Semántica `BLOB`/`CLOB` → `TEXT`

| | |
|--|--|
| Impacto | Assets anunciador (`LOGO`, `IMG_FONDO_ANUNCIADOR`), documentos |
| Acción | `ANUNCIADOR.LOGO` / `IMG_FONDO_ANUNCIADOR` **corregidos a `BYTEA`** (excepción documentada en catálogo; migración V29 idempotente). **Resto (`78`)**: evaluar por objeto — binario real → `BYTEA`; contenido codificado (base64) → mantener `TEXT` |
| Prioridad | Alta para anunciador — **resuelto** para anunciador; resto abierto |
| Estado | **RESUELTO** para anunciador (V29 idempotente, media type fijo `image/png`) · resto **Abierto** |
| Evidencia | `V29__ts_anunciador_assets_bytea.sql` · `catalogo-tipos-oracle-pg.md` (sección excepción) · plan-cutover Fase 2 (2026-08-24) |

### P-ORA-007 — Delta de índices (Oracle 7579 vs PG 7575)

| | |
|--|--|
| Impacto | Performance / unicidad |
| Acción | Diff de nombres de índices; recrear faltantes si son UNIQUE/PK auxiliares |
| Prioridad | Media |
| Estado | Abierto |

### P-ORA-008 — Nullability relajada (3 columnas)

| | |
|--|--|
| Impacto | Integridad |
| Acción | Ver `inventario-ddl-oracle-pg/diff_nullability.csv`; alinear `NOT NULL` o waiver |
| Prioridad | Baja–media |
| Estado | Abierto |

### P-ORA-009 — FKs diferidas en grupo anunciador/recepción (corte inicial)

Las tablas `ts` anunciador/recepción se migraron por **grupo funcional** (V27 `ts_anunciador`,
V28 `ts_recepcion`) — ver [`plan-cutover-api-schema-ts.md`](../planificacion/plan-cutover-api-schema-ts.md).
Se crearon las FKs **internas/cruzadas** (a tablas presentes en el set). Las FKs que
apuntan a tablas **aún no migradas** se **difieren**: no aplicadas en V27/V28, quedaron
como comentario en esos archivos. Se agregan cuando cada tabla referenciada exista.

Pendientes de agregar (lista íntegra, cruzada con el DDL `ts` real de 10.0.0.35):

| Tabla `ts` (ya en V27/V28) | Constraint FK diferida | Referencia (tabla no migrada) |
|---|---|---|
| `ambiente_amb` | `fk_2_ambiente_amb` | `ts.tipo_ambiente_df` |
| `sector_amb_centro_ate` | `fk_1_sector_amb_centro_ate` | `ts.centro_atencion` — **APLICADA en V28** |
| `llamado_anunciador` | `sys_c00176206` | `ts.paciente` |
| `llamado_anunciador` | `sys_c00176209` | `ts.cola_espera_triage` |
| `llamado_anunciador` | `sys_c00176211` | `ts.servicio` |
| `llamado_anunciador` | `sys_c00176207` | `ts.puesto_recepcion` — **APLICADA en V28** |
| `llamado_anunciador` | `sys_c00176208` | `ts.cola_espera_recep` — **APLICADA en V28** |
| `llamado_anunciador` | `sys_c00667100` | `ts.cola_espera_serv_amb` — **APLICADA en V28** |
| `centro_atencion` | `fk_1` | `ts.deposito` |
| `centro_atencion` | `fk_2` | `ts.localidad` |
| `centro_atencion` | `fk_3`, `fk_6`, `sys_c00160554` | `ts.servicio_centro` |
| `centro_atencion` | `fk_5` | `ts.pack_logos` |
| `centro_atencion` | `fk_7..17` | `ts.concepto_cont` |
| `centro_atencion` | `fk_18` | `ts.server_mail` |
| `centro_atencion` | `fk_19` | `ts.server_sms` |
| `centro_atencion` | `fk_20` | `ts.grp_centro_atencion` |
| `centro_atencion` | `fk_21` | `ts.tipo_int_centro` |
| `cola_espera_recep` | `fk_1`, `fk_10` | `ts.prefijo_ticket` |
| `cola_espera_recep` | `fk_2` | `ts.opcion_terminal_ag` |
| `cola_espera_recep` | `fk_3` | `ts.personal` |
| `cola_espera_recep` | `fk_5` | `ts.recepcion_amb` |
| `cola_espera_recep` | `fk_6` | `ts.paciente` |
| `cola_espera_recep` | `fk_7` | `ts.convenio` |
| `cola_espera_recep` | `fk_8` | `ts.plan_convenio` |
| `cola_espera_recep` | `fk_9` | `ts.nivel_esi_terminal_triage` |
| `cola_espera_serv_amb` | `fk_1`, `fk_6` | `ts.personal` |
| `cola_espera_serv_amb` | `fk_2` | `ts.turno` |
| `cola_espera_serv_amb` | `fk_3` | `ts.especialidad_serv` |
| `cola_espera_serv_amb` | `fk_4` | `ts.recepcion_amb` |
| `cola_espera_serv_amb` | `fk_5` | `ts.paciente` |
| `cola_espera_serv_amb` | `fk_7` | `ts.atencion_amb` |
| `cola_espera_serv_amb` | `fk_8` | `ts.triage_amb` |
| `cola_espera_serv_amb` | `fk_9` | `ts.servicio_centro` |
| `cola_espera_serv_amb` | `fk_10` | `ts.opcion_terminal_ag` |
| `cola_espera_serv_amb` | `fk_12` | `ts.prefijo_ticket` |
| `puesto_recepcion` | `fk_2` | `ts.caja_recepcion` |
| `puesto_recepcion` | `sys_c0040682`, `sys_c0040683` | `ts.tipo_pulsera` |
| `recepcion` | `fk_1` | `ts.deposito` |

**Nota de aplicación:** las FKs marcadas como *APLICADA en V28* (cruzadas) ya se crearon;
las estrictamente diferidas (aun sin la tabla) se añadirán con `ALTER TABLE ... ADD
CONSTRAINT` en una migración V futura cuando la tabla referenciada se migre al método de
grupo. No se inventó la tabla: solo se postergó la FK.

> **Avance 2026-08-24 (V31 `ts_agi_maestros` / V32 `ts_agi_recepcion`):**
> Al migrar los grupos maestro + recepción AGI se **levantaron (aplicaron) varias FKs que
> antes estaban diferidas** en V27/V28: ahora hay integridad referencial entre las tablas
> del set V27-V32. FKs aplicadas en V31/V32 — internas e inter-grupo — (revisadas con el DDL
> de los archivos de migración):
> - `paciente` → `convenio` / `plan_convenio` / `paciente(id_madre)`
> - `convenio` → `validador_online_df` (x2) / `plan_convenio(id_plan_conv_dflt_tur)`
> - `plan_convenio` → `convenio`
> - `turno` → `paciente`, `servicio`, `convenio`, `plan_convenio`, `centro_atencion`, `prestacion`
> - `terminal_ag` → `ambiente_amb`
> - `opcion_terminal_ag` → `terminal_ag`, `centro_atencion`, `recepcion`
> - `sec_nro_ticket_terminal` → `centro_atencion`
> - `det_ocupacion_ambiente_amb` → `ocupacion_ambiente_amb`, `persona(id_personal)`, `servicio`; `ocupacion_ambiente_amb` → `ambiente_amb` (V30)
> - `recepcion_amb` → `recepcion`, `paciente`, `convenio`, `plan_convenio`, `centro_atencion`, `puesto_recepcion`, `terminal_ag`, `persona` (x3)
> - `det_prest_recep_amb` → `recepcion_amb`, `prestacion`, `convenio`, `plan_convenio`, `turno`, `ord_serv_amb`
> - `ord_serv_amb` → `paciente`, `convenio`, `plan_convenio`, `recepcion_amb`, `servicio` (x2), `centro_atencion` (x2)
>
> Las **tablas `tmp_*`** (`tmp_recepcion_amb`, `tmp_det_prest_recep_amb`,
> `tmp_ticket_autorecepcion`) son **staging** que la validación puebla y **no** llevan FKs.
>
> **Fks que siguen diferidas** en V31/V32 (referencias a tablas aún no migradas al set):
> - `paciente.id_personal_conf_datos` → `personal`
> - `convenio.id_vademecum_comercial` → `vademecum_comercial`; `convenio.id_fmt_grp_fact` → `fmt_grp_fact`
> - `plan_convenio.id_vademecum_comercial` → `vademecum_comercial`
> - `prestacion.id_personal_carga` → `personal`; `prestacion.cod_analisis_lab` → `analisis_lab`
> - `turno.id_personal{,_otorga,_suspende,_realiza_reemplazo,_reemplazo}` → `personal`; `turno.id_motivo_*` → `motivo`; `turno.id_grp_prest_tur_*` → `grupo_prestacion_*`; `turno.id_call_center_otorga` → `call_center`; `turno.id_internacion` → `internacion`; `turno.id_turno_no_disponible/_inhibe` → `turno` (auto, diferidas por política de entorno)
>
> **T1 (2026-08-27):** existen `ts.personal`, `ts.call_center`, `ts.motivo`, `ts.tipo_motivo_df` (V34+V35), pero las FKs desde `ts.turno` (y resto de columnas `id_personal*` de recepción/AGI) **siguen diferidas** a propósito (RF-3 / seed OTORGADO). Levantamiento = corte posterior cuando seed AGI esté alineado.
> - `opcion_terminal_ag.id_terminal_triage_espera` → `terminal_triage`; `opcion_terminal_ag.id_sector_admision` → `sector_admision`; `opcion_terminal_ag.prefijo_ticket` → `prefijo_ticket`
> - `sec_nro_ticket_terminal.prefijo_ticket` → `prefijo_ticket`
> - `det_ocupacion_ambiente_amb.id_centro_ate` → `centro_atencion` (dejada por verificar compatibilidad Oracle); `det_ocupacion_ambiente_amb.id_especialidad` → `especialidad`; `det_ocupacion_ambiente_amb.id_det_reserva_ambiente_amb` → `det_reserva_ambiente_amb`; `ocupacion_ambiente_amb.id_reserva_ambiente_amb` → `reserva_ambiente_amb`; `persona.id_nacionalidad` → `nacionalidad`; `persona.id_personal_alta` → `personal` (V30)
> - `recepcion_amb.id_personal{,_recep,_anula}` → `personal`; `recepcion_amb.id_triage_amb` → `triage_amb`; `recepcion_amb.id_localidad_fact/id_provincia_fact` → `localidad`/`provincia`; `recepcion_amb.id_institucion_derivante` → `institucion_derivante`; `recepcion_amb.id_rendicion_recepcion` → `rendicion_recepcion`
> - `det_prest_recep_amb.id_personal_*` → `personal`; `det_prest_recep_amb.id_centro_ate_realiza/factura` → `centro_atencion` (política entorno); `det_prest_recep_amb.id_servicio_realiza/factura` → `servicio` (política entorno); `det_prest_recep_amb.id_especialidad_realiza` → `especialidad`; `det_prest_recep_amb.id_atencion_amb` → `atencion_amb`; `det_prest_recep_amb.id_valid_online` → `validador_online_df`; `det_prest_recep_amb.id_guideline` → `guideline`
> - `ord_serv_amb.id_personal{,_anula,_no_fact}` → `personal`; `ord_serv_amb.id_orden_serv_abierta/principal/trans` → `ord_serv_amb` (auto); `ord_serv_amb.id_especialidad_realiza` → `especialidad`; `ord_serv_amb.id_motivo_*` → `motivo_*`; `ord_serv_amb.id_lote_rend_recep_amb/id_rendicion_recepcion` → `rendicion_*`; `ord_serv_amb.id_empresa/id_sucursal_empresa` → `empresa`/`sucursal_empresa`
>
> **No se inventó ninguna FK**: cada una salió de leer los archivos reales `V29`-`V32`
> (`Hospital-API/infrastructure/src/main/resources/db/migration/`) y quedó registrada en sus
> comentarios de "FOREIGN KEYS DIFERIDAS". Las FKs se levantan cuando cada tabla referenciada
> se migre al método de grupo («no documentado» si una referencia quedara sin confirmar).

| | |
|--|--|
| Objeto Oracle | Tablas `ANUNCIADOR`, `RECEPCION`/`COLA_*`, `AGI`/recepción ambulatoria (FKs a `PACIENTE`, `SERVICIO`, `DEPOSITO`, `PERSONAL`, `CONVENIO`, etc.) |
| ¿En PG? | Tablas sí (V27-V32); **algunas FKs levantadas** en V31/V32; resto **diferidas** (tablas referenciadas aún no migradas) |
| Impacto | Sin FK completa, no hay integridad referencial sobre movimientos a las tablas aún no migradas hasta el cu; riesgo bajo porque el contenido aún se escribe vía app |
| Acción | Migrar cada tabla referenciada por grupo, luego `ADD CONSTRAINT FK`. Revés de cada CU |
| Prioridad | Alta cuando se migre `PACIENTE`, `SERVICIO`, `DEPOSITO`, `PERSONAL`, `ESPECIALIDAD`, `ATENCION_AMB` |
| Estado | Abierto (parcialmente resuelto con V31/V32) |
| Evidencia | `V27`-`V32__ts_*.sql` (FKs aplicadas y diferidas comentadas) + `information_schema` 10.0.0.35 |

### P-ORA-010 — WS real de validación de elegibilidad (obras sociales)

> **Deuda de integración AGI:** hoy el flujo de autorizado/rechazo usa una tabla **seed** (`elegibilidad_seed`) como placeholder porque el WS real del validador de obra social **NO está implementado en el stack nuevo**.

| | |
|--|--|
| Objeto Oracle | `ts.validador_online_df` (config del validador: `url_ws_produccion`, `url_ws_test`, `req_token`, `req_version_credencial`, …) + clientes WS en `Hospital-Legacy/VALIDADORES/` (ConexiaUP, ITC, ITCRest, activia, apross, sancor, osmecon, …) |
| ¿En PG? | **Tabla sí** (`ts.validador_online_df`, migrada en `V31__ts_agi_maestros.sql`); **clientes WS NO** (no están en el stack nuevo) |
| Impacto | El AGI no puede validar la elegibilidad real contra la obra social; solo puede simularla con `elegibilidad_seed` |
| Acción | Implementar un adaptador `ValidadorElegibilidadPort` real que llame al WS de la obra social (según `ts.validador_online_df`/`convenio.id_validador_online`), replicando lo que el legacy hace en `Hospital-Legacy\VALIDADORES\ws\ValidadorWS.java` (`convenioValidElegibilidad`). Reemplazar `SeedValidadorElegibilidadAdapter` y retirar `elegibilidad_seed` del esquema de prueba. |
| Prioridad | Media-Alta (bloquea validación real de elegibilidad en AGI) |
| Estado | Abierto (placeholder `elegibilidad_seed` a la espera del WS real) |
| Evidencia | `ts.validador_online_df` (V31), `Hospital-Legacy/VALIDADORES/` (clientes), `ValidadorWS.java`, `SeedValidadorElegibilidadAdapter.java`, `elegibilidad_seed` (V12) |

### P-ORA-TUR-011 — Sync `atiende_turnos` tras `f_check_hab_turnos` (D-TUR-11)

| | |
|--|--|
| Objeto Oracle | `TS.TURNOS.f_check_hab_turnos` actualiza `servicio_centro` / `personal_servicio` / `equipo_serv_centro`.`atiende_turnos` además de `hab_turnos_*.vigente` |
| ¿En PG? | Tablas puente **no** en Flyway Api (solo `hab_turnos_*` V36); check T2 solo toca `hab_*.vigente` |
| Impacto | Oferta legible en maestros puente no refleja vigencia hasta portar DDL+sync |
| Acción | Cuando existan `servicio_centro` / `personal_servicio` / `equipo_serv_centro` en Api: extender check o comando hijo. DDL puente lectura arranca en [`turnos-hab-buscadores/`](../cortes/turnos/turnos-hab-buscadores/) (B0); sync `atiende_turnos` sigue diferido aquí. |
| Prioridad | Media (bloquea paridad completa del job; no bloquea ABM hab T2) |
| Estado | Abierto — diferido Clarify T2 D-TUR-11 |
| Evidencia | [`turnos-config-hab-horarios/spec.md`](../cortes/turnos/turnos-config-hab-horarios/spec.md) · verify T2 gate-done 2026-08-27 |

---

## Plantilla (copiar al agregar)

```markdown
### P-ORA-NNN — <título>

| | |
|--|--|
| Objeto Oracle | `TS.<NOMBRE>` (`TABLE`/`VIEW`/…) |
| ¿En PG? | No / parcial (`…`) |
| Impacto | … |
| Acción | … |
| Prioridad | Alta / Media / Baja |
| Estado | Abierto / En curso / Cerrado |
| Evidencia | … |
```

---

## Cómo usar en un slice SDD

En `spec.md` / `tasks.md` del CU:

1. Declarar tablas `ts.*` que usa.
2. Si falta vista/package/trigger → link a `P-ORA-NNN` (crear si no existe).
3. **Prohibido** cerrar gate-done inventando tabla `public.*` alternativa.
