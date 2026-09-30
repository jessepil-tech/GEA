---
title: Cortes — Turnos
status: active
last_updated: 2026-09-22
---

# Turnos

Orden T0–T7: [`relevamiento-turnos/cortes.md`](../../relevamiento/relevamiento-turnos/cortes.md).  
Estado del gate: [`estado-piloto-vs-general.md`](../../estado/estado-piloto-vs-general.md). Esta carpeta **no** se ordena por gate-done.

T6 padre [`turnos-ciclo-vida/`](turnos-ciclo-vida/) **gate-done**. T6.4 [`turnos-grilla-suspender/`](turnos-grilla-suspender/) **gate-done**. T6.5 [`turnos-grilla-reemplazo/`](turnos-grilla-reemplazo/) **gate-done**. T7 [`turnos-avisos/`](turnos-avisos/) **gate-done** D-TUR-76. T5-print [`turnos-agenda-imprimir-turno/`](turnos-agenda-imprimir-turno/) **gate-done** D-TUR-77.

## SDD

| T | Slug | Qué es |
|---|------|--------|
| T1 | [`turnos-maestros-personal/`](turnos-maestros-personal/) | Maestros identidad turno |
| T2 | [`turnos-config-hab-horarios/`](turnos-config-hab-horarios/) | Habilitación |
| T3 | [`turnos-horarios-grupos/`](turnos-horarios-grupos/) | Grupos + horarios |
| T4 | [`turnos-generacion-grilla/`](turnos-generacion-grilla/) | Generación de grilla |
| T5 | [`turnos-agenda-otorgar/`](turnos-agenda-otorgar/) | Reservar / otorgar / liberar |
| T5.1 | [`turnos-agenda-ficha-paciente/`](turnos-agenda-ficha-paciente/) | Ficha paciente + west |
| T5.1b | [`turnos-agenda-info-popups/`](turnos-agenda-info-popups/) | Popups north |
| T5.1c | [`turnos-agenda-elegibilidad-cobros/`](turnos-agenda-elegibilidad-cobros/) | Elegibilidad + doc req |
| T5.1d | [`turnos-agenda-cobros/`](turnos-agenda-cobros/) | Cobros agenda |
| T5.1e | [`turnos-agenda-info-turno/`](turnos-agenda-info-turno/) | Chrome infoTurno |
| T5.1e-p | [`turnos-agenda-info-turno-persist/`](turnos-agenda-info-turno-persist/) | Persist obs / fecha |
| T5.1e-q | [`turnos-agenda-info-turno-prest/`](turnos-agenda-info-turno-prest/) | Prep / requisitos |
| T5.2 | [`turnos-agenda-sobreturno/`](turnos-agenda-sobreturno/) | Sobreturno |
| T5.3 | [`turnos-agenda-repetidos/`](turnos-agenda-repetidos/) | Turnos repetidos |
| T5.3-b | [`turnos-agenda-repetidos-cambiar/`](turnos-agenda-repetidos-cambiar/) | Cambiar horario repetidos |
| T5.4 | [`turnos-agenda-multiples/`](turnos-agenda-multiples/) | Turnos múltiples |
| T5.4-b | [`turnos-agenda-multiples-cambiar/`](turnos-agenda-multiples-cambiar/) | Cambiar horario múltiples |
| T5.5 | [`turnos-agenda-consultas/`](turnos-agenda-consultas/) | Consulta Agenda Turnero |
| T5.5-pdf | [`turnos-agenda-consultas-pdf/`](turnos-agenda-consultas-pdf/) | PDF Consulta Agenda |
| T5-print | [`turnos-agenda-imprimir-turno/`](turnos-agenda-imprimir-turno/) | PDF turno gear Agenda (**gate-done** D-TUR-77) |
| T5.5-excel | [`turnos-agenda-consultas-export/`](turnos-agenda-consultas-export/) | Excel Consulta Agenda |
| T5.6 | [`turnos-agenda-preagenda/`](turnos-agenda-preagenda/) | Pre-agenda Turnero |
| T6.1 | [`turnos-agenda-reasignar/`](turnos-agenda-reasignar/) | Reasignar desde agenda |
| T6.2 | [`turnos-agenda-cola-reasignar/`](turnos-agenda-cola-reasignar/) | Cola accordion Reasignación (**gate-done**) |
| T6.2-print | [`turnos-agenda-cola-reasignar-print/`](turnos-agenda-cola-reasignar-print/) | PDF south cola Reasignación (**gate-done**) |
| T6.2-excel | [`turnos-agenda-cola-reasignar-export/`](turnos-agenda-cola-reasignar-export/) | Excel south cola Reasignación (**gate-done**) |
| T6.3 | [`turnos-agenda-historial/`](turnos-agenda-historial/) | Historial accordion (`historialTurno.xhtml`) (**gate-done**) |
| T6.3-excel | [`turnos-agenda-historial-export/`](turnos-agenda-historial-export/) | Excel south Historial (**gate-done**) |
| T6.4 | [`turnos-grilla-suspender/`](turnos-grilla-suspender/) | Suspender / quitar suspensión grilla (**gate-done**) |
| T6.5 | [`turnos-grilla-reemplazo/`](turnos-grilla-reemplazo/) | Reemplazo profesional grilla (**gate-done**) |
| T6 | [`turnos-ciclo-vida/`](turnos-ciclo-vida/) | Migrar turno vencido (job; **gate-done** Camino 1) |
| T7 | [`turnos-avisos/`](turnos-avisos/) | Cola `mensaje_turno` + MailTurnoJob (**gate-done** Camino 1) |
| — | [`turnos-hab-buscadores/`](turnos-hab-buscadores/) | Buscadores de habilitación |
| — | [`turnos-hab-buscadores-filtros/`](turnos-hab-buscadores-filtros/) | Filtros de esos buscadores |

## Parcial

Material de trabajo, no el set SDD completo.

| T | Slug | Qué hay |
|---|------|---------|
| T4 hijo | [`turnos-consulta-agendas-drilldown/`](turnos-consulta-agendas-drilldown/) | `tasks.md` |
| T4 hijo | [`turnos-consulta-agendas-imprimir/`](turnos-consulta-agendas-imprimir/) | Flujos + verify, sin spec |

## Pagarés

Solo README: hijo diferido o compromiso. No son el tablero diario.

| T | Slug | Nota |
|---|------|------|
| T3 hijo | [`turnos-horarios-especiales/`](turnos-horarios-especiales/) | Diferido |
| T3 hijo | [`turnos-horarios-inhibiciones/`](turnos-horarios-inhibiciones/) | Diferido |
| T3 hijo | [`turnos-horarios-pers-shell/`](turnos-horarios-pers-shell/) | Diferido |
| — | [`turnos-consultas-operador/`](turnos-consultas-operador/) | Consultas menú (no mezclar con T5.5) |
| — | [`turnos-consultas-preagenda/`](turnos-consultas-preagenda/) | Menú `consultaPreagenda` |
