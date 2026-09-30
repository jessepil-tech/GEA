---
title: Inventario copy — T5.1c elegibilidad
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros.copy
---

# Inventario copy — Elegibilidad north (G0)

Fuente: `HOSPITAL_2/.../Resources.properties` + `agenda.xhtml`.  
Web: extender `turnos-agenda-labels.ts` **después** FIRME / Gate UI.

| msg.key | Valor | Constante Web | En UI este slice |
|---------|-------|---------------|------------------|
| `nro_afiliado` | Nro. Afiliado | `nroAfiliado` | **ya** |
| `nro_documento` | Nro. Documento | `nroDocumento` | **ya** |
| `validando_elegibilidad` | Validando Elegibilidad | `validandoElegibilidad` | header spinner (T5 labels ya) |
| `PENDIENTE` | PENDIENTE | `pendiente` | title icono sin resultado |
| `documentacion_requerida` | Documentación Requerida | `documentacionRequerida` | **ya** T5.1b |
| `doc_requerido` | Documentación Requerida | `docRequerido` | **ya** |
| `obligatorio` | Obligatorio | `obligatorio` | **ya** |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` | **ya** |
| `informacion` | Información | `informacion` | **ya** — no usar para pagar (cobros) |

Mensajes BB (`NRO_AFILIADO_ELEGIBILIDAD_REQUIRED` / `NRO_DOCUMENTO_ELEGIBILIDAD_REQUIRED`): inventario validaciones; copy exacto al portar constantes.

## Geometría copy-adjacent

| Zona | Legacy | Web DoD |
|------|--------|---------|
| Fila afiliado | label + `inputMask` `InputWid100` + icono FA | **misma fila**; icono ~15px (`fs15`) margen 10px |
| Fila documento | label + input + icono (si valida doc) o input disabled | **misma fila** |
| Spinner | dialog modal, `ajaxloadingbar.gif`, `closable=false` | modal DS; sin X; copy header |

No toasts de éxito (validar no es CRUD). Rechazo: mensaje en dialog/toast (Clarify #7).
