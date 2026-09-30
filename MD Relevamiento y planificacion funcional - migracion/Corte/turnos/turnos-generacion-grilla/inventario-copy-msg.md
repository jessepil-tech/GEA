---
title: Inventario copy T4 — msg.* ↔ labels
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-generacion-grilla.copy
---

# Inventario copy — T4 generación grilla (G0)

Fuente UI: `Resources.properties` (keys lowercase).  
XHTML in-scope:

- `atencionTurno/generacionGrillaTurnos.xhtml`
- `atencionTurno/eliminarGrillaTurnos.xhtml`
- `atencionTurno/consultaAgendasGeneradas.xhtml`

Web destino (G5 done): `Hospital-Web/src/app/configuracion/pages/grilla-turnos-labels.ts`.

| msg.key | Valor properties | Constante Web | En UI |
|---------|------------------|---------------|-------|
| `generar_agenda_turnos` | Generar Agenda Turnos | `titleGenerar` | sí (GEN) |
| `eliminar_agenda_turnos` | Eliminar Agenda Turnos | `titleEliminar` | sí (ELIM) |
| `consulta_agendas_generadas` | Consulta de Agendas Generadas | `titleConsulta` | sí (CONS) |
| `opcion` | Opción | `opcion` | sí (3) |
| `servicio` | Servicio | `servicio` | sí (3) |
| `profesional` | Profesional | `profesional` | sí (3) |
| `equipo` | Equipo | `equipo` | sí label; filtro **diferido** D-TUR-17 |
| `centro_atencion` | Centro Atención | `centroAtencion` | sí (3) |
| `grp_prest_serv` | Grupo de Prestaciones por Servicio | `grpPrestServ` | sí |
| `grp_prest_profesional` | Grupo de Prestaciones por Profesional | `grpPrestProfesional` | sí |
| `grp_prest_equipo` | Grupo de Prestaciones por Equipo | `grpPrestEquipo` | diferido UI equipo |
| `fecha_desde` | Fecha Desde | `fechaDesde` | sí (GEN+ELIM) |
| `fecha_hasta` | Fecha Hasta | `fechaHasta` | sí (GEN+ELIM) |
| `hora_desde` | Hora Desde | `horaDesde` | sí (GEN+ELIM+CONS) |
| `hora_hasta` | Hora Hasta | `horaHasta` | sí (GEN+ELIM+CONS) |
| `domingo` | Domingo | `domingo` | sí (GEN+ELIM) |
| `lunes` | Lunes | `lunes` | sí |
| `martes` | Martes | `martes` | sí |
| `miercoles` | Miércoles | `miercoles` | sí |
| `jueves` | Jueves | `jueves` | sí |
| `viernes` | Viernes | `viernes` | sí |
| `sabado` | Sábado | `sabado` | sí |
| `feriado` | Feriado | `feriado` | sí (GEN chk + CONS leyenda) |
| `buscar` | Buscar | `buscar` | sí |
| `buscar_profesional` | Buscar Profesional | `buscarProfesional` | sí (dialog) |
| `buscar_equipo` | Buscar Equipo | `buscarEquipo` | diferido D-TUR-17 |
| `generar_agenda` | Generar Agenda | `generarAgenda` | sí (GEN) |
| `eliminar_agenda` | Eliminar Agenda | `eliminarAgenda` | sí (ELIM) |
| `volver` | Volver | `volver` | sí (3) |
| `consultar` | Consultar | `consultar` | sí (ELIM+CONS) |
| `observaciones` | Observaciones | `observaciones` | sí (GEN col) |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` | sí (3) |
| `especificacion_de_rango_horario` | Especificación de Rango Horario | `especificacionRangoHorario` | sí (ELIM) |
| `fecha` | Fecha | `fecha` | sí (ELIM+CONS) |
| `hora` | Hora | `hora` | sí (ELIM) |
| `duracion` | Duración | `duracion` | sí (ELIM+CONS) |
| `estado` | Estado | `estado` | sí (ELIM; legacy duplica columna) |
| `paciente` | Paciente | `paciente` | sí (ELIM+CONS) |
| `profesional_equipo` | Profesional/Equipo | `profesionalEquipo` | sí |
| `sobreturno` | Sobreturno | `sobreturno` | sí (footer) |
| `cancelado` | Cancelado | `cancelado` | sí (footer) |
| `reemplazado` | Reemplazado | `reemplazado` | sí (footer) |
| `inhibido` | Inhibido | `inhibido` | sí (footer) |
| `confirmacion` | Confirmación | `confirmacion` | sí (ELIM dialog) |
| `la_agenda_se_eliminara_los_turnos_quedaran_pendientes_desea_continuar` | La agenda se eliminará y los turnos otorgados quedarán pendientes para su reasignación. ¿Desea continuar? | `confirmEliminarAgenda` | sí |
| `aceptar` | Aceptar | `aceptar` | sí |
| `cancelar` | Cancelar | `cancelar` | sí |
| `resultado` | Resultado | `resultado` | sí (ELIM popup) |
| `turnos_a_reasignar_en_esta_agenda` | Turnos A Reasignar En Esta Agenda | `turnosAReasignar` | sí (ELIM popup) |
| `prestacion` | Prestación | `prestacion` | sí (popup) |
| `anio` | Año | `anio` | sí (CONS) |
| `grupo` | Grupo | `grupo` | sí (CONS) |
| `enero` … `diciembre` | Enero…Diciembre | `mesEnero`…`mesDiciembre` | sí (CONS) |
| `disponible` | Disponible | `disponible` | sí (CONS leyenda) |
| `completo` | Completo | `completo` | sí |
| `cancelados` | Cancelados | `cancelados` | sí |
| `con_turno` | Con Turno | `conTurno` | sí |
| `no_disponible` | No Disponible | `noDisponible` | sí |
| `turnos` | Turnos | `turnos` | sí |
| `tipo_doc` | Tipo Documento | `tipoDoc` | sí (CONS) |
| `nro_doc` | Número Documento | `nroDoc` | sí |
| `convenio` | Convenio | `convenio` | sí |
| `plan_convenio` | Plan Convenio | `planConvenio` | sí |
| `reservado` | Reservado | `reservado` | sí (CONS footer) |
| `imprimir` | Imprimir | `imprimir` | sí (CONS; BIRT) |

## Fuera / no usar en v1

| Key en properties | Nota |
|-------------------|------|
| `generacion_grilla_turnos` / `eliminar_grilla_turnos` / `eliminar_grilla` / `desea_eliminar_grilla` | Alternas **no** usadas en estos xhtml |
| Radio/filtro **equipo** + `buscar_equipo` + `grp_prest_equipo` | **diferido** D-TUR-17 |
| Impresión BIRT consulta | **done** Api+Web → Reports (`turnos-consulta-agendas-imprimir`); toast INFO legacy opcional pendiente |

## Regla anti-regresión

- No inventar/acortar títulos (`Generar Agenda Turnos`, no “Generación”).
- Confirm eliminar: texto **completo** del key largo.
- Feedback post-generar: toast + tabla `observaciones` (no solo HTTP 200).
