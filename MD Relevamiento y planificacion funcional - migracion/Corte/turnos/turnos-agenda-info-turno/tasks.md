---
title: Tasks — T5.1e chrome infoTurno
version: 0.1.0
status: gate-done
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno.tasks
---

# Tasks

## Gate UI (xhtml)

- Path: `Hospital-Legacy/HOSPITAL_2/WebRoot/pages/turnos/asignacionTurnos/infoTurno.xhtml`
- Dialog padre: `asignacionTurnos.xhtml` L274 `width=1200 height=520` header `informacion_turno`
- Camino un-turno L11–163 + L220–275 + footer L280–295 (no L164–218 repetidos)

## Geometría

- Paciente: una tabla 8 cols; nombre colspan 3; sexo/fecha/convenio/plan segunda fila
- Turno: **una** tabla 6 cols (Fecha alineada con Centro y Fecha Prescripción)
- `InputWid100` = 100% celda; disabled `#dadada`
- `p:dialog` 1200×520 = **cuerpo**; titlebar extra; sin scroll del modal
- Derecha rowspan: obs 450×116, prep 450×260 (al lado de Datos **y** Req/Doc)
- Abajo: req 360 + doc 360
- Footer 120px centrado; sin X si otorga

1. [x] Inventarios G0
2. [x] Template HIS + labels
3. [x] Inputs ficha / docReq desde agenda
4. [x] e2e paneles visibles
5. [x] G6 smoke ops (Francisco) — **PASS** 2026-09-10

## Viaje Playwright (Clarify #8)

| Viaje | Decisión |
|-------|----------|
| Asignar/sobreturno abre chrome HIS (Datos Paciente + Datos Turno/Sobreturno) | e2e-migrado |
