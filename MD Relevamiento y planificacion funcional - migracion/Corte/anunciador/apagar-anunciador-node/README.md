# SDD — Apagar ANUNCIADOR Node

`phase_id:` **`sdd.hospital.apagar-anunciador-node`**  
Estado: **gate-done (dev)** · 2026-08-18 · [verify-report.md](verify-report.md) **PASS**

Producto retiró el satélite Node del perímetro migrado. Canónico:
**Identity + Hospital-Api + Hospital-Web**.

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Gates de corte, RF, waivers |
| [plan.md](plan.md) | Cortes N0–N5 |
| [tasks.md](tasks.md) | Checklist (completo) |
| [inventario-despliegues-node.md](inventario-despliegues-node.md) | N4 hosts/env |
| [runbook-stop-node.md](runbook-stop-node.md) | N5 stop |
| [verify-report.md](verify-report.md) | Envelope PASS |

Inventario técnico Node→Api: [`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/).  
Legacy Node+Vue: [`Hospital-Legacy/ANUNCIADOR/`](../../../../../Hospital-Legacy/ANUNCIADOR/). No hay `DEPRECATED.md` en ese árbol; N5 archiva el código, no lo borra.

## Capacidades de sala

| Capacidad | Destino | Corte |
|-----------|---------|-------|
| Auth | Identity | done |
| Llamados + WS | Api + Web display | done |
| Ocupación | Api+Web | **N1** |
| Logo / fondo | Api+Web | **N2** |
| Diccionario TTS | Api+Web SpeechSynthesis | **N3** |
| Reloj Socket | Reloj cliente | **WAIVE** |
| Config sin Node | appsettings Identity+Api | **N4** |
| Stop Node + verify | runbook + smoke | **N5 (dev)** |

## Smoke

```bash
bash Hospital-Api/tools/smoke-node-down-anunciador.sh
```

## Clarify — **CERRADA 2026-08-18**

N1–N3 obligatorios (paridad). Ver [`regla-waiver-paridad-legacy.md`](../../../canon/regla-waiver-paridad-legacy.md).

**Pendiente ops (fuera de gate dev):** cutover UAT/prod por centro; sync Oracle→PG assets/lexemas reales.
