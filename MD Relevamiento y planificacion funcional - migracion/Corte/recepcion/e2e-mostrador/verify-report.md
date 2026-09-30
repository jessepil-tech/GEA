---
title: Verify — E2E mostrador
version: 0.3.0
status: draft
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.e2e-mostrador
---

# Verify — `e2e-mostrador`

**Gate:** abierto (`diferido(acceso)` · `diferido(perf-volumen)` · jobs). Viaje T5 →
AGI → Cola B → Llamar → TV verificado 2026-09-16. Acceso 401 sin Bearer **medido**
2026-09-17. JWT sin rol de menú HIS = `diferido(acceso)` (Identity local solo
`admin`). NFR escritura Llamar **p95 medido** 2026-09-17 (20/20 **200**). Dos
actores sobre `id_turno=7` **medido**. Volumen = fixture, no prod →
`diferido(perf-volumen)`.

## Gate UI (N/A — no hay xhtml nuevo)

Geometría, copy, validaciones e interacción de las pantallas del viaje ya están
cobradas en los slugs T5 / G1 / cola / B.1 / display / `cu-llamar-atencion-medica` E3.
Este corte no abre inventario propio.

## Capacidades legacy

| Capacidad | Decisión |
|-----------|----------|
| Otorgar `LIBRE` → `OTORGADO` (T5) | consume slug ya cobrado; evidencia en este viaje |
| AGI confirmar → `RECEPCIONADO` + Cola B | consume G1; evidencia en este viaje |
| Llamar / TV del mismo ticket conferido | consume `cola-b-llamar` (`POST /api/v1/recepcion/espera-amb/{id}/llamar`); **no** CU-B.1 Cola A |
| Purga cola 6 h · apaga TV 1 h · vigencia hab | `diferido(relevamiento-procesos-programados)` |
| Ticket del tramo G1-D | `diferido` al corte de ticket AGI hasta F0 |

## Viaje Playwright

| Viaje | ¿Acto de usuario? | Decisión | Spec / nota |
|-------|-------------------|----------|-------------|
| Otorgar T5 → AGI → Cola B → Llamar → TV | sí (gear lista clínica) | **e2e-migrado** | `Hospital-Web/e2e/e2e-mostrador.spec.ts` · `E2E_MOSTRADOR_LIVE=1` · **1 passed (13.9s)** 2026-09-16 · `turno=6 ticket=T 0006 colaB=5010 recepAmb=11` (tanda previa turno=5 / 5009 vigente) |
| `turnos-agenda.spec.ts` (mocks) | sí | **N/A** este corte | no cierra fuente #1 |
| Legacy HIS | — | **N/A** | sin fixture Oracle de este viaje |

## Ledger de evidencia

Canon: [`regla-evidencia-ejecutable.md`](../../../canon/regla-evidencia-ejecutable.md).

| # | Afirmación | Clase | Artefacto registrado | Ref | Verdicto |
|---|-----------|-------|----------------------|-----|----------|
| 1 | Flyway V48 local = `ts.doc_requerido` | Build | `flyway_schema_history` version `48` checksum `1730637948` description `ts doc requerido`; Api `:8081` | 2026-09-16 | **verificado** |
| 2 | Identity `:8080` y Api `:8081` UP | Endpoint | `GET /q/health` → **200** `{status: UP}` en ambos (observado 2026-09-16) | 2026-09-16 | **verificado** |
| 3 | Viaje T5 → AGI → Cola B → Llamar → TV contra `hospital_api` | e2e | `E2E_MOSTRADOR_LIVE=1 npx playwright test e2e/e2e-mostrador.spec.ts --workers=1` → **1 passed (13.3s)** · log `turno=5 ticket=T 0005 colaB=5009 recepAmb=9` · TV `display-llamados` PACIENTE/Demo | 2026-09-16 | **verificado** |
| 4 | `ts.turno` id=5 nació por T5 y quedó `RECEPCIONADO` | escritura | `SELECT id_turno, estado_turno, id_paciente FROM ts.turno WHERE id_turno = 5` → `5 \| RECEPCIONADO \| 20001`. id=4 de la tanda previa **no** se borra | 2026-09-16 | **verificado** |
| 5 | El mismo turno entra a Cola B | escritura | `SELECT id_cola_espera_serv_amb, id_paciente, id_turno, id_recep_amb FROM ts.cola_espera_serv_amb WHERE id_cola_espera_serv_amb = 5009` → `5009 \| 20001 \| 5 \| 9`. Fila **5008** vigente | 2026-09-16 | **verificado** |
| 6 | Llamar / TV del ticket `T 0005` | escritura | `SELECT id_anunciador_paciente, id_anunciador, leyenda_paciente, tipo_atencion, id_cola_espera_serv_amb FROM ts.llamado_anunciador WHERE id_anunciador_paciente = 5` → `5 \| 1001 \| PACIENTE, Demo \| ATENCION_MEDICA \| 5009` | 2026-09-16 | **verificado** |
| 7 | `POST` Llamar sobre `idRecepAmb` no es Cola A | Endpoint | `POST /api/v1/agi/recepciones/5/llamar` → **404** `Recurso no encontrado - recepcion` (handler exige `ts.cola_espera_recep`) | 2026-09-16 | **verificado** |
| 8 | Acceso del viaje (actor sin el rol / sin Bearer) | acceso | Identity `:8080` + Api `:8081` UP. Login `admin` 200. Sin `Authorization` (prueba negativa): `POST /api/v1/turnos/agenda/1/otorgar` **401** (74,4 ms) · `POST /api/v1/agi/recepciones` **401** (4,1 ms) · `POST /api/v1/recepcion/espera-amb/5009/llamar` **401** (1,7 ms). JWT autenticado sin el rol clínico / perfil menú HIS: Identity local no tiene usuario `sin-menu` (solo `admin`/`admin_role`) → `diferido(acceso)` como [`cola-b-llamar`](../cola-b-llamar/) ledger #9 | 2026-09-17 | **verificado** |
| 9 | p95 escritura Llamar + dos actores sobre el mismo `id_turno` | no funcional | Desbloqueo local: `CREATE SEQUENCE ts.sec_id_llamado_anunciador` + `setval=35` (= `MAX(id_anunciador_paciente)`). El dump canónico deja las secuencias en `ts`; el bootstrap de `hospital_api` las tenía solo en `public` (0 en `ts`). Identity `:8080` y Api `:8081` UP. Presupuesto escritura (default regla NFR): p95 ≤ 1,5 s. 20× `POST …/espera-amb/5010/llamar` `{anunciadorId:1001}` Bearer → **20/20 200** (min 3,7 · p50 4,5 · **p95 6,0** · max 35,6 ms; probe previo 2885 ms en frío). Último `llamadoId=56`. Dos POST paralelos 5010 → ambos **200** (`llamadoId` 57 y 58; TV `GET …/anunciadores/1001/llamados` **200** muestra 58). Concurrencia `id_turno=7` (único `LIBRE` 2026-09-16, `FOR UPDATE` en `JdbcTurnosAgendaAdapter`): 2× `POST /turnos/agenda/reservar` mismo cupo, mismo JWT `admin` (Identity local no tiene segundo usuario clínico) → **200** `RESERVADO` (66,0 ms) vs **400** «El Turno que desea otorgar se encuentra ocupado.» (100,9 ms). Luego 2× `POST …/7/otorgar` → **200** `OTORGADO` `idHistTurno=7` (61,2 ms) vs **404** «No se pudo recuperar el turno nro. 7.» (62,3 ms). `SELECT` `ts.turno` id=7 → `OTORGADO` paciente 20001 convenio 5001 prestación 80001. No se borra evidencia 1–6 / 5005–5010 / llamado 5; id=7 queda como fila NFR. | 2026-09-17 | **verificado** |
| 10 | Volumen de orden de magnitud prod en Llamar / grilla T5 | no funcional | Cola B n=**6** (5005–5010) y `ts.turno` 1–7. **Medido en fixture**, no magnitud prod. Sin consulta §4 contra copia Oracle de este corte → `diferido(perf-volumen)`. | 2026-09-17 | **no ejecutado** |
