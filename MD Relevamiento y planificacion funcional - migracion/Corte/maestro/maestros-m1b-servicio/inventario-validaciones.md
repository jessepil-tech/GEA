---
title: Inventario validaciones — M1b servicio / vínculo
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1b-servicio.validaciones
---

# Inventario validaciones — M1b (G0)

`BBServicio.actionBtnAceptar`: insert/update **sin** chequeo BB de vacío. Xhtml marca `servicio (*)`.

| Regla | Mensaje | UI | API |
|-------|---------|----|-----|
| Nombre servicio `(*)` | BB no valida | exigir; `REQUIRED_FIELD_ERROR` | 400 create y update |
| Éxito alta/edición/baja catálogo | `ROW_*_INFO` | toast | 2xx |
| Confirm baja servicio | `desea_eliminar_el_servicio` | modal 1:1 | N/A |
| Vínculo: par servicio+centro | dump PK/NOT NULL `id_servicio`+`id_centro_ate` | no guardar sin ambos | 400 |
| Par duplicado | 23505 | toast | 409 DUPLICATE_ENTRY |
| `atiende_turnos` | default dump NOT NULL | default legacy al insert; no simular el job | persistir `N` |

Tabs amb/int/lab: no validar en v1.
