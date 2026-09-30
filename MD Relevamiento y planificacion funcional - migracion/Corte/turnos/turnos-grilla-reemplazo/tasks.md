---
title: Tasks — T6.5 · reemplazo profesional de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 D-TUR-74 (Francisco 2026-09-21)
2. [x] **TSK-ops-g0-2** Inventarios + reservas/fixture + spec/plan/verify (2026-09-21)
3. [x] **TSK-ops-g0-3** COUNT `ts.turno` rango G6 (acto T4, no seed) — 2026-09-21 centro 1001 serv 10 pers 90001: **2** (`17255210` OTORGADO · `17255283` LIBRE)
4. [x] **TSK-ops-g0-4** Gate UI: xhtml + inventarios medidos **antes** de template
5. [x] **TSK-app-g2-1** GET lista JDBC (`f_get_grilla_personal_fechas` rediseñada)
6. [x] **TSK-app-g2-2** POST reemplazar (ids + motivo + horas) port `f_reemplazar_personal`
7. [x] **TSK-app-g2-3** POST quitar (ids; personal/motivo null)
8. [x] **TSK-app-g2-4** Golden + rollback + dos actores (engine, sin FOR UPDATE)
9. [x] **TSK-web-g4-1** Página reemplazo + menú TURNOS / Agenda turnos
10. [x] **TSK-web-g5-1** e2e-migrado 4 viajes (stub CI; ≠ G6)
11. [x] **TSK-ops-g6-1** Smoke Francisco (LIBRE → reemplazado → nulos) — 2026-09-21 también OTORGADO, solape, parcial+OTORGADO, hist
12. [x] **TSK-ops-g6-2** Verify PASS + gobierno (`verificar-sdd.sh turnos-grilla-reemplazo`)

## Gate UI

| Path | Rol | DoD |
|------|-----|-----|
| `reemplazoPersonalGrillaTurnos.xhtml` | Pantalla | North T4, dos lupas personal, motivo, tabla selección, footer v1.19, south 3×150px |
| `hospital-menu.catalog.ts` folder Agenda turnos | Menú | Leaf junto a Generar/Eliminar/Suspender/Quitar |

**Prohibido:** fork `/turnos/agenda`; T6 padre entero; seed de `turno`; portar vencidos/mail; clonar BODY GENERAL; `obsTableWrap` T4 para la leyenda.
