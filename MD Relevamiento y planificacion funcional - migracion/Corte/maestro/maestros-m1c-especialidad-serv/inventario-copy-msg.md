---
title: Inventario copy — especialidad_serv
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1c-especialidad-serv.copy
---

# Inventario copy — G0

Fuente: `HOSPITAL_2/.../Resources.properties`. West tab 10204 = `msg.especialidad`.

| msg.key | Valor | Web | Dónde |
|---------|-------|-----|-------|
| `especialidad` | Especialidad | `especialidad` | tab west, header dialog, columna, campo `(*)` |
| `numero_orden` | Número Orden | `numeroOrden` | columna 45px · campo `(*)` |
| `activo` | Activo | `activo` | check |
| `cerrar_ate_amb_sin_diag` | Cerrar Atención Medica sin Diagnóstico | `cerrarAteAmbSinDiag` | check (HIS sin tilde en Medica) |
| `atencion_enfermeria` | Atención Enfermería | `atencionEnfermeria` | check |
| `modalidad` | Modalidad | `modalidad` | texto max 2 |
| `ctd_etiquetas_modalidad` | Cantidad Etiquetas Modalidad | `ctdEtiquetasModalidad` | numérico |
| `mensaje_espera_serv_centro` | Mensaje Espera Servicio Centro | `mensajeEsperaServCentro` | textarea 3 filas |
| `permite_internacion` | Permite Internación | `permiteInternacion` | check |
| `tipo_internacion` | Tipo Internación | `tipoInternacion` | select |
| `HOSPITALARIA` | HOSPITALARIA | `hospitalaria` | option |
| `TRANSITORIA` | TRANSITORIA | `transitoria` | option |
| `AMBAS` | AMBAS | `ambas` | option |
| `centro_atencion` | Centro Atención | `centroAtencion` | filtro HAB + dialog (HIS padre west) |
| `servicio` | Servicio | `servicio` | filtro HAB + dialog |
| `leyenda` | Los campos marcados con (*) son obligatorios | `leyenda` | pie dialog |
| `agregar` | Agregar | `agregar` | HAB debajo |
| `buscar` | Buscar | `buscar` | searchBar |
| `aceptar` | Aceptar | `aceptar` | dialog 150px |
| `cerrar` | Cerrar | `cerrar` | dialog 150px |
| `editar` | Editar | `editar` | lápiz |
| `eliminar` | Eliminar | `eliminar` | basura |
| `acciones` | Acciones | `acciones` | columna 70px |
| `desea_eliminar_especialidad` | ¿Desea eliminar la Especialidad? | `deseaEliminarEspecialidad` | confirm |
| `no_se_encontraron_registros` | No se encontraron registros | `sinRegistros` | vacío |

Toasts: `ROW_*_INFO`. Warn internación: `Debe seleccionar el tipo de internacion`.
