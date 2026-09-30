---
title: Verify report — sdd.hospital.piloto-agi-g1
description: Envelope post-implementación slice G1 recepción autogestión.
version: 1.1.0
status: active
owner: grupogea
last_updated: 2026-08-13
phase_id: sdd.hospital.piloto-agi-g1
---

# Verify report — `sdd.hospital.piloto-agi-g1`

## metadata

| Campo | Valor |
|-------|--------|
| feature / slug | piloto-agi-g1 |
| date | 2026-08-13 |
| spec / plan / tasks | docs/sdd/piloto-agi-g1/ |
| analyzer | revisión manual + evidencia runtime |
| fase | post-implement (TSK-ops-4) |

## verdict

**PASS** — slice G1 (identificar → turnos → ticket mock) **gate-done**.

Pre-implement (ops-3) también **PASS**; este documento cierra la compuerta post app-1…6.

## checklist

| # | Criterio | Resultado | Notas |
|---|----------|-----------|-------|
| V1 | CA comprobables | pass | CA 1–7 |
| V2 | Plan alineado | pass | sin ValidadorWS / hardware |
| V3 | Tasks cubren RF | pass | app-1…6 hechos |
| V4 | Verificación | pass | smoke + 404 + Web :4210 |
| V5 | INV-1…3 | pass | seed Postgres Dev Services; golden → G1-b |
| V6 | BLOCKER | pass | Q1–Q3 cerrados |
| V7 | Sin secretos | pass | demo DNI / admin solo docs |
| V8 | phase_id + TSK-* | pass | no confundir con un gate genérico “G1” |

## Criterios de aceptación (evidencia 2026-08-13)

| CA | Evidencia | Resultado |
|----|-----------|-----------|
| 1 Identificar seed | smoke `POST .../identificar` DNI 30111222 → 200 | pass |
| 2 Turnos | smoke `GET .../turnos` ≥1 | pass |
| 3 Ticket | smoke `POST .../recepciones` → 201 `T-…` | pass |
| 4 Doc inexistente | `identificar` nro `00000000` → **404** | pass |
| 5 Sin Bearer | endpoints `@Authenticated` (mismo patrón anunciador) | pass |
| 6 UI wizard | Hospital-Web `/agi/recepcion` (build OK; Web :4210 up) | pass |
| 7 SDD + verify | este reporte | pass |

## commands (re-ejecutados)

```bash
bash Hospital-Api/tools/smoke-piloto-agi-g1.sh   # PASS
# Identity :8080 + Hospital-Api :8081 + Web :4210
```

## blockers

Ninguno.

## warnings

| id | description |
|----|-------------|
| W1 | Inventario “G1” no es un gate; IDs solo `TSK-*` / `RF-*` |
| W2 | Postgres local = host `postgresql_01` (:5432) DBs `hospital_identity` / `hospital_api`; Dev Services solo en `%test` |
| W3 | G1-b: ValidadorWS, hardware, golden master, API Key terminal |
| W4 | Display anunciador sala (`/display/...`) es A1; auth TV por API Key = follow-up aparte |

## architecture_invariants

| invariant | status |
|-----------|--------|
| INV-1 No microservicio Core | ok |
| INV-2 Identity sin turnos/pacientes clínicos | ok |
| INV-3 Golden master fuera de este slice | ok (G1-b) |
| NFR-2 Solo JWT Identity en v1 | ok |

## gate

| Gate | Estado |
|------|--------|
| `…gate-spec` | PASS |
| `…gate-plan` | PASS |
| `…gate-tasks` | PASS |
| `…gate-verify` (pre + post) | **PASS** |
| `…gate-done` | **PASS** (2026-08-13) |

## next_step

- **G1-b** (ValidadorWS / golden / hardware), **o**
- API Key de pantalla anunciador (A1 TV), **o**
- Postgres compose fijo para demos estables.
