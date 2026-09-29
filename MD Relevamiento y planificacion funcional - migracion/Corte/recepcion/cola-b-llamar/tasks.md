---
title: Tasks — Llamar fila Cola B
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cola-b-llamar
---

# Tasks — Llamar Cola B

- [x] **TSK-c0-docs** Abrir slug (README Reservas/Fixture/Estado, spec, plan, verify). Índice semilla recepción `colaEspera.xhtml` + acto `listaEsperaAtencionMedica`.
- [x] **TSK-c0-firma** Clarify #4 = **B** (solo API; UI clínica en `cu-llamar-atencion-medica`). Firmado 2026-09-16.
- [x] **TSK-c1-port** Extender `AnunciadorWritePort` + JDBC path `id_cola_espera_serv_amb` / `ATENCION_MEDICA`. Cola A intacta.
- [x] **TSK-c1-gm** Golden master `ATENCION.f_set_paciente_anun_cola` (casos + replay) o puente declarado vs Oracle 11.2.
- [x] **TSK-c2-api** `POST` Llamar por id de Cola B; IT 200/401/404 observados.
- [x] **TSK-c3-ui** C4=B → **N/A** (sin botón en `/recepcion/espera-amb`).
- [x] **TSK-c4-e2e** `E2E_MOSTRADOR_LIVE=1` Llamar→TV. Ledger `id_anunciador_paciente=5` (`cola B 5009`, turno 5).
- [x] **TSK-c4-acceso** Actor con JWT sin el rol; mensaje como contrato (401 sin Bearer ya cubierto). IT `llamar_jwtSinRolAdmin_alcanzaElComando` → **404** (no 403). `diferido(acceso)` emisor Identity.
- [x] **TSK-c4-nfr** p95 + dos actores sobre el mismo `id_cola_espera_serv_amb`.
- [ ] **TSK-f4** No borrar filas de evidencia (`turno` 1–6, cola B 5005–5010, llamado id=5). NFR re-llamar 5010 **reemplazó** llamado id=6 (delete previos mismo serv_amb+anunciador).
