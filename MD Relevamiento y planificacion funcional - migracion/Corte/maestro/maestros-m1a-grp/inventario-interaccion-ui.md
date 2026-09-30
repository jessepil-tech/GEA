---
title: Inventario interacción UI — M1a grp
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-grp.interaccion
---

# Inventario interacción UI — M1a grp (G0)

| Control | Disparador | Efecto | Web v1 |
|---------|------------|--------|--------|
| North + Buscar | ajax / clic | 0/N dialog buscador; 1 carga datos | searchBar + tabla |
| Agregar | clic | form vacío, checkbox default S | dialog |
| Eliminar | confirm **Grupo Centro Atención** | DELETE | modal texto 1:1 |
| Aceptar | clic | insert/update | toast |
| Overlay buscador | `closable="false"` | no drag-select | `[closeOnBackdrop]="false"` |
