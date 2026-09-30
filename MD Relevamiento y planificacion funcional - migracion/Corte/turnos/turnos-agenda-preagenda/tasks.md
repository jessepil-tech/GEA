---
title: Tasks — T5.6 pre-agenda turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.turnos-agenda-preagenda.tasks
---

# Tasks

1. [x] **TSK-ops-g0-1** Clarify **FIRME** Camino 1 — 2026-09-15 (Francisco «ok»)
2. [x] **TSK-ops-g0-2** Inventarios G0 — 2026-09-15 (docs; Gate UI código **después** FIRME)
3. [x] **TSK-app-g1-1** Flyway `ts.pre_agenda_turno` IF NOT EXISTS — V54
4. [x] **TSK-app-g2-1** Query lista PENDIENTE + IT validación (JDBC `ts.pre_agenda_turno`)
5. [x] **TSK-app-g2-2** Otorgar T5: `idPreAgendaTurno` opcional → UPDATE OTORGADO
6. [x] **TSK-web-g3-0** Gate UI north+tabla **antes** de template — geometría `preAgendaTurnos.xhtml`
7. [x] **TSK-web-g4-1** Turnero live + vista preagenda + Asignar → Agenda
8. [x] **TSK-web-e2e** Viajes: abre vista; toast fechas; Consultar filas; Asignar — PASS 2026-09-15
9. [x] **TSK-ops-g6-1** Smoke Francisco — 2026-09-15 «ok pre agenda se ve bien» (seed V55 70001)
10. [x] **TSK-ops-g6-2** Verify PASS + gobierno

## Gate UI (G3) — xhtml

| Path | Rol | Geometría DoD |
|------|-----|----------------|
| `asignacionTurnos.xhtml` L89–91 | Turnero `pre_agenda_turnos` | accordion 170; ítem live + highlight |
| `preAgendaTurnos.xhtml` L16–34 | Paciente + lupa + limpiar | `InputWid100`; lupa; closethick |
| L35–59 | Servicio 111px/250px; Convenio 111/250; Plan (label HIS `convenio`) 111/250 | `InputWid100` filter contains |
| L61–76 | Fecha desde/hasta 150px | calendar `dd/MM/yy` |
| L78–82 | Consultar | **D-TUR-65:** fila propia centrada (`btnConsultarRow`), no inline HIS |
| L89–123 | Tabla scroll 100%; header `pre_agenda_turnos`; cols HIS; acciones 60px overlay **ASIGNAR TURNO** | sin pager; empty `no_se_encontraron_registros` |
| — | South / Imprimir / Excel | **N/A** (HIS no tiene) |
| `agenda.xhtml` | Post-Asignar | reusa T5; calendario 260 **sí** al volver Agenda |

**Prohibido:** fork ruta; template antes de geometría; confundir con `consultaPreagenda.xhtml`.
