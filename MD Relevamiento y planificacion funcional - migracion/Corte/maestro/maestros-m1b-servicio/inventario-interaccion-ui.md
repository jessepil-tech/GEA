---
title: Inventario interacción UI — M1b servicio / vínculo
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1b-servicio.interaccion
---

# Inventario interacción UI — M1b (G0)

## Servicio

| Control | Disparador | Efecto | Web v1 |
|---------|------------|--------|--------|
| North ajax + Buscar | tipear/clic | 0/N dialog; 1 carga | igual |
| Agregar | clic | `addServicio=true` formulario vacío | |
| Eliminar | confirm **Servicio** | DELETE | modal texto 1:1 (no “registro”) |
| Aceptar | clic | insert/update | toast |

## Vínculo

| Control | Disparador | Efecto | Web v1 |
|---------|------------|--------|--------|
| North servicio + Buscar | ajax | buscador / carga | searchBar: q + centro + amb + Buscar |
| Filtro centro (`idCentroAte`) | select buscador | restringe listado; vacío = todos | `GET …/servicios-centro?idCentroAte=` |
| Centro en dialog (PK) | disabled en edición | no editar par | selects deshabilitados al editar |
| West datos | disabled sin id | hoja datos | única hoja v1 |
| Overlay | closable típico HIS | medir dialog | no cerrar por drag-select |
