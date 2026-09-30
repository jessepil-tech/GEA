---
title: Inventario validaciones — M1c especialidad
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1c-especialidad.validaciones
---

# Inventario validaciones — M1c (G0)

`BBEspecialidad.actionBtnAceptar`: insert/update **sin** chequeo BB de vacío. Xhtml marca `especialidad (*)` y `adulto_pediatrico (*)`.

| Regla | Mensaje | UI | API |
|-------|---------|----|-----|
| Nombre `(*)` | BB no valida | exigir | 400 |
| Adulto/Pediátrico `(*)` | BB no valida | ADULTO / PEDIATRICO / TODOS | 400 |
| Interconsulta | checkbox HIS → CHAR S/N | opcional, default N | persistir S/N |
| Nombre HIS | `replaceAccentedChars` (mayúsculas, sin acentos) | se ve el valor guardado | mismo normalizado |
| Éxito | `ROW_*_INFO` | toast | 2xx |
| Confirm baja | `desea_eliminar_especialidad` | modal 1:1 | N/A |
| PK duplicada | 23505 | toast | 409 |
