---
title: Inventario interacción UI — M1c especialidad
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1c-especialidad.interaccion
---

# Inventario interacción UI — M1c (G0)

| Control | Disparador | Efecto | Web v1 |
|---------|------------|--------|--------|
| North + Buscar | ajax / clic | 0/N dialog buscador; 1 carga | searchBar + tabla |
| Agregar | clic | `addEspecialidad=true` form vacío | dialog |
| Eliminar | confirm **Especialidad** | DELETE | modal texto 1:1 |
| Aceptar | clic | insert/update | toast |
| Overlay buscador | `closable="false"` | no drag-select | `[closeOnBackdrop]="false"` |
