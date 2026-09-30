---
title: Verify — CU clínico B
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-b-post-recepcion
---

# Verify — CU-B Post-recepción

## verdict

**PASS (contrato CU-B lista)** — smoke lista/filtro/detalle **PASS**
(re-check 2026-08-14 tarde).

Re-auditoría regla WAIVE:

| Ítem | Hallazgo | Estado |
|------|----------|--------|
| RF-1..3 lista/filtro/UI | Entregado | **completo** |
| RF-4 Llamar | Legacy HOSPITAL_2 **sí** | **gate-done** en [`../cu-clinico-b1-llamar-recepcion/`](../cu-clinico-b1-llamar-recepcion/) |
| Enlace UI → Anunciadores | Solo navegación | **no** cuenta como paridad del acto |
| Cola rica (filtros, poll, últimos 10, triage) | Legacy **sí** | diferido post-B.1 (documentado en spec) — **no WAIVE** |

**Conclusión:** CU-B **lista** está completa; **puesto recepción** no lo está hasta B.1 (+ deuda cola).

## CA

| CA | Resultado |
|----|-----------|
| Tras confirmar, aparece en lista | pass (smoke) |
| Filtro `destino` | pass (smoke) |
| Detalle + `ticketPdfUrl` | pass (smoke) |
| UI `/agi/espera` | entregada |
| RF-4 Llamar | diferido B.1 (no WAIVE) → **gate-done** B.1 |

## Cómo

```bash
bash Hospital-Api/tools/smoke-cu-clinico-b.sh
# IT (incluye cuB_*):
cd Hospital-Api && mvn -pl presentation-api -am -Dtest=AgiRecepcionResourceIT test
```

UI: **Espera AGI** → `/agi/espera`.
