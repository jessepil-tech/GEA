---
title: Inventario validaciones — P3 ABM Anunciador / Terminal AG
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.anunciador-agi-config-abm.validaciones
---

# Inventario validaciones — P3 ABM (G0)

Plantilla gate: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) v1.10+.

Fuente:

- BB: `BBDatosAnunciador` · `BBConfiguracionAvanzada` · `BBAnunciador` · `BBAnunciadorAmbienteAmb`
- BB: `BBDatosTerminalAutogestion` · `BBTerminalAutoGestion` · `BBOpcionTerminalAutogestion`
- BB: `BBDiccionarioAnunciador`
- Mensajes: `HOSPITAL-BUSINESS/.../MessageBundle.java`
- Delegators: `Anunciadores.*` · `Configuracion.insert/update/deleteTerminalAg` · `deleteOpcionTerminalAg`

Web + API: create **y** update (misma regla). Feedback: WARN/ERROR → toast; INFO CRUD → toast. Error API en pantalla sin `/500`. Modal baja (no solo `window.confirm` en Web salvo paridad anunciador).

## Anunciador — datos (`BBDatosAnunciador.actionBtnAceptar`)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Nombre / título marcados `(*)` en xhtml | **BB no valida** (inserta igual) | C4: exigir ambos; toast `DEBE_COMPLETAR…` | C1: **sí** exigir (cerrar hueco BB; no copiar el bug) |
| Defaults alta (`Anunciador.defaultBuilder`) | minutos=5; ctd char leyenda/lugar=35; voz/multimedia/ocupación=false; tipo llamado=`APELLIDO_NOMBRE` | C4 prefill | C1 defaults si omitidos |
| Éxito alta | `ROW_INSERT_INFO` | toast | 2xx |
| Éxito edición | `ROW_UPDATE_INFO` | toast | 2xx |
| Error negocio / JDBC | `MessageManager.addToMessages(e)` | toast | 4xx cuerpo |

Centro/sector en datos: **comentados** en xhtml → no validar ni pintar (no revivir).

## Anunciador — baja (`BBAnunciador.actBtnEliminarAnunciador`)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Confirm | `desea_eliminar_anunciador` (`window.confirm`) | C4 modal (texto 1:1) | N/A |
| Semántica | **DELETE físico** `Anunciadores.deleteAnunciador` | C4 | C1 DELETE (o POST baja) **físico**. **No** inventar `activo` en `ts.anunciador` |
| Sin id | botón disabled | C4 | 404 |
| FK / no se pudo | excepción o `ROW_DELETE_ERROR` (ambientes usan WARN ese texto) | toast | 409/400 — no silenciar |

## Anunciador — config avanzada (`BBConfiguracionAvanzada`)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| `ctd_char` cola y lugar marcados `(*)` | **BB no valida** | C4 exigir numéricos | C1 exigir |
| Tipo llamado `(*)` | **BB no valida**; default `APELLIDO_NOMBRE` | C4 combo | C1 enum `APELLIDO_NOMBRE` \| `DOCUMENTO` \| `DOCUMENTO_ALTERADO` |
| URL / tipo multimedia | deshabilitados si checkbox multimedia off | C4 | C1 ignora o null si off |
| Logo: tipos | `allowTypes` jpg/jpeg/gif/tiff/png (case mix) | C4 | C1 same |
| Logo preview | 160×160 (`DFLT_IMAGE_WIDTH/HEIGHT`) | C4 | N/A |
| Fondo preview | 264×160 UI; BB `DFLT_IMAGE_WIDTH_FONDO=1920` × `1080` | C4 preview acotado; store full | C1 |
| Archivo inválido / tamaño | `archivo_no_valido` / `tamano_no_valido` | fileUpload | 400 |
| Quitar logo / restablecer fondo | update fila (null BLOB) | C4 | PUT assets |
| Restablecer valores | confirm `desea_restablecer_valores` → null logo+fondo + update | C4 | PUT |
| Éxito | `ROW_UPDATE_INFO` | toast | 2xx |

Reproducir video en TV = **fuera** (gap display). Este CU **persiste** flags/url.

## Vínculo ambientes (`BBAnunciadorAmbienteAmb`)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Alta = ambientes **existentes** (buscador) | `ROWS_INSERT_INFO` | dialog 1200×550 | POST vínculo; **no** ABM `ambiente_amb` |
| Baja vínculo | confirm `desea_eliminar_el_registro` + DELETE vínculo | icon-only trash | DELETE vínculo |
| Baja fallo | `ROW_DELETE_ERROR` WARN | toast | 409 |
| Éxito baja | `ROW_DELETE_INFO` | toast | 2xx |

## Terminal AG (`BBDatosTerminalAutogestion.guardarTerminalAg`)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Nombre vacío | `DEBE_COMPLETAR_LOS_CAMPOS_REQUERIDOS` | C4 | C2 |
| Sin centro | idem | C4 | C2 |
| Sin sector amb | idem | C4 | C2 |
| Sin ambiente amb | idem | C4 | C2 |
| Sin `ctdOpciones` | idem | C4 | C2 |
| Éxito alta / edit | `ROW_INSERT_INFO` / `ROW_UPDATE_INFO` | toast | 2xx |
| Baja | DELETE físico `deleteTerminalAg` + `p:confirm` `desea_eliminar_el_registro` | C4 | C2 DELETE. Checkbox `activo` **no** sustituye Eliminar |

## Opciones (`BBOpcionTerminalAutogestion.btnAceptar`)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| `nroOpcion` null | `DEBE_COMPLETAR…` | C4 | C2 |
| Leyenda vacía | idem | C4 | C2 |
| Tipo vacío | idem | C4 | C2 enum `ESPERA_TRIAGE` \| `AUTO_RECEPCION` \| `ESPERA_RECEPCION` |
| Sin centro | idem | C4 (readonly; viene del terminal) | C2 |
| Prioridad null | idem | C4 | C2 |
| Prefijo vacío | idem | C4 | C2 |
| Sin triage **y** sin recepción espera | idem (legacy: ambos null) | C4: al menos el destino habilitado por tipo | C2: si tipo TRIAGE → `idTerminalTriageEspera`; si RECEPCION/AUTO → `idRecepcionEspera` |
| Baja fila | DELETE + confirm registro | icon trash | C2 |
| Éxito | INSERT/UPDATE info | toast | 2xx |

## Diccionario (`BBDiccionarioAnunciador`)

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Palabra / equivalente | **BB no valida** (PK `palabra`) | C4 exigir palabra | C3 PK + equivalente |
| Alta | insert; toast **`ROW_UPDATE_INFO`** (bug HIS: usa UPDATE en alta) | C4: toast **insert** (cerrar hueco; no copiar bug) | C3 |
| Edit | `ROW_UPDATE_INFO` | toast | C3 |
| Baja | `ROW_DELETE_INFO` + confirm registro | C4 | C3 DELETE PK |

## Fuera

Terminal triage / niveles ESI / serv-triage anunciador: **diferido** `anunciador-config-avanzada` / no este slug.

## Verify

Tabla validaciones ↔ API + UI en [verify-report.md](verify-report.md) al cerrar C1–C4.
