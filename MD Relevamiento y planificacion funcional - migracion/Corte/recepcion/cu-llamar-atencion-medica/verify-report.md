---
title: Verify — CU Llamar atención médica
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# Verify — Llamar atención médica

**Gate:** **gate-done** 2026-09-16. E3 UI cobrada. Writer 6b GM + NFR medidos;
acceso `diferido(acceso)`. [`cola-b-llamar/`](../cola-b-llamar/).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Llamar cola personal (lista espera AM) | **done** | este SDD E3 + writer `cola-b-llamar` |
| Llamar cola servicio | diferido(`cu-llamar-atencion-medica-ampliar`) | plan |
| Llamar en atención | diferido(`cu-llamar-atencion-medica-ampliar`) | plan / pane visible, Llamar disabled |
| Re-call cabecera | diferido | plan |
| Quitar al cancelar | diferido(Fase 4 ciclo-vida) | ciclo-vida |
| Auto-atender al llamar | diferido(`cu-llamar-atencion-medica-norte`) | plan |
| Norte Referencias / Turnos / Atendidos / HC | diferido(`cu-llamar-atencion-medica-norte`) | botones visibles disabled |
| Gate ambiente tiene anunciador | diferido(`cola-b-llamar`) | POST v1 lleva `anunciadorId` demo 1001 |

## Gate UI (E3)

Inventarios propios: [geometría](inventario-geometria.md),
[copy](inventario-copy-msg.md), [validaciones](inventario-validaciones.md),
[interacción](inventario-interaccion-ui.md).

## Viaje Playwright

| Viaje | ¿Acto de usuario? | Decisión | Spec / nota |
|-------|-------------------|----------|-------------|
| Otorgar T5 → AGI → Cola B → gear Llamar → TV | sí | **e2e-migrado** | `Hospital-Web/e2e/e2e-mostrador.spec.ts` · `E2E_MOSTRADOR_LIVE=1` · **1 passed (13.9s)** 2026-09-16 · `turno=6 ticket=T 0006 colaB=5010 recepAmb=11` |
| POST Llamar sin pantalla | no | **N/A** este gate | cerrado en `cola-b-llamar` (Clarify B); E3 lo sustituye en el viaje |

## Ledger de evidencia

Canon: [`regla-evidencia-ejecutable.md`](../../../canon/regla-evidencia-ejecutable.md).

| # | Afirmación | Clase | Artefacto registrado | Ref | Verdicto |
|---|-----------|-------|----------------------|-----|----------|
| 1 | Identity `:8080` y Api `:8081` UP | Endpoint | `GET /q/health` → **200** en ambos (observado 2026-09-16 15:34) | 2026-09-16 | **verificado** |
| 2 | Vitest menú + copy E3 | Test | `npx ng test --no-watch --include='**/sidebar-nav.config.spec.ts' --include='**/espera-atencion-labels.spec.ts'` → **2 files / 12 tests passed** | 2026-09-16 | **verificado** |
| 3 | Viaje UI gear→LLAMAR PACIENTE→confirm→POST→TV | e2e | `E2E_MOSTRADOR_LIVE=1 npx playwright test e2e/e2e-mostrador.spec.ts --workers=1` → **1 passed (13.9s)** · log `turno=6 ticket=T 0006 colaB=5010 recepAmb=11` · TV `display-llamados` PACIENTE/Demo | 2026-09-16 | **verificado** |
| 4 | `POST` Llamar desde la pantalla | Endpoint | Playwright `waitForResponse` `POST …/espera-amb/5010/llamar` **ok** (2xx) en la tanda del e2e | 2026-09-16 | **verificado** |
| 5 | `ts.turno` id=6 nació por T5 y quedó `RECEPCIONADO` | escritura | `SELECT id_turno, estado_turno, id_paciente FROM ts.turno WHERE id_turno = 6` → `6 \| RECEPCIONADO \| 20001`. Filas 1–5 **no** se borran | 2026-09-16 | **verificado** |
| 6 | El mismo turno entra a Cola B | escritura | `SELECT id_cola_espera_serv_amb, id_paciente, id_turno, id_recep_amb, cola_espera FROM ts.cola_espera_serv_amb WHERE id_cola_espera_serv_amb = 5010` → `5010 \| 20001 \| 6 \| 11 \| CLINICA MEDICA` | 2026-09-16 | **verificado** |
| 7 | Llamar clínico del ticket `T 0006` | escritura | `SELECT id_anunciador_paciente, id_anunciador, leyenda_paciente, tipo_atencion, id_cola_espera_serv_amb, llamar FROM ts.llamado_anunciador WHERE id_anunciador_paciente = 6` → `6 \| 1001 \| PACIENTE, Demo \| ATENCION_MEDICA \| 5010 \| N` (N = consume-on-read del GET TV) | 2026-09-16 | **verificado** |
| 8 | Geometría / copy / validaciones / interacción | Geometría / copy | inventarios E3 en este folder vs `listaEsperaAtencionMedica.xhtml` | 2026-09-16 | **verificado** |
| 9 | Acceso (actor sin el rol) | acceso | owner `cola-b-llamar` TSK-c4-acceso — IT `llamar_jwtSinRolAdmin_alcanzaElComando` → **404** (no 403); `diferido(acceso)` emisor Identity | 2026-09-16 | **verificado** |
| 10 | Presupuesto no funcional | no funcional | owner `cola-b-llamar` TSK-c4-nfr — p95 Cola B **7,3 ms** (n=20) ≤ B.1 **59,9 ms** (n=3); 2 POST 5008 ambos **200**, 1 fila vigente; volumen 6 filas centro 1001 | 2026-09-16 | **verificado** |
| 11 | Golden master `f_set_paciente_anun_cola` | Golden master | owner `cola-b-llamar` TSK-c1-gm — 6 casos replay PASS (`SetPacienteAnunColaEngineGoldenMasterTest`) | 2026-09-16 | **verificado** |

## Resultado

E3 **cobrado**: ruta `/ambulatoria/espera-atencion`, menú ATENCION MEDICA, acto HIS
(engranaje → LLAMAR PACIENTE → confirm → TV). AGI persiste `cola_espera='CLINICA MEDICA'`
(VARCHAR 20 del servicio); la lista **no** filtra el literal `ATENCION_MEDICA`.
