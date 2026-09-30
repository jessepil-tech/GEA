---
title: Inventario validaciones — especialidad_serv
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv.validaciones
---

# Inventario validaciones — G0

`BBEspecialidadServ.actionBtnAceptar`:

| Regla | Mensaje | UI | API |
|-------|---------|----|-----|
| `especialidad (*)` | xhtml; BB no valida vacío | exigir combo | 400 `DEBE_COMPLETAR` |
| `numero_orden (*)` | xhtml | exigir | 400 |
| Triple PK centro+servicio+especialidad | dump NOT NULL | dialog HAB | 400 |
| Par duplicado | 23505 | toast | 409 `DUPLICATE_ENTRY` |
| `permite_internacion` S y tipo null | `Debe seleccionar el tipo de internacion` | warn | 400 mismo texto |
| `permite_internacion` N | `onChange` limpia tipo | deshabilita select | persistir tipo null |
| Defaults alta | orden 1 · etiquetas 0 · activo S · internación S · tipo AMBAS | form | draft |
| `modalidad` / mensaje | `replaceAccentedChars` | — | hisName; max 2 / 60 |
| Baja | `desea_eliminar_especialidad` | modal | DELETE |
| Éxito | `ROW_*_INFO` | toast | 2xx |
