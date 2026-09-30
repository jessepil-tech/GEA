---
title: Inventario interacción — especialidad_serv
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv.interaccion
---

# Inventario interacción — G0

| Disparador HIS | Web |
|----------------|-----|
| Agregar west | `Agregar` debajo de la tabla HAB |
| Lápiz fila | dialog edición; PK disabled |
| Basura + `confirm` JS | `app-confirm-dialog` copy 1:1 |
| Aceptar popup | POST/PUT + toast; overlay global recarga |
| Cerrar popup | cierra sin guardar (`msg.cerrar`) |
| `onChangePermiteInternacion` | limpia tipo; select disabled si check off |
| `visible` del dialog | no auto-abre; clic Agregar/lápiz |
| Overlay | `closable=false` → `closeOnBackdrop=false` |
