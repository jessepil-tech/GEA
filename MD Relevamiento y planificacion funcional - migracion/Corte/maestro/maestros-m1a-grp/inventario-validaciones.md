---
title: Inventario validaciones — M1a grp
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-grp.validaciones
---

# Inventario validaciones — M1a grp (G0)

`BBDatosGrpCentroAtencion.actionBtnAceptar`: insert/update **sin** chequeo BB de vacío. Xhtml marca `grp_centro_atencion (*)`.

| Regla | Mensaje | UI | API |
|-------|---------|----|-----|
| Nombre `(*)` | BB no valida | exigir | 400 |
| Permite elegir centro turnos | defaultBuilder **true** → CHAR S | checkbox, default S en alta | persistir S/N |
| Nombre HIS | `replaceAccentedChars` (mayúsculas, sin acentos) | se ve el valor guardado | mismo normalizado |
| Éxito | `ROW_*_INFO` | toast | 2xx |
| Confirm baja | `desea_eliminar_grp_centro_atencion` | modal 1:1 | N/A |
| PK duplicada | 23505 | toast | 409 |
| FK centro | 23503 | toast | 409 `FOREIGN_KEY_VIOLATION` |
