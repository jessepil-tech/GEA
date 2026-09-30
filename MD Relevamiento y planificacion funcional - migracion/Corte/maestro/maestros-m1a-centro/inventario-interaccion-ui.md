---
title: Inventario interacción UI — M1a centro de atención
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1a-centro.interaccion
---

# Inventario interacción UI — M1a (G0)

| Control legacy | xhtml | Disparador | Efecto | Web v1 |
|----------------|-------|------------|--------|--------|
| North input | shell | Enter/`p:ajax` + Buscar | 0→dialog; 1→carga; N→dialog | listado HAB filtra; dialog buscador absorbido |
| West Datos | disabled sin id y sin `Agregando` | Clic | `datosCentroAtencion.faces` | N/A west; datos en `ds-dialog` |
| Agregar | west south 150px | Clic | sesión `Agregando` → datos alta | botón bajo tabla → dialog |
| Eliminar | west + `window.confirm` **registro** | Clic | DELETE + shell vacío | ícono fila + modal texto 1:1 |
| Aceptar datos | south | Clic | insert/update | toast + cierra dialog |
| Provincia combo | ajax | change | refresca localidad | igual |
| Centro dflt combo | ajax | change | refresca servicios dflt | GET; combo puede quedar vacío si no hay M1b |
| Overlay dialog | `closable=false` | no backdrop | — | drag-select en input no cierra |
| X / lupas depósito | datos (más abajo) | — | — | **no** en v1 |

`visible="#{bbCentroAtencion.displayBuscadorCentroAtencion}"` **no** auto-abre al navegar: hace falta Buscar o 0/N matches.
