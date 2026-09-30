---
title: Inventario copy — T5.1 hijo info popups
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-info-popups.copy
---

# Inventario copy — Info popups (G0)

Fuente: `HOSPITAL_2/.../Resources.properties`.  
Web: extender `turnos-agenda-labels.ts`.

| msg.key | Valor | Constante Web | En UI este slice |
|---------|-------|---------------|------------------|
| `informacion_de_busqueda` | Información de Búsqueda | `informacionDeBusqueda` | header dialog paciente |
| `informacion_convenio` | Información Convenio | `informacionConvenio` | **ya en labels** — header dialog plan |
| `observaciones` | Observaciones | `observaciones` | **ya** — header auto-popup HIS |
| `observaciones_convenio` | Observaciones Convenio | `observacionesConvenio` | **ya** — panel |
| `observaciones_plan_convenio` | Observaciones Plan Convenio | `observacionesPlanConvenio` | **ya** — panel |
| `informacion` | Información | `informacion` | title botón (ya) |
| `cerrar` | Cerrar | `cerrar` | **ya** — auto-popup; aria X info |
| `documentacion_requerida` | Documentación Requerida | `documentacionRequerida` | header panel info convenio |
| `doc_requerido` | Documentación Requerida | `docRequerido` | columna tabla (Resources HOSPITAL_2) |
| `obligatorio` | Obligatorio | `obligatorio` | leyenda naranja |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` | **ya** — emptyMessage tabla |

## Geometría copy-adjacent

| Zona | Legacy | Web DoD |
|------|--------|---------|
| Fila paciente | label + input + lupa + **info** + X | **misma fila**; info 32×32 entre lupa y X |
| Fila plan | combo `InputWid100` + **info** | **misma fila**; info 32×32 |
| Dialog búsqueda | 978×618 modal | max ~978; overflow auto |
| Dialog convenio | 550×550 | ~550 |
| Auto obs | 650 × paneles 194px | ~650; Cerrar centrado |

No toasts nuevos (lectura).
