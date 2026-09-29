---
title: Inventario interacción UI — P3 ABM Anunciador / Terminal AG
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.anunciador-agi-config-abm.interaccion
---

# Inventario interacción UI — P3 ABM (G0)

Complementa copy/validaciones: **cómo** se dispara cada acto. Prohibido sustituir west/menú/icon-only por botones inline sin `diferido(slug)`.

## Anunciador (maestro)

| Control legacy | xhtml | Disparador | Efecto | Web v1 |
|----------------|-------|------------|--------|--------|
| North input | `anunciador.xhtml` | Enter/`p:ajax` + Buscar | Abre buscador o carga 1 | Dialog lista; no navegar a display |
| West menuitem Datos | disabled sin id | Clic | `datosAnunciador.faces` | hoja / tab |
| West Configuración avanzada | disabled sin id | Clic | `configuracionAvanzada.faces` | hoja **misma entidad** (no otro módulo) |
| West Ambiente | disabled sin id | Clic | `anunciadorAmbienteAmb.faces` | hoja vínculo |
| Agregar | west south 150px | Clic | limpia sesión → datos (alta) | mismo flujo |
| Eliminar | west south + `window.confirm` texto **anunciador** | Clic | DELETE físico + vuelve shell vacío | modal texto 1:1; **no** confirm genérico “registro” |
| Aceptar datos | south | Clic | insert/update | toast |
| Check multimedia | avanzada | ajax | habilita tipo + URL | igual |
| fileUpload logo/fondo | auto, 1 archivo, **sin** drag-drop | elegir archivo | BLOB sesión + preview | sin DnD |
| Quitar logo / Restablecer fondo | botones | Clic | null BLOB | |
| Restablecer valores | south + `window.confirm` | Clic | null logo+fondo + update | modal |
| Ambiente trash | col width 50 **icon-only** | Clic + `p:confirm` registro | DELETE vínculo | icon-only + modal |
| Ambiente Agregar | south `Wid150px` | Clic | dialog buscador 1200×550 | no ABM ambiente |

Menuitems serv/triage/esp-serv **no existen** en el west v1 (están en otros xhtml) → no añadir “por si acaso”.

## Terminal AG

| Control | Disparador | Efecto | Web v1 |
|---------|------------|--------|--------|
| North nombre + ajax | tipear/Buscar | buscador 1200×550 | |
| Centro north | readonly | no editar ahí | |
| West terminal / opciones | clic; opciones disabled sin id | hojas | |
| Agregar / Eliminar west | Eliminar = `p:confirm` **registro** | DELETE físico terminal | |
| Centro en datos | input ajax **o** icon-only search | dialog `buscar_centro_atencion` | icon-only; no botón texto extra |
| Aceptar datos | south | insert/update + required BB | |
| Grilla opciones: editar | icon pencil | popup 800 | no navegar ruta nueva |
| Grilla opciones: trash | icon + confirm registro | DELETE opción | icon-only |
| Tipo opción change | ajax | habilita combo triage **xor** recepción | mismos disabled |

## Diccionario

| Control | Disparador | Efecto | Web v1 |
|---------|------------|--------|--------|
| Filtro cols | contains | client filter HIS | C4 filtro equivalente |
| Lápiz | icon | popup edit | |
| Trash | icon + confirm registro | DELETE PK | |
| Agregar | `Wid150px` | popup alta | |
| Escuchar | `rendered="false"` | no | **no** portar TTS de esta pantalla (N3 display ya existe) |

## Anti-regresión

| Error | Síntoma | Regla |
|-------|---------|-------|
| Lista piloto “abrir TV” como única UI | No se puede dar de alta | C4 = ABM; abrir display es acción **secundaria** (otra ventana) |
| Inventar tab serv/triage | Scope creep | `diferido(anunciador-config-avanzada)` |
| Baja lógica `activo` en anunciador | Columna no existe | DELETE + error FK |
| Confundir checkbox `activo` terminal con Eliminar | Terminal “apagado” sigue en BD HIS si no se pulsa Eliminar | Ambos: flag **y** DELETE |
| Botón Escuchar diccionario | Script muerto | no implementar |

## Verify

Fila **Inventario interacción** ↔ componentes Web en [verify-report.md](verify-report.md) al C4.
