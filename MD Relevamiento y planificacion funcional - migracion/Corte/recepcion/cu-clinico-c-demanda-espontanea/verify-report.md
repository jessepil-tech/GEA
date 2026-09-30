---
title: Verify — CU clínico C Demanda espontánea
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-17
phase_id: sdd.hospital.cu-clinico-c-demanda-espontanea
---

# Verify — CU-C Demanda espontánea

## verdict

**PASS** — IT `cuC_*` + smoke host (2026-08-17).

Decisión **C1**: `turno_id` nullable + `servicio` denormalizado (Flyway V17).

## CA

| CA | Resultado |
|----|-----------|
| Paciente sin turnos → ticket espontánea | pass |
| `turnoId` null + servicio set | pass |
| Aparece en lista CU-B | pass (smoke) |
| UI `/agi/demanda-espontanea` | entregada |

## Cómo

```bash
cd Hospital-Api
mvn -pl presentation-api -am -Dtest=AgiRecepcionResourceIT#cuC_listServicios_yConfirmarEspontanea,AgiRecepcionResourceIT#cuC_espontanea_sinServicio_returns400 -Dsurefire.failIfNoSpecifiedTests=false test
bash tools/smoke-cu-clinico-c.sh
```
