---
title: Inventario copy T3 — msg.* ↔ labels
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.turnos-horarios-grupos.copy
---

# Inventario copy — T3 horarios (g4-2)

Fuente: `turnosPersonal/*` + `turnosServicios/*` (in-scope) · `Resources.properties` (keys **lowercase** exactas).  
Web: `Hospital-Web/.../horarios-turnos-labels.ts`.

| msg.key | Valor properties | Constante Web | En UI |
|---------|------------------|---------------|-------|
| `turnos_por_profesional` | Turnos por Profesional | `titlePers` | sí |
| `turnos_por_servicio` | Turnos por Servicio | `titleServ` | sí |
| `profesional` | Profesional | `profesional` | sí |
| `centro_atencion` | Centro Atención | `centroAtencion` | sí |
| `servicio` | Servicio | `servicio` | sí |
| `buscar` | Buscar | `buscar` | sí |
| `grupo_horario_prestaciones` | Grupo de Horario de Prestaciones | `grupoHorarioPrestaciones` | sí |
| `prestacion` | Prestación | `prestacion` (columna nombre grupo) | sí |
| `cantidad_turnos_simultaneos` | Cantidad de Turnos Simultáneos | `cantidadTurnosSimultaneos` | sí |
| `prestaciones` | Prestaciones | `prestaciones` / tab | sí |
| `prestaciones_asociadas` | Prestaciones Asociadas | `prestacionesAsociadas` | disponible |
| `horario_trabajo` | Horario de Trabajo | `horarioTrabajo` / tab | sí |
| `cod_prestacion` | Código Prestación | `codPrestacion` | sí |
| `duracion_1ra_vez` | Duración 1ra Vez | `duracion1raVez` | sí |
| `duracion_turno` | Duración Turno | `duracionTurno` | sí |
| `habilitada_web` | Habilitada Web | `habilitadaWeb` | sí |
| `habilitada_call_center` | Habilitada Call Center | `habilitadaCallCenter` | sí |
| `activa` | Activa | `activa` | sí |
| `fecha_vigencia` | Fecha Vigencia | `fechaVigencia` | sí |
| `fecha_fin_vigencia` | Fecha Fin Vigencia | `fechaFinVigencia` | sí |
| `nueva_vigencia` | Nueva Vigencia | `nuevaVigencia` | disponible |
| `copiar_valores_vigentes` | Copiar Valores Vigentes | `copiarValoresVigentes` | sí (alta vigencia) |
| `disponibilidad_horaria` | Disponibilidad Horaria | `disponibilidadHoraria` | sí (sección días) |
| `dia` | Día | `dia` | sí |
| `hora_desde` | Hora Desde | `horaDesde` | sí |
| `hora_hasta` | Hora Hasta | `horaHasta` | sí |
| `cant_min_duracion` | Cant. Min. Duración | `cantMinDuracion` | sí (pers) |
| `cant_hs_libera` | Cant. Hs Libera | `cantHsLibera` | sí (pers) |
| `inicio_fin` | Inicio / Fin | `inicioFin` | disponible |
| `acciones` | Acciones | `acciones` | sí |
| `agregar` / `editar` / `eliminar` | … | mismos | sí (icon+title) |
| `aceptar` | Aceptar | `aceptar` | sí |
| `cerrar` | Cerrar | `cerrar` (dismiss popup) | sí |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` | sí |
| `leyenda` | Los campos marcados con (*) son obligatorios | `leyenda` | disponible |
| `desea_eliminar_prestacion` | ¿Desea eliminar la Prestación? | confirm borrar **grupo** | sí |
| `desea_eliminar_grupo_prestacion` | ¿Desea eliminar el Grupo Prestación? | confirm borrar **prestación asociada** | sí |
| `desea_eliminar_horatio_trabajo` | ¿Desea eliminar el Horario de Trabajo? | confirm borrar horario/día | sí |

## Diferido / técnico

| Ítem | Nota |
|------|------|
| `idPrestacion` en form | Sin `msg.*` en grilla legacy; campo técnico API PK |
| Menú: horario_especial, inhibicion, ocupación | Diferidos T3 (D-TUR-14/15); west disabled en UI |
| Subtítulo aclaratorio | **Eliminado** (no legacy) |
| `seleccionarGrupo` / `seleccionarHorario` | Strings vacíos (ayudas inventadas retiradas) |

## Shell pers (hijo `turnos-horarios-pers-shell`)

| msg.key | Valor properties | Constante Web | En UI |
|---------|------------------|---------------|-------|
| `datos_personales` | Datos Personales | `datosPersonales` | sí (west + sección) |
| `inicio_fin` | Inicio / Fin | `inicioFin` | sí (días) |
| `INICIO` | INICIO | `inicio` | sí (combo) |
| `FINAL` | FINAL | `final` | sí (combo) |
| `reserva` | Reserva | `reserva` | disponible |


## Buscador prestaciones (alta multi-select)

| msg.key | Valor properties | Constante Web | ¿En UI? |
| --- | --- | --- | --- |
| `buscar_prestacion` | Buscar Prestación | `buscarPrestacion` | sí (dialog) |
| `prestacion` | Prestación | `prestacion` | sí (filtro + columna) |
| `cod_prestacion` | Código Prestación | `codPrestacion` | sí |
| `buscar` | Buscar | `buscar` | sí |
| `aceptar` | Aceptar | `aceptar` | sí |
| `volver` | Volver | `volver` | sí (dialog) |
| `no_se_encontraron_registros` | No se encontraron registros | `sinRegistros` / `noSeEncontraronRegistros` | sí |
