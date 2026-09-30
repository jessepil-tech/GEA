---
title: Verify — Paridad recepción / cola M1–M4
version: 0.3.0
status: gate-done
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.paridad-recepcion-cola
---

# Verify-report — M1–M4

## Resultado

**PASS** — 2026-08-18

> **Avance 2026-08-25 (cutover ts):** la paridad recae sobre `ts.cola_espera_recep` (V28),
> `ts.cola_espera_serv_amb` (V28) y `ts.llamado_anunciador` (V27). El piloto `recepcion_agi`
> fue retirado en **V33**; `llamado_paciente` / `public.*` ya no existen. Schema canónico
> según [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

| Slice | Evidencia |
|-------|-----------|
| M1–M3 Cola A | `RecepcionColaResourceIT` + UI `/recepcion/cola` |
| M4 Cola B | `RecepcionEsperaAmbResourceIT` + UI `/recepcion/espera-amb` |

## Cuándo se usa cada tabla PG

| Tabla | Uso **ahora** | Diferido |
|-------|---------------|----------|
| `cola_espera_recep` | **Ya en prod-dev:** lista pendientes, llamar, últimos 10, UI puesto | Sync masivo desde Oracle → UAT |
| `cola_espera_serv_amb` | **Ya:** lista filtrada Cola B + UI | Acciones menú; maestros catálogo |
| `ts.llamado_anunciador` | Display TV + WS + ciclo S→N / quitar (V27) | Enganche anulación CUs → Fase 4 |
| Oracle `LLAMADO_ANUNCIADOR` 1:1 | **superseded** — ya en `ts.llamado_anunciador` (V27) | — |

## Capacidades legacy (anti-gap)

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Cola A listar / llamar / últimos | **done** | M1–M3 |
| Cola B listar + filtros | **done** | M4 |
| Acciones menú Cola B | **diferido** | backlog M5 |
| Ciclo llamado anunciador post-llamar | **done** (núcleo) | [`ciclo-vida-llamado-anunciador/`](../../anunciador/ciclo-vida-llamado-anunciador/) gate-done 2026-08-26 |

## Endpoints M4

```http
GET /api/v1/recepcion/espera-amb?idCentroAte=1001
  &colaEspera=ATENCION_MEDICA
  &idServicio=10&idPersonal=9001&idEspecialidad=101
  &estado=EN_ESPERA|EN_ATENCION
```

UI: `/recepcion/espera-amb` (seed centro `1001`).
