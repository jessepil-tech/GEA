---
title: Plan — CU clínico B.1 Llamar recepción
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-b1-llamar-recepcion
---

# Plan — CU-B.1

## Enfoque (ejecutado)

| Capa | Decisión |
|------|----------|
| API | `POST .../recepciones/{id}/llamar` |
| Persistencia | `AnunciadorWritePort` → INSERT `llamado_paciente`; V16 linkage |
| Default display | Seed `ANU-DEMO` (`hospital.agi.anunciador-default-id`) |
| UI | Botón Llamar por fila en `/agi/espera` |
| Paridad | Selección por id (+ throttle 5s); “siguiente sin id” fuera de este gate |

## Dependencias

- CU-B lista gate-done  
- Anunciador piloto (display consume llamados)
