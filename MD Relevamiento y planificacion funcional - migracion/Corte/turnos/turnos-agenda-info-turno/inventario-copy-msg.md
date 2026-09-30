---
title: Inventario copy — T5.1e infoTurno
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno.copy
---

# Inventario copy — infoTurno (G0)

Fuente: `Resources.properties` + `infoTurno.xhtml`. Web: `turnos-agenda-labels.ts`.

| msg.key | Valor | Constante | Este slice |
|---------|-------|-----------|------------|
| `informacion_turno` | Información Turno | `informacionTurno` | ya — header dialog 1200 |
| `datos_paciente` | Datos del Paciente | `datosDelPaciente` | panel |
| `paciente` | Paciente | `paciente` | ya |
| `tipo_paciente` | Tipo Paciente | `tipoPaciente` | ya — input vacío v1 |
| `sexo` | Sexo | `sexo` | ya |
| `fecha_nacimiento` | Fecha Nacimiento | `fechaNacimiento` | ya |
| `convenio` | Convenio | `convenio` | ya |
| `plan_convenio` | Plan Convenio | `planConvenio` | ya |
| `datos_turno` | Datos Turno | `datosTurno` | **nuevo** |
| `datos_sobreturno` | Datos Sobreturno | `datosSobreturno` | **nuevo** |
| `fecha` | Fecha | `fecha` | ya |
| `hora` | Hora | `hora` | ya |
| `estado` | Estado | `estado` | ya |
| `centro_atencion` | Centro Atención | `centroAtencion` | ya |
| `servicio` | Servicio | `servicio` | ya |
| `profesional` | Profesional | `profesional` | ya |
| `prestacion` | Prestación | `prestacion` | ya |
| `fecha_prescripcion` | Fecha Prescripción | `fechaPrescripcion` | **nuevo** |
| `observaciones` | Observaciones | `observaciones` | ya |
| `preparacion_previa` | Preparación Previa | `preparacionPrevia` | **nuevo** |
| `requisitos_realizacion` | Requisitos Realización | `requisitosRealizacion` | **nuevo** |
| `documentacion_requerida` | Documentación Requerida | `documentacionRequerida` | ya |
| `documentacion` | Documentación | `documentacion` | **nuevo** — col tabla req |
| `obligatorio` | Obligatorio | `obligatorio` | ya |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` | ya |
| `asignar_turno` | Asignar Turno | `btnAsignarTurno` | ya — 120px |
| `cancelar` | Cancelar | `cancelar` | ya — modo otorga |
| `volver` | Volver | `volver` | ya — solo lectura HIS (Web: Cerrar T5 = mismo acto) |

## Geometría

| Zona | Legacy | Web DoD |
|------|--------|---------|
| Dialog | 1200×520 **cuerpo** (`p:dialog`), titlebar aparte | `width: 1200px`; body `height: 520px` **sin** titlebar adentro |
| Paciente nombre | `width:98%` colspan 3 | misma tabla 8 cols; nombre `span` 3 |
| Fecha/hora/estado/centro | `InputWid100` **misma tabla** (columnas compartidas) | `<table>` 6 cols; input `width: 100%` de la TD — **no** `w-[111px]` |
| Disabled | Verona `#dadada` | `.gt-his-info-inp:disabled` |
| Obs | 450×116 | igual |
| Prep | 450×260 (228 si recepción) | 450×260; print **N/A** agenda |
| Req / Doc | 360×202 | igual; tablas emptyMessage |
| Footer | `MarAuto` buttons 120px | centrado `w-[120px]` DS buttons |
