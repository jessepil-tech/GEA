---
title: Tasks — T5 Turnos agenda / otorgar
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-otorgar
---

# Tasks — T5

1. [x] **TSK-ops-g0-1** Abrir SDD + links relevamiento/cortes/T4 — **2026-09-07**
2. [x] **TSK-ops-g0-2** Clarify **FIRME** en [spec.md](spec.md) (filas 1–16 + D-TUR-28) — **2026-09-07**
3. [x] **TSK-ops-g0-3** Inventario copy — [`inventario-copy-msg.md`](inventario-copy-msg.md) **2026-09-07**
3b. [x] **TSK-ops-g0-5** Inventario interacción UI — [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md) **2026-09-07** (corrige botones inline → menú gear)
4. [x] **TSK-ops-g0-4** Inventario validaciones — [`inventario-validaciones.md`](inventario-validaciones.md) **2026-09-07**
5. [x] **TSK-app-g1-1** Inventario gaps DDL — [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md) **2026-09-07**
5b. [x] **TSK-app-g1-2** Flyway V46 seed motivo `LIBERACION_TURNO` · `ctrl_turnos_pac` **diferido** — **2026-09-07**
6. [x] **TSK-app-g2-1** Golden lectura + `TurnosAgendaPort` grilla día + días disponibles (serv+pers) — **2026-09-07**
7. [x] **TSK-app-g2-2** API GET grilla + días + IT — **2026-09-07**
8. [x] **TSK-app-g3-1** Port reserva + **tomar (opción A)** + expirar 3/30 min + API `POST …/tomar` · `POST …/expirar-reservas` + IT — **2026-09-07** (inhibe JDBC diferido: sin `inhab_tur_*` PG)
9. [x] **TSK-app-g4-1** Port otorga + libera + sobreturno + hist + API + IT — **2026-09-07** (libera sin unificación slots adyacentes v1; tope sobreturno diferido)
10. [x] **TSK-web-g5-0** **Gate UI arranque** (skill `gate-ui-arranque`): xhtml + geometría DoD + inventarios copy/validaciones **antes** de template Angular — **2026-09-07**
10b. [x] **TSK-web-g5-1** UI `/turnos/agenda` (filtros + calendario + grilla + acciones) · use-case · menú TURNOS (hoja Turnero) — **2026-09-07**
10c. [x] **TSK-web-g5-2** Dialogs: infoTurno (otorgar + **poll ~59 s → tomar**) · motivo libera · buscadores prest/prof — **2026-09-07** (paciente/convenio buscador → ficha mínima v1)
11. [x] **TSK-ops-g6-1** Smoke stack real (Identity+Api+Web; Reports N/A salvo hijo T7) — **PASS 2026-09-08**
12. [x] **TSK-web-e2e** Playwright 3 viajes (consultar / reservar+otorgar / liberar) — **2026-09-07** · `Hospital-Web/e2e/turnos-agenda.spec.ts`
13. [x] **TSK-ops-g6-2** Verify PASS + matriz/pipeline/backlog/mapa-menu — **2026-09-07**

## Gate UI (G5) — xhtml

Geometría DoD (agenda.xhtml): north tabla **4 cols** `200px | 40% | 200px | 40%` — Paciente|Estado, Convenio|Plan, Centro|Servicio, Profesional|Equipo (equipo disabled D-TUR-17), Prestación `colspan 3` + cod 150px; west **260px** calendario; center grilla + filtro estado header; **col acciones `width="24"` menú gear** (`p:tieredMenu`); south leyenda 6 estilos.

| Path | Rol |
|------|-----|
| `…/asignacionTurnos/agenda.xhtml` | Filtros north + calendario + grilla + **menú gear fila** |
| `…/asignacionTurnos/asignacionTurnos.xhtml` | West turnero + popups |
| `…/asignacionTurnos/infoTurno.xhtml` | Otorgar + **`p:poll` 59 s → tomar** |
| Buscadores agenda | Paciente / convenio / prestación / personal — no copiar HAB si el popup difiere |

## Viaje Playwright (Clarify #15)

| Viaje | Decisión | Spec (G6) |
|-------|----------|-----------|
| Consultar grilla día | e2e-migrado | `Hospital-Web/e2e/turnos-agenda.spec.ts` |
| Reservar + otorgar | e2e-migrado | mismo |
| Liberar | e2e-migrado | mismo |
| Legacy HIS | no | sin fixture Oracle |
