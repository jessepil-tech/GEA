---
title: Tasks — Apagar ANUNCIADOR Node
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.apagar-anunciador-node
---

# Tasks — Apagar Node

## Ops / Clarify

1. [x] **TSK-ops-1** Responder Clarify README (ocupación / logos / TTS) — **2026-08-18: N1+N2+N3 obligatorios**
2. [x] **TSK-ops-2** Inventario despliegues — [inventario-despliegues-node.md](inventario-despliegues-node.md)
3. [x] **TSK-ops-3** Waivers W-NODE-1..3 firmados en [verify-report.md](verify-report.md)

## N0 — Baseline

4. [x] **TSK-ops-n0-1** Smoke Node detenido — `Hospital-Api/tools/smoke-node-down-anunciador.sh` **PASS**
5. [x] **TSK-ops-n0-2** URLs rotas Vue+Node down — documentadas en inventario

## N1 — Ocupación

6. [x] **TSK-app-n1-1** Modelo PG + seed ocupación — Flyway `V21__anunciador_ocupacion_ambientes.sql`
7. [x] **TSK-app-n1-2** `GET /api/v1/anunciadores/{id}/ocupacion-ambientes` (+ IT Bearer/API Key)
8. [x] **TSK-app-n1-3** Push WS `ocupacion-ambientes-cambio` (snapshot onOpen + scheduler 5m) + poll HTTP fallback Web
9. [x] **TSK-app-n1-4** UI Hospital-Web display compuesto (`/display/anunciadores/:id` si `mostrarProfOcupaAmb`)

## N2 — Assets

10. [x] **TSK-app-n2-1** Estrategia: GET Api data-URI (paridad Node) + fallback estático Web
11. [x] **TSK-app-n2-2** Flyway V22 + `GET …/logo` + `GET …/img-fondo` + display Web

## N3 — Diccionario

12. [x] **TSK-app-n3-1** Port diccionario + flag voz + TTS Web (SpeechSynthesis; sin ResponsiveVoice SaaS)

## N4–N5 — Corte

13. [x] **TSK-ops-n4** Config Web sin `API_ANUNCIADOR` (solo Identity+Api); Vue marcado DEPRECATED
14. [x] **TSK-ops-n5** [runbook-stop-node.md](runbook-stop-node.md) + [verify-report.md](verify-report.md) **PASS** (dev)
15. [x] **TSK-ops-doc** Relevamiento matriz §7 → apagado done (dev)
