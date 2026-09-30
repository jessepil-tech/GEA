---
title: Inventario copy — T5.5 hijo Excel
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.turnos-agenda-consultas-export.copy
---

# Inventario copy — Excel Consulta Agenda

Fuente: padre [`../turnos-agenda-consultas/inventario-copy-msg.md`](../turnos-agenda-consultas/inventario-copy-msg.md) + `Resources` keys de `generarReporteExcel`.

| msg.key | Valor | Uso |
|---------|-------|-----|
| `exportar_excel` | Exportar Excel | south **live** (este corte) |
| `consulta_agenda` | Consulta Agenda | título hoja / workbook |
| `centro_atencion` | Centro Atención | header + col |
| `servicio` | Servicio | header + col |
| `personal` | Personal | header (HIS usa este key, no «profesional») |
| `fecha_desde` / `fecha_hasta` | Fecha Desde / Hasta | header |
| `hora_desde` / `hora_hasta` | Hora Desde / Hasta | header |
| `estado` | Estado | header |
| `fecha` | Fecha | col |
| `hora_inicio` | Hora de Inicio | col |
| `hora_fin` | Hora Fin | col |
| `tipo_documento` | Tipo Documento | col |
| `nro_documento` | Nro. Documento | col |
| `paciente` | Paciente | col |
| `nro_hc_anterior` | Nro. Historia Clínica Anterior | col |
| `profesional_equipo` | Profesional/Equipo | col |
| `cod_prestacion` | Código Prestación | col |
| `prestacion` | Prestación | col |
| `convenio` | Convenio | col (no header) |
| `plan_convenio` | Plan Convenio | col |
| `telefono_paciente` | Teléfono Paciente | col |
| `correo_paciente` | Correo Paciente | col |

Labels ya en `turnos-agenda-labels.ts` (T5.5). No inventar.
