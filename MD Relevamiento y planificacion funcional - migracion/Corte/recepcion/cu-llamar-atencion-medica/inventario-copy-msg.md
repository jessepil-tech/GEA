---
title: Inventario copy — Lista espera atención médica
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# Inventario copy — E3

Fuente: `HOSPITAL_2/.../listaEsperaAtencionMedica.xhtml` +
`HOSPITAL_2/src/ar/com/thinksoft/resources/Resources.properties`.

Constante Web: `Hospital-Web/.../espera-atencion-labels.ts`.

| msg.key | Valor HOSPITAL_2 | Web E3 |
|---------|------------------|--------|
| especialidad | Especialidad | header + combo norte |
| pacientes | Pacientes | combo norte |
| referencias | Referencias | botón norte **disabled** |
| turnos | Turnos | botón norte **disabled** |
| pacientes_atendidos | Pacientes Atendidos | botón norte **disabled** |
| historia_clinica | HISTORIA CLINICA | botón norte **disabled** (mayúsculas HIS en menú; Title Case en botón norte como `msg.pacientes_atendidos`) |
| volver | Volver | backLink |
| pacientes_del_dr | Pacientes del Dr. | header tabla personal |
| cantidad | Cantidad | header ` - Cantidad: N` |
| hora_turno | Hora Turno | columna |
| espera | Espera | columna |
| ctd_llamados | Ctd. Llamados | columna |
| LLAMAR_PACIENTE | LLAMAR PACIENTE | menú gear |
| ATENDER_PACIENTE | ATENDER PACIENTE | menú gear **disabled** |
| INFORMACION | INFORMACIÓN | menú gear **disabled** |
| COMUNICACION_INTERNA | COMUNICACIÓN INTERNA | menú gear **disabled** |
| HISTORIA_CLINICA | HISTORIA CLINICA | menú gear **disabled** |
| desea_llamar_al_paciente | ¿Desea llamar al Paciente | confirm + ` {nombre} ?` |
| no_se_encontraron_registros | No se encontraron registros | empty |
| pacientes_en_atencion | Pacientes en Atención | header pane derecho |
| pacientes_del_servicio | Pacientes del Servicio | **N/A** este corte (pane oculto, flag apagado) |
| motivo_sobreturno | Motivo Sobreturno | tooltip `*` · **parcial** (texto `*` sí; panel 333px diferido) |
