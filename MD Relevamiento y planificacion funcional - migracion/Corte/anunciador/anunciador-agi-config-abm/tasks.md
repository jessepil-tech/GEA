---
title: Tasks — ABM Anunciador + Terminal AG
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-12
phase_id: sdd.hospital.anunciador-agi-config-abm
---

# Tasks — `anunciador-agi-config-abm`

1. [x] **TSK-ops-c0-1** Firma Clarify spec filas 1–10 — **FIRME 2026-09-08** (avance P3)
2. [x] **TSK-ops-c0-2** Abrir SDD + links relevamiento/cierre/backlog — **2026-09-07**
3. [x] **TSK-ops-c0-3** Inventario copy `msg.*` → labels (`inventario-copy-msg.md`) — **2026-09-08**
4. [x] **TSK-ops-c0-4** Inventario validaciones BB/MessageBundle → UI+API create **y** update — **2026-09-08**
4b. [x] **TSK-ops-c0-5** Inventario geometría + interacción (`inventario-geometria.md`, `inventario-interaccion-ui.md`) — **2026-09-08**
5. [x] **TSK-app-c1-1** POST/PUT anunciador (**datos + flags** voz/multimedia/ocupación/tipo llamado/ctd chars/minutos) + vínculo ambientes + PUT logo/fondo + IT — **2026-09-08**
6. [x] **TSK-app-c2-1** POST/PUT/DELETE terminal + opciones + IT (DELETE físico HIS; checkbox `activo` aparte) — **2026-09-08**
7. [x] **TSK-app-c3-1** POST/PUT/DELETE diccionario + IT — **2026-09-08**
8. [x] **TSK-web-c4-1** UI paridad disposición vs xhtml (gate geometría) — anunciador + **config avanzada (flags)** + ambientes — click-through **PASS 2026-09-12** (Buscar clic GET 200 carga `C4-1 FIX`; preview logo data-URI `naturalWidth` 189). Catálogo ambientes vacío = datos PG (`ts.ambiente_amb`), no UI. Residual: JWT vencido no re-inyectado en browser.
9. [x] **TSK-web-c4-2** UI terminal + opciones + diccionario — **2026-09-12** (`/anunciadores/terminales`, `/anunciadores/diccionario`). Diccionario CRUD Playwright PASS (POST/PUT/DELETE). Terminal shell+Buscar+dialog centro PASS; alta/opciones no re-clicadas (0 filas `ts.ambiente_amb` / `ts.recepcion`).
10. [ ] **TSK-ops-c5-1** Smoke: alta **nueva** (no seed) → display + GET terminal — **no ejecutar sin pedido**
11. [ ] **TSK-ops-c5-2** Verify anti-gap + actualizar matriz/maestros/cierre P3/backlog/mapa-menú
