# CU-B — Post-recepción

Programa: [`cu-clinico/`](../cu-clinico/).

| Artefacto | Estado |
|-----------|--------|
| [spec](spec.md) | active |
| [plan](plan.md) | active |
| [tasks](tasks.md) | done |
| [verify](verify-report.md) | **PASS** (smoke) |

UI: `/agi/espera` · Smoke: `bash Hospital-Api/tools/smoke-cu-clinico-b.sh`

**Alcance:** lista / detalle de recepciones en espera.  
**No** publica en anunciador al listar ni al confirmar recepción — eso es  
[`cu-clinico-b1-llamar-recepcion/`](../cu-clinico-b1-llamar-recepcion/) (botón **Llamar**).
