---
title: Plan — Piloto AGI G1-b
description: Puerto elegibilidad seed + ramificación de ticket.
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-b
---

# Plan — Piloto AGI G1-b

Estado: **reviewed** (defaults Q1–Q3).

## Enfoque

Reusar `features/agi`. Extender modelo seed; inyectar evaluación **antes** del insert
de `recepcion_agi`. Paridad con `BBRecepcionarPaciente.isPacienteValidoElegibilidad`
+ `enviarPacienteEsperarRecepcion` / `enviarPacienteEsperarAtencion` a nivel de
**destino**, no de cola Oracle ni reporte BIRT.

## Decisiones

| Tema | Decisión |
|------|----------|
| Validador | Puerto + `SeedValidadorElegibilidadAdapter` |
| Flags convenio | Tabla `convenio_agi` + FK en `paciente_agi` |
| Oráculo seed | Tabla `elegibilidad_seed (nro_doc, autorizado, mensaje)` |
| Ticket | Campos `destino`, `autorizado`, `mensaje_validador` |
| Auth API Key | Ya `@Authenticated`; smoke con header |
| Hardware / WS real / GM | Fuera de slice |

## Flyway

`V12__piloto_agi_g1b_elegibilidad.sql`

## API

Sin rutas nuevas obligatorias: `POST /recepciones` enriquece el ticket.
Opcional interno: evaluación dentro de `AgiPort.confirmarRecepcion`.

## Verificación

- IT: camino autorizado + rechazado
- Smoke: ambos DNI + API Key
- UI: destino visible
