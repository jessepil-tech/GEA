---
title: Verify — CU clínico A
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-a-catalogo-abm
---

# Verify — CU-A Catálogo ABM

## verdict

**PASS (contrato CU-A)** — IT `CatalogoConveniosResourceIT` **6/6** + smoke host
`smoke-cu-clinico-a.sh` **PASS** (2026-08-14; re-check 2026-08-14 tarde).

Re-auditoría regla WAIVE: **sin WAIVE indebido**. AGI no tenía ABM convenios;
HOSPITAL_2 convenio master (eliminar/planes/…) es **otra entidad** → deuda Fase 4,
no omitida como “opcional” del mismo RF.

## CA

| CA | Resultado |
|----|-----------|
| Smoke CRUD | pass |
| Duplicado → 409 | pass (IT + smoke) |
| Validación codigo | pass (IT) |
| UI `/catalogo/convenios` | entregada (smoke manual UI) |
| G1 no roto | no regresión en este slice |
| Paridad AGI ABM | N/A (legacy sin ABM) — OK |
| Paridad HOSPITAL_2 convenio master | **fuera de objeto** / diferido Fase 4 — no WAIVE |

## Cómo

```bash
cd Hospital-Api
mvn -pl presentation-api -am -Dtest=CatalogoConveniosResourceIT -Dsurefire.failIfNoSpecifiedTests=false test
bash tools/smoke-cu-clinico-a.sh
```

UI: Hospital-Web → **Convenios**.
