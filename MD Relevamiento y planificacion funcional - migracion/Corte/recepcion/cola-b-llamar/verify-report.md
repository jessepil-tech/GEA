---
title: Verify — Llamar fila Cola B
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cola-b-llamar
---

# Verify — `cola-b-llamar`

**Gate:** C4 medido. Clarify **#4 = B** firmado. GM PASS. Acceso: IT 404 +
`diferido(acceso)`. NFR 2026-09-16: p95 Cola B **7,3 ms** (n=20) ≤ B.1 **59,9 ms**
(n=3 pendientes). Volumen fixture **6** filas centro 1001 (no magnitud prod).
Dos POST 5008 → ambos **200**; 1 fila vigente (delete previos). Sin botón
espera-amb.

## Gate UI

C4=**B** (solo API): **N/A** — no hay xhtml nuevo. Geometría, copy, validaciones
e interacción de `/recepcion/espera-amb` ya cobradas en M4. Disparador Llamar =
`POST /api/v1/recepcion/espera-amb/{id}/llamar` (e2e live; UI clínica E3 en el
hermano).

## Capacidades legacy

| Capacidad | Decisión |
|-----------|----------|
| Llamar fila `cola_espera_serv_amb` → TV | este corte; **done** (API + e2e live) |
| Menú M5 Cola B | `diferido` (otro corte) |
| Llamar Cola A | **N/A** (B.1 gate-done) |
| Cola servicio / en atención / cabecera | `diferido(cu-llamar-atencion-medica)` |
| Jobs purga / Llamador | `diferido(relevamiento-procesos-programados)` |
| `AtencionAutomaticaColaEsperaAmbJob` | **N/A** este corte |
| Golden master `f_set_paciente_anun_cola` | **PASS** (6 casos; ver ledger #11) |
| Perfil→menú del acto (lista espera AM) | `diferido(acceso)` — Identity emisor `menu:KEY` |

## Viaje Playwright

| Viaje | ¿Acto de usuario? | Decisión | Spec / nota |
|-------|-------------------|----------|-------------|
| Llamar Cola B → TV | API (C4=B) + display | **e2e-migrado** | `e2e-mostrador.spec.ts` live · **1 passed** 2026-09-16 (13.3s) · `turno=5 ticket=T 0005 colaB=5009 recepAmb=9` |
| Menú M5 | sí | **N/A** este corte | — |
| Legacy HIS | — | **N/A** | sin fixture Oracle |

## Ledger de evidencia

Canon: [`regla-evidencia-ejecutable.md`](../../../canon/regla-evidencia-ejecutable.md).

| # | Afirmación | Clase | Artefacto registrado | Ref | Verdicto |
|---|-----------|-------|----------------------|-----|----------|
| 1 | Universo: menú Cola B no incluye Llamar; el acto es lista espera médica | e2e | `colaEspera.xhtml` menuitems (CI, reemplazar, derivar, anular, print, etiqueta) · `BBListaEsperaAtencionMedica.actionBtnLlamarPacienteColaEsperaPersonal` | 2026-09-16 | **verificado** |
| 2 | Techo: semilla recepción `colaEspera.xhtml` reportes 7/5 EXCEDE — no se firma | e2e | `./tools/indice-legacy.sh --semilla HOSPITAL_2/WebRoot/pages/recepcion/recepcionPaciente/colaEspera.xhtml` | 2026-09-16 | **verificado** |
| 3 | `POST /api/v1/recepcion/espera-amb/{id}/llamar` sin Bearer | Endpoint | `POST …/espera-amb/5008/llamar` → **401** (host :8081, 2026-09-16) · IT `llamar_withoutBearer_returnsUnauthorized` → **401** | 2026-09-16 | **verificado** |
| 4 | POST Llamar id inexistente | Endpoint | IT `llamar_inexistente_returns404` → **404** `colaEsperaServAmb` | 2026-09-16 | **verificado** |
| 5 | IT publica el llamado en ANU-DEMO | Endpoint | `RecepcionEsperaAmbResourceIT` 8 tests 0 fail (12:58) · POST cola B **5001** + GET `/api/v1/anunciadores/1001/llamados` → **200** `PACIENTE, Demo` | 2026-09-16 | **verificado** |
| 6 | Viaje live T5→AGI→Cola B→Llamar→TV | e2e | `E2E_MOSTRADOR_LIVE=1 npx playwright test e2e/e2e-mostrador.spec.ts --workers=1` → **1 passed (13.3s)** · log `turno=5 ticket=T 0005 colaB=5009 recepAmb=9` · TV `display-llamados` PACIENTE/Demo | 2026-09-16 | **verificado** |
| 7 | Escritura `ts.llamado_anunciador` de la fila 5009 | escritura | `SELECT id_anunciador_paciente, id_anunciador, leyenda_paciente, tipo_atencion, id_cola_espera_serv_amb, llamar FROM ts.llamado_anunciador WHERE id_anunciador_paciente = 5` → `5 \| 1001 \| PACIENTE, Demo \| ATENCION_MEDICA \| 5009 \| N` (N = consume-on-read del GET/TV) | 2026-09-16 | **verificado** |
| 8 | Padres del viaje no se borran | escritura | `ts.turno` id=4 y id=5 `RECEPCIONADO` 20001; cola B **5008** (turno 4) y **5009** (turno 5, recep_amb 9) vigentes | 2026-09-16 | **verificado** |
| 9 | Acceso: actor autenticado sin el rol clínico (perfil→menú HIS) | acceso | IT `llamar_jwtSinRolAdmin_alcanzaElComando` (`TestSecurity` user=`sin-menu` roles=`user_role`) POST `…/espera-amb/999999/llamar` → **404** (no 403; no INSERT). Recurso `@Authenticated` sin `RolesAllowed`. `f_set` sin `rol_funcional`. Paridad menú lista espera AM = `diferido(acceso)` → [`destino-seguridad-identity.md`](../../../planificacion/destino-seguridad-identity.md) paso 2 | 2026-09-16 | **verificado** |
| 10 | p95 / volumen / dos actores sobre el mismo id de cola B | no funcional | Identity `:8080` + Api `:8081` UP. Volumen: `COUNT(*) cola_espera_serv_amb id_centro_ate=1001` → **6** (fixture e2e; no magnitud prod — `medido en fixture`). GET `…/espera-amb?idCentroAte=1001` → **200** n=6 **264 ms** (1ª). Tiempo: 20× POST `…/espera-amb/5010/llamar` **200** p95 **7,3 ms** (min 4,3 max 32,9). B.1: 3 pendientes (4002/4003/4005) POST `…/recepcion/colas/2001/llamar` **200** p95 **59,9 ms** (n=3; throttle 5 s; no hay 20 pendientes). Objetivo ≤ B.1 **cumple**. Concurrencia: 2 POST paralelos cola **5008** ambos **200** (27,7 ms / 8,6 ms); `COUNT` llamados 5008 = **1** (id=32 `llamar=S`) — `deletePreviosMismoServAmb`, no `FOR UPDATE`. Re-llamar 5010 **reemplazó** llamado id=6; id=5 (5009) y turnos 1–6 / colas 5005–5010 vigentes. | 2026-09-16 | **verificado** |
| 11 | Golden master `ATENCION.f_set_paciente_anun_cola` vs Oracle 11.2 | golden master | 6 casos: 3 errores capturados JDBC 11.2.0.4 (SELECT INTO + `raise_application_error`, sin CALL/INSERT; ids −1 / 0 / NULL) + 3 mapeos sintéticos BODY 10.7.1 (centro-arg ignorado, paciente NULL, servicio/centro NULL). Replay: `mvn -pl core test -Dtest=SetPacienteAnunColaEngineGoldenMasterTest`. Fixture `Hospital-Api/core/src/test/resources/golden-master/f_set_paciente_anun_cola.json`. Bordes: NULL, cero, missing. Fecha n/a. Resultado replay **PASS** (6/6). | 2026-09-16 | **verificado** |
