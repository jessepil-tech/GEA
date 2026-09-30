---
title: Inventario copy — T5.5 consulta agenda
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.turnos-agenda-consultas.copy
---

# Inventario copy — Consulta Agenda (G0)

Fuente: `Resources.properties` + `MessageBundle`.  
Web: extender `turnos-agenda-labels.ts` (no inventar).

| msg.key / constante | Valor | En UI este corte |
|---------------------|-------|------------------|
| `consulta_agenda` | Consulta Agenda | ítem Turnero + highlight |
| `centro_atencion` | Centro Atención | north |
| `servicio` | Servicio | north |
| `profesional` | Profesional | north |
| `equipo` | Equipo | north; combo disabled |
| `estado` | Estado | north |
| `convenio` | Convenio | north |
| `buscar` | Buscar | title lupa |
| `fecha_desde` | Fecha Desde | north (ya T5 west) |
| `fecha_hasta` | Fecha Hasta | north |
| `hora_desde` | Hora Desde | north |
| `hora_hasta` | Hora Hasta | north |
| `incluye_sobreturnos` | Incluye Sobreturnos | checkbox |
| `consultar` | Consultar | botón |
| `turnos` | Turnos | header tabla |
| `informacion_turno` | (title info) | col 24px |
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
| `plan_convenio` | Plan Convenio | col |
| `telefono_paciente` | Teléfono Paciente | col |
| `correo_paciente` | Correo Paciente | col |
| `reservado` / `sobreturno` / `cancelado` / `reemplazado` / `inhibido` | (T5) | footer leyenda |
| `no_se_encontraron_registros` | (T5) | empty |
| `exportar_excel` | Exportar Excel | south **disabled** (hijo) |
| `imprimir` | Imprimir | south **live** |
| `WRONG_INTERVAL_DATE_3` | La fecha hasta debe ser posterior a la fecha desde | toast |
| `WRONG_INTERVAL_DATE_9` | La fecha seleccionada no puede ser anterior a la actual. | toast |
| `LIBRE` / `OTORGADO` / Todos | combo estado | HIS `STRING_TODOS_COMBO` |
