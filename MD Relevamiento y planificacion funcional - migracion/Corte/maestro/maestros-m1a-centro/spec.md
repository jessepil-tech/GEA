---
title: Spec — M1a ABM centro de atención
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1a-centro.spec
---

# Spec — M1a ABM centro de atención

Padre: [`relevamiento-maestros/`](../../../relevamiento/relevamiento-maestros/) · corte **M1a**  
[`cortes.md`](../../../relevamiento/relevamiento-maestros/cortes.md).

## Problema

`ts.centro_atencion` existe en el dump (DDL). Turnos/AGI/Recepción **leen** centros. No hay ABM: seed/bootstrap ≠ paridad. El primer CU del stream Maestros es el alta real desde el tile `ADMINISTRACION_GENERAL_NA`.

## Resultado

1. Alta / edición / baja / buscar de `ts.centro_atencion` desde Hospital-Web + Api (CQRS, schema `ts`).
2. Combo grupo / provincia / localidad: **GET** de padres (seed o filas previas). No ABM grp/provincia acá.
3. Evidencia = `id_centro_ate` nacido del CU + query.
4. Gate UI: inventarios G0 de este slug **antes** de template.

## Universo firmado (paso 3)

Índice (`./tools/indice-legacy.sh --semilla centroAtencion` + `--semilla datosCentroAtencion`, 2026-09-16): techo **ok** (4+2 xhtml candidatos). Clarify recorta.

| Entra | Path | Motivo |
|-------|------|--------|
| Shell | `pages/configuracion/centroAtencion/centroAtencion.xhtml` | menú 10203 |
| Datos | `pages/configuracion/centroAtencion/datosCentroAtencion.xhtml` | formulario `(*)` |
| Buscador | `pages/buscadores/buscadorCentroAtencion.xhtml` · `buscadorDeposito.xhtml` | 1 hop dialogs |
| Beans | `BBCentroAtencion` · `BBDatosCentroAtencion` · `BBBuscadorCentroAtencion` | acto buscar/alta/baja |
| ID | `NextIdService` ya portado (`CustomIdGenerator` legacy) | **reusar**; no portar GENERAL BODY |

| Fuera (diferido / N/A) | Destino |
|------------------------|---------|
| West: mensajes, concepto, recepción, prefijo, logo, sectores, cajas, drogas, ambientes | [`maestros-m1a-centro-west`](../maestros-m1a-centro-west/) |
| `servicioPorCentro.xhtml` | [`maestros-m1b-servicio`](../maestros-m1b-servicio/) |
| `datosEspecialidad.xhtml` | [`maestros-m1c-especialidad`](../maestros-m1c-especialidad/) |
| `grpCentroAtencion` 10217 | [`maestros-m1a-grp`](../maestros-m1a-grp/) |
| `contractDefault.xhtml` | tronco (no portar) |
| Firmas `PKG.f_/p_` | **ninguna** en índice A/B de estas semillas |
| Jobs `centro_atencion` | **N/A** (índice `--jobs` sin hits) |
| `esClienteX()` | `diferido(multi-instalacion)` — instalación **TS** genérica |

## Clarify (bloqueo de implement)

| # | Pregunta | Respuesta este slice |
|---|----------|----------------------|
| 1 | Pipeline config | **Cubierto:** tile `ADMINISTRACION_GENERAL_NA`; grp/provincia/localidad via GET+seed. **Diferido:** ABM grp M2/hijo; hab turnos T2 ya cobrado; Identity `GET /menus` fino `identidad-menus-m2`. |
| 2 | Happy path | Entrar hoja Centro → Agregar → completar `(*)` → Aceptar → toast insert → fila en `ts.centro_atencion`. Buscar en listado. Editar fila → Aceptar → toast update. |
| 3 | Ciclo de vida | `activo` / `centro_virtual`. DELETE físico legacy (confirm). Sin historial UI. `AUD_CENTRO_ATENCION` → [`maestros-m1a-auditoria-centro`](../maestros-m1a-auditoria-centro/). |
| 4 | Errores / permisos | JWT 401. Sin perfil del tile → no entra (prueba actor sin menú). BB **no** aborta por `rol_funcional_pers` en insert. NOT NULL dump + `REQUIRED_FIELD_ERROR` si el Api cierra el hueco BB (paridad anunciador: API sí exige `(*)`). FK grp/provincia/localidad inexistente → 4xx. |
| 5 | Side-effects | Ninguno (no TV, no PDF, no cola). `fecha_last_update` / `actualizado_por` sello, no historial. |
| 6 | Fuera | Ver tabla universo. No WAIVE ABM centro. |
| 7 | Paridad UI | Gate arranque: inventarios de este slug. Chrome listado **agenda/HAB** (firma 2026-09-17b). Campos datos 2-up en dialog. |
| 8 | Viaje Playwright | **`diferido(fixture)`** hasta que exista fila #1 y padres seed aplicados en la PG del e2e. |

## Inventario de capacidades

| Capacidad | Evidencia legacy | ¿Escritura BD? | ¿Realtime? | Después qué | Este slice |
|-----------|------------------|---------------|------------|-------------|------------|
| Alta centro | `BBDatosCentroAtencion.actionBtnAceptar` insert | INSERT `centro_atencion` | No | M1b vínculo | **In scope** |
| Edición | mismo Aceptar update | UPDATE | No | — | **In scope** |
| Baja | `BBCentroAtencion.actBtnEliminarCentroAtencion` | DELETE | No | — | **In scope** |
| Buscar north + dialog | `buscarCentroAtencion` · `buscadorCentroAtencion.xhtml` | Lee | No | — | **In scope** |
| Combo grp / provincia / localidad | `selectItems*` en datos | Lee padres | No | ABM M2/grp | **GET**; ABM **diferido** |
| Campos datos `datosCentroAtencion.xhtml` (depósito, refrescos, GS1, internación, triage, receta, binomio) | mismo form | Sí `ts.centro_atencion` | No | — | **In scope** |
| Mensajes mail/SMS / concepto contable / logo | west | Sí en legacy | No | west | **diferido** west |
| Vínculo servicio por centro | west `servicioPorCentro` | Sí | No | M1b | **diferido M1b** |
| Auditoría campo a campo | `AUD_CENTRO_ATENCION` | Sí legacy | No | — | **diferido** [`maestros-m1a-auditoria-centro`](../maestros-m1a-auditoria-centro/) |

## RF de escritura

| RF | Crear | Estado visible | Transición | Fin |
|----|-------|----------------|------------|-----|
| RF-1 Alta | INSERT `ts.centro_atencion` via CU | aparece en GET/buscar | `activo` S/N · `centro_virtual` | baja física o queda |
| RF-2 Edición | UPDATE mismos campos in-scope | GET refleja | — | — |
| RF-3 Baja | DELETE | deja de listar | confirm modal | — |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Resource delgado; puerto + handler CQRS; JDBC `ts.centro_atencion` columnas dump |
| RF-2 | PK `id_centro_ate` vía `NextIdService` (paridad `CustomIdGenerator`) |
| RF-3 | UI bajo tile `ADMINISTRACION_GENERAL_NA`; breadcrumb menú real |
| RF-4 | Toast `ROW_INSERT_INFO` / `ROW_UPDATE_INFO` / `ROW_DELETE_INFO`; baja = modal `desea_eliminar_el_registro` |
| NFR-1 | Scaffold starter Api/Web |
| NFR-2 | Tiempo: p95 escritura ≤ 1,5 s; volumen: `medido en vacío` + `diferido(perf-volumen)` hasta rowcount Oracle; concurrencia: recurso `sec_id_tabla` / NextId — dos altas simultáneas no repiten PK (ya hay lock en NextId o se declara gap) |
| NFR-3 | Sin `FOR UPDATE` en el bean; si el insert no disputa turno/cama, NFR concurrencia = numerador |

## Decisiones PL/SQL

Ninguna firma `PKG.f_/p_` en el universo. Insert Hibernate → Java + `NextIdService` **ya portado**. No traducir GENERAL.

## Acceso

| Superficie | Valor |
|------------|-------|
| Perfil menú | hoja 10203 bajo `ADMINISTRACION_GENERAL_NA` |
| Rol funcional PL/SQL | **no** hay `Raise_application_error` de rol en `ImpBusCentroAtencion.insert` |
| Prueba negativa | actor **sin** el tile: no ve la entrada; no se usa admin como única evidencia |

## Paridad / WAIVE

| Superficie | ¿Legacy la tiene? | Decisión |
|------------|-------------------|----------|
| ABM centro 10203 | **Sí** | Implementar. **No WAIVE** |
| Hojas west | **Sí** | **Diferir** `maestros-m1a-centro-west` |
| ABM grp | **Sí** | **Diferir** `maestros-m1a-grp` |

## Criterios de aceptación

1. Inventarios G0 en este slug (copy/validaciones/geometría/interacción).
2. IT Api: POST crea fila; GET por id; PUT; DELETE. Ledger con **id**.
3. UI: listado HAB (búsqueda 500px + tabla + Agregar + pager); datos 2-up `InputWid100` en `ds-dialog`; buscador HIS absorbido.
4. Sin `Vnn` de estructura ni seed Flyway.
5. `verify-report.md` sin silencios en la tabla de capacidades.
