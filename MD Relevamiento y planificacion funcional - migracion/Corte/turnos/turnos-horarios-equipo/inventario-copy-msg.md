---
title: Copy — Turnos por equipo
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-horarios-equipo.copy
---

# Copy

Fuente: `turnosEquipo.xhtml` y las hojas de datos, grupo, prestaciones y horario.  
`Resources.properties` de HOSPITAL_2. Web: `horarios-turnos-labels.ts`.  
Las claves compartidas con T3 usan la misma constante.

| msg.key | Valor properties | Constante Web | En UI |
|---------|------------------|---------------|-------|
| `turnos_por_equipo` | Turnos por Equipo | `titleEquipo` | sí |
| `equipo` | Equipo | `equipo` | sí |
| `centro_atencion` | Centro Atención | `centroAtencion` | sí |
| `servicio` | Servicio | `servicio` | sí |
| `buscar` | Buscar | `buscar` | sí |
| `datos_equipo` | Datos Equipo | `datosEquipo` | sí |
| `observaciones` | Observaciones | `observaciones` | sí |
| `grupo_horario_prestaciones` | Grupo de Horario de Prestaciones | `grupoHorarioPrestaciones` | sí |
| `prestaciones` | Prestaciones | `prestaciones` | sí |
| `prestaciones_asociadas` | Prestaciones Asociadas | `prestacionesAsociadas` | sí |
| `horario_trabajo` | Horario de Trabajo | `horarioTrabajo` | sí |
| `nueva_vigencia` | Nueva Vigencia | `nuevaVigencia` | sí |
| `fecha_vigencia` | Fecha Vigencia | `fechaVigencia` | sí |
| `fecha_fin_vigencia` | Fecha Fin Vigencia | `fechaFinVigencia` | sí |
| `copiar_valores_vigentes` | Copiar Valores Vigentes | `copiarValoresVigentes` | sí |
| `disponibilidad_horaria` | Disponibilidad Horaria | `disponibilidadHoraria` | sí |
| `reserva_de_turno` | Reserva de turno (el properties trae un espacio inicial) | `reservaDeTurno` | sí |
| `reserva` | Reserva | `reserva` | sí |
| `inicio_fin` | Inicio / Fin | `inicioFin` | sí |
| `INICIO` / `FINAL` | INICIO / FINAL | `inicio` / `final` | sí |
| `cant_min_duracion` | Cant. Min. Duración | `cantMinDuracion` | sí |
| `cant_hs_libera` | Cant. Hs Libera | `cantHsLibera` | sí |
| `ctd_planes_conv` | Ctd. Planes Conv. | `ctdPlanesConv` | sí |
| `dia` / `hora_desde` / `hora_hasta` | Día / Hora Desde / Hora Hasta | `dia` / `horaDesde` / `horaHasta` | sí |
| `cod_prestacion` / `prestacion` | Código Prestación / Prestación | `codPrestacion` / `prestacion` | sí |
| `duracion_turno` | Duración Turno | `duracionTurno` | sí |
| `habilitada_web` / `habilitada_call_center` / `activa` | Habilitada Web / Habilitada Call Center / Activa | mismas de T3 | sí |
| `convenio` / `plan_convenio` / `buscar_convenio` | Convenio / Plan Convenio / Buscar Convenio | `convenio` / `planConvenio` / `buscarConvenio` | sí, popup del lápiz |
| `agregar` / `editar` / `eliminar` / `aceptar` / `cerrar` | mismos textos de T3 | mismas constantes | sí, ícono + `title` donde el xhtml es ícono |
| `leyenda` | Los campos marcados con (*) son obligatorios | `leyenda` | sí |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` | sí |
| `desea_eliminar_prestacion` | ¿Desea eliminar la Prestación? | `deseaEliminarPrestacion` | sí, confirm del grupo |
| `desea_eliminar_grupo_prestacion` | ¿Desea eliminar el Grupo Prestación? | confirm de la prestación | sí |
| `desea_eliminar_horatio_trabajo` | ¿Desea eliminar el Horario de Trabajo? | `deseaEliminarHorarioTrabajo` | sí, día y vigencia |
| `desea_eliminar_el_registro` | ¿Desea eliminar el registro? | `deseaEliminarRegistro` | sí, fila del popup convenio |
| `horario_especial` | Horario Especial | `horarioEspecial` | ítem visible y disabled |
| `inhibicion` | Inhibición | `inhibicion` | ítem visible y disabled |
| `ocupacion_turnos_convenio` | Ocupación Turnos Convenio | `ocupacionTurnosConvenio` | ítem visible y disabled |
| `ocupacion_turnos_plan_convenio` | Ocupación Turnos Plan Convenio | `ocupacionTurnosPlanConvenio` | ítem visible y disabled |

El filtro `filterBy` de la columna prestación no tiene clave propia. No se porta, igual que T3.
