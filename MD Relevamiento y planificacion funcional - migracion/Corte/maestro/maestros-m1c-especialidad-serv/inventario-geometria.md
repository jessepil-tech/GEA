---
title: Inventario geometría — especialidad_serv
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv.geometria
---

# Inventario geometría — G0

HIS: include west de `servicioCentro.xhtml` (layoutPane **N/A** HAB).

Tabla: `paginator rows=7` HIS → HAB 10/20/50/100. Columnas:

| Columna | width HIS |
|---------|-----------|
| número orden | 45, right |
| especialidad | grow |
| activo | 80, center, check disabled |
| cerrar ate amb sin diag | 80, center |
| atención enfermería | 55, center |
| modalidad | grow |
| ctd etiquetas | 80, right |
| mensaje espera | grow |
| permite internación | 70, center |
| tipo internación | grow |
| acciones | 70 |

Dialog `popupEspecialidadServ` **600px**, `closable=false`, no drag. Label `td width=100px`.
Campos `InputWid100` = `calc(100% - 10px)` de la celda. Una `tr` = un campo (no 2-up).
Textarea `rows=3`. Botones Aceptar/Cerrar `Wid150px`. Select especialidad `disabled` si no agregando.

Filtros HAB (no están en la hoja): q 320px · centro 258px · servicio 258px (mismo token M1b).
