---
title: Inventario copy — M1b servicio / vínculo
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1b-servicio.copy
---

# Inventario copy — M1b (G0)

Fuente: `HOSPITAL_2/.../Resources.properties`.

| msg.key | Valor | Web | Dónde |
|---------|-------|-----|-------|
| `servicio` | Servicio | `servicio` | título 10002, north, campo `(*)` |
| `buscar` | Buscar | `buscar` | north |
| `buscar_servicio` | Buscar Servicio | `buscarServicio` | dialog catálogo |
| `agregar` | Agregar | `agregar` | west 150px |
| `eliminar` | Eliminar | `eliminar` | west 150px |
| `aceptar` | Aceptar | `aceptar` | south 135px (servicio) |
| `acciones` | Acciones | `acciones` | west south |
| `desea_eliminar_el_servicio` | ¿Desea eliminar el Servicio? | `deseaEliminarElServicio` | modal baja catálogo |
| `servicio_centro` | Servicio Centro | `servicioCentro` | título 10204 · west datos |
| `centro_atencion` | Centro Atención | `centroAtencion` | filtro buscador 10204 (select) + columna; PK en dialog |
| `datos_servicio_centro` | Datos Servicio Centro | `datosServicioCentro` | accordion west |
| `ambulatorio_internado_todos` | Ambulatorio/Internado/Todos | `ambIntTodos` | buscador + columna + `(*)` datos |
| `desea_eliminar_servicio_centro` | ¿Desea eliminar el Servicio Centro? | `deseaEliminarServicioCentro` | modal baja vínculo |

Toasts: mismos `ROW_*_INFO` que M1a.
