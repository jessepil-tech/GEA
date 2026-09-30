---
title: Plan — CU clínico C
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-17
phase_id: sdd.hospital.cu-clinico-c-demanda-espontanea
---

# Plan — CU-C

## Decisión (cerrada)

| Opción | Estado |
|--------|--------|
| **C1** `turno_id` nullable | **Adoptada** |
| C2 Turno sintético | descartada |
| C3 Tabla aparte | descartada |

Flyway `V17__cu_c_demanda_espontanea.sql`: DROP NOT NULL + `servicio`/`instruccion`
+ tabla `servicio_agi` seed.

## Enfoque (ejecutado)

1. Clarify C1 ✅  
2. Command `ConfirmarEspontanea` + `ListServicios` ✅  
3. UI wizard ✅  
4. Smoke ✅  
