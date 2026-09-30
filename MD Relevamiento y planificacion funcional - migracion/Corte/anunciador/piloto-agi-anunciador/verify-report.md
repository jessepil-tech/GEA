---
title: Verify report — sdd.hospital.piloto-agi-anunciador
description: Envelope de calidad post-implementación CU #1 (anunciador de llamados).
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-13
phase_id: sdd.hospital.piloto-agi-anunciador
---

# Verify report — `sdd.hospital.piloto-agi-anunciador`

## metadata

| Campo | Valor |
|-------|--------|
| feature / slug | piloto-agi-anunciador |
| date | 2026-08-13 |
| spec path | docs/sdd/piloto-agi-anunciador/spec.md |
| plan path | docs/sdd/piloto-agi-anunciador/plan.md |
| tasks path | docs/sdd/piloto-agi-anunciador/tasks.md |
| analyzer | revisión manual (spec ↔ plan ↔ tasks + evidencia runtime) |

## verdict

**PASS** — CU #1 (A1+A2 anunciador de llamados) cerrado para ampliar a G1 o `catalogo`.

## checklist

| # | Criterio | Resultado | Notas |
|---|----------|-----------|-------|
| V1 | Spec con CA comprobables | pass | CA 1–6 en spec.md |
| V2 | Plan coherente con spec | pass | Primer vertical = A1+A2; Core = features en Api |
| V3 | Tasks cubren RF | pass | mapa RF→TSK en tasks.md |
| V4 | Verificación en tasks de código | pass | IT + smoke + UI manual |
| V5 | INV-1 / INV-2 | pass | sin microservicio Core; Identity sin catálogo negocio |
| V6 | BLOCKER mitigados | pass | socket.io WAIVE en plan; ABM diferido RF-6 |
| V7 | Sin secretos; alcance acotado | pass | seed demo; credenciales solo README/dev |
| V8 | `phase_id` + `TSK-*` | pass | `sdd.hospital.piloto-agi-anunciador.*` |

## Criterios de aceptación (evidencia)

| CA | Evidencia 2026-08-13 | Resultado |
|----|----------------------|-----------|
| 1 Ping auth | `GET /api/v1/piloto/ping` con Bearer → 200; sin token → 401 | pass |
| 2 Anunciadores / llamados | Seed `ANU-DEMO`; smoke `tools/smoke-piloto-anunciador.sh` **PASS** | pass |
| 3 Api levantable | `quarkus:dev` :8081 + Dev Services Postgres + Flyway v7 | pass |
| 4 SDD + verify | este reporte | pass |
| 5 Front | Hospital-Web `/anunciadores` + llamados tras login Identity | pass |
| 6 Estrategia Core | [estrategia-core.md](estrategia-core.md) | pass |

## blockers

Ninguno.

## warnings

| id | description |
|----|-------------|
| W1 | CORS: Quarkus no trata `*` en lista mixta. Orígenes locales deben listarse (p. ej. `:4200` y `:4210`). Ajustado en Identity y Hospital-Api. |
| W2 | `AnunciadoresResourceIT` / surefire fallan si ya hay `quarkus:dev` en :8081 (conflicto de arranque). Evidencia primaria = smoke + UI con servicios vivos. |
| W3 | Remotos ADO de Hospital-Api / Hospital-Web diferidos (trabajo local). |
| W4 | A1 sala/TV: `/display/anunciadores/:id` sin JWT; `X-API-Key` + `smoke-display-anunciador.sh`. |

## architecture_invariants

| invariant | status |
|-----------|--------|
| INV-1 No microservicio Core aparte | ok |
| INV-2 Identity sin sectores/ambientes/anunciadores | ok |
| RF-3 Login solo Identity | ok |
| NFR-1 Postgres (no Hibernate→Oracle 11.2) | ok |
| NFR-2 Puerto Api ≠ Identity | ok (8081 / 8080) |

## test_evidence_plan

| requirement | tipo | ref |
|-------------|------|-----|
| RF-1…3 ping JWT | integration + smoke | TSK-app-3, smoke |
| RF-4 listado/llamados | integration + smoke E2E | TSK-app-4, app-6 |
| RF-5 front | manual UI | TSK-app-5 |
| RF-6 seed/catálogo | Flyway V7 + doc | estrategia-core |

## commands_to_run_before_merge

```bash
# Identity :8080 + Hospital-Api :8081 (quarkus:dev)
bash /Volumes/External/Development/osw/grupogea/Hospital-Api/tools/smoke-piloto-anunciador.sh

# ITs (sin otro quarkus:dev ocupando el puerto de test)
cd /Volumes/External/Development/osw/grupogea/Hospital-Api
mvn -pl presentation-api -am test -Dtest='AnunciadoresResourceIT,PilotoPingIT' \
  -Dsurefire.failIfNoSpecifiedTests=false

# UI
# Hospital-Web → sign-in admin / Admin123! → /anunciadores
```

## gate

| Gate | Estado |
|------|--------|
| `sdd.hospital.piloto-agi-anunciador.gate-spec` | PASS |
| `sdd.hospital.piloto-agi-anunciador.gate-plan` | PASS |
| `sdd.hospital.piloto-agi-anunciador.gate-tasks` | PASS |
| `sdd.hospital.piloto-agi-anunciador.gate-verify` | **PASS** |
| `sdd.hospital.piloto-agi-anunciador.gate-done` | **PASS** (CU #1; 2026-08-13) |

## next_step

- Ampliar a **G1** (recepción / autogestión AGI) como 2º vertical, **o**
- Feature `catalogo` (ABM sector/ambiente) en el mismo `Hospital-Api`.
