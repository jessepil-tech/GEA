---
title: Inventario validaciones — Lista espera atención médica
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# Inventario validaciones — E3

Fuente: `BBListaEsperaAtencionMedica` + confirm JS del xhtml + API Cola B Llamar.

| Caso | Legacy | Web E3 |
|------|--------|--------|
| Llamar sin anunciador del ambiente | `anunciadorDisponible` oculta el menuitem | POST 404 `anunciador` → toast, sin `/500` |
| Confirmación Llamar | `confirm('¿Desea llamar al Paciente {nombre} ?')` | `app-confirm-dialog` (no `window.confirm`) |
| Cancelar confirm | no llama package | cierra modal, sin POST |
| Fila no recepcionada | engranaje `rendered=false` | Cola B ya recepcionada; gris HIS **N/A** en este seed |
| econsulta | oculta Llamar | sin flag en read-model M4 → se muestra Llamar · **diferido** si aparece el flag |
| Lista vacía | `emptyMessage` | copy `no_se_encontraron_registros` |
| Sin Bearer | — | 401 ya cubierto en IT Cola B |

No hay `impbus` de alta en esta pantalla (no ABM). Errores de negocio = toast.
