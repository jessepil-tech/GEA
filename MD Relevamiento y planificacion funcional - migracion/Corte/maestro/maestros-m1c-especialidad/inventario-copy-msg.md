---
title: Inventario copy — M1c especialidad
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.maestros-m1c-especialidad.copy
---

# Inventario copy — M1c (G0)

Fuente: `HOSPITAL_2/.../Resources.properties`.

| msg.key | Valor | Web | Dónde |
|---------|-------|-----|-------|
| `especialidad` | Especialidad | `especialidad` | título 10003, north, campo `(*)`, columna |
| `buscar` | Buscar | `buscar` | north / searchBar |
| `buscar_especialidad` | Buscar Especialidad | `buscarEspecialidad` | dialog buscador HIS (absorbido) |
| `agregar` | Agregar | `agregar` | west 150px → HAB debajo |
| `eliminar` | Eliminar | `eliminar` | west / ícono fila |
| `aceptar` | Aceptar | `aceptar` | south 135px |
| `acciones` | Acciones | `acciones` | |
| `interconsulta` | Interconsulta | `interconsulta` | checkbox |
| `adulto_pediatrico` | Adulto / Pediátrico | `adultoPediatrico` | select `(*)` |
| `ADULTO` | ADULTO | `adulto` | option |
| `PEDIATRICO` | PEDIATRICO | `pediatrico` | option |
| `TODOS` | TODOS | `todos` | option (HIS `msg.TODOS`) |
| `desea_eliminar_especialidad` | ¿Desea eliminar la Especialidad? | `deseaEliminarEspecialidad` | modal baja |
| `no_se_encontraron_registros` | No se encontraron registros | `sinRegistros` | vacío tabla |

Toasts: `ROW_*_INFO` iguales a M1a/M1b.
