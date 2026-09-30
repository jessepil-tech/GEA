---
title: Inventario validaciones — prep / req infoTurno
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-prest.validaciones
---

# Inventario validaciones

Fuente: `ImpPrestacion.selectPreparacionPrestEdad` L168–172 · `ImpBusPreparacionPrest` L24–29.  
Este acto **no** tiene toasts MessageBundle de bloqueo.

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Solo si `idPrestacion != null` | (no carga) | no GET | skip |
| Edad = `floor((hoy − fechaNac) / 365.25)` | — | — | GET |
| Fecha nac nula / error | edad **0** (HIS catch) | — | edad 0 |
| `edadDesde` null **o** ≤ edad | filtra | muestra 1ª fila | Criteria HIS |
| `edadHasta` null **o** ≥ edad | filtra | idem | idem |
| Varias filas match | HIS `list.get(0)` | una sola prep | primera |
| Sin match | panel vacío | vacío | `preparacionHtml` null |
| Req sin filas | emptyMessage | emptyMessage | lista `[]` |
| GET error | — | toast sin `/500` | 4xx/5xx manejado |

ABM solape edades (`NO_PUEDEN_EXISTIR_EDADES_SOLAPADAS`) = **fuera** (config).
