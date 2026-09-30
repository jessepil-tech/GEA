---
title: Inventario validaciones — M1a centro de atención
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1a-centro.validaciones
---

# Inventario validaciones — M1a (G0)

Fuente: `BBDatosCentroAtencion.actionBtnAceptar` · `BBCentroAtencion.actBtnEliminarCentroAtencion` · `MessageBundle` · dump NOT NULL.

BB **no valida** `(*)` antes del insert (igual que anunciador datos). Web + API: create **y** update exigen los `(*)` del xhtml; toast `REQUIRED_FIELD_ERROR`.

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| grp, centro, nombre, calle, nro calle, provincia, localidad y refrescos/cantidades marcados `(*)` | BB inserta igual | exigir; toast `Debe completar todos los campos requeridos.` | 400 mismo texto; números `*` usan defaultBuilder si el body los omite |
| email `validator="emailValidator"` | validador JSF | mismo | 400 si no vacío e inválido |
| Éxito alta | `ROW_INSERT_INFO` | toast | 2xx |
| Éxito edición | `ROW_UPDATE_INFO` | toast | 2xx |
| Éxito baja | `ROW_DELETE_INFO` | toast | 2xx |
| Confirm baja | `desea_eliminar_el_registro` (`window.confirm`) | modal DS texto 1:1 | N/A |
| Error JDBC / FK | `MessageManager.addToMessages(e)` | toast | 4xx cuerpo |
| Binomio `(*)` no virtual | BB no valida | se pintan; no bloquean Aceptar (igual que BB) | no exigir `id_tipo_int_centro_dflt_bebe` |
| Lupa depósito | `buscadorDeposito` | dialog; combo vacío si `ts.deposito` no está en el host | GET `/depositos` lista vacía si falta la tabla |

Create y update: misma regla de requeridos.
