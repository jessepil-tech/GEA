---
title: Inventario copy — T6.5 reemplazo profesional
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo.copy
---

# Inventario copy — Reemplazo profesional

Fuente: `Resources.properties` + `MessageBundle` + xhtml.

| msg.key / constante | Uso HIS | Este corte |
|---------------------|---------|------------|
| `reemplazar_agenda_turnos` | pageTitle | sí · «Reemplazar Agenda de Turnos» |
| `profesional` `(*)` | north origen | sí |
| `buscar` | lupa origen / reemplazante | sí · icono pegado (chrome Agenda) |
| `limpiar_profesional` | N/A en esta hoja HIS (sí en Agenda) | tooltip X origen / reemplazante |
| `centro_atencion` / `servicio` | north disabled | sí |
| `fecha_desde` / `fecha_hasta` / `hora_desde` / `hora_hasta` | north | sí |
| `domingo`…`sabado` / `feriado` | checks | sí |
| `consultar` | botón 150px | sí |
| `profesional_reemplazante` `(*)` | north | sí · «Profesional Reemplazante» |
| `motivo_reemplazo` `(*)` | combo north | sí · «Motivo Reemplazo» |
| `reemplazar_agenda` | south | sí · «Reemplazar Agenda» |
| `quitar_reemplazo` | south | sí · «Quitar Reemplazo» |
| `volver` | south | sí |
| `desea_reemplazar_el_profesional` | confirm HIS | modal DS · «¿Desea reemplazar el profesional?» |
| `desea_quitar_el_reemplazo` | confirm HIS | modal DS · «¿Desea quitar el reemplazo?» |
| `no_se_encontraron_registros` | empty table | sí |
| `reservado` / `sobreturno` / `reemplazado` / `suspendido` / `inhibido` | footer leyenda | sí |
| `REQUIRED_PERSONAL` / `Debe ingresar el Personal` | BB consultar | toast WARN |
| `DEBE_INGRESAR_PERSONAL_REEMPLAZANTE` | BB | «Debe ingresar el personal reemplazante» |
| `DEBE_INGRESAR_MOTIVO_REEMPLAZO` | BB | «Debe ingresar el motivo de reemplazo» |
| `DEBE_SELECCIONAR_AL_MENOS_UN_TURNO` | BB | «Debe seleccionar al menos un turno» |
| `WRONG_INTERVAL_DATE_3` / `WRONG_INTERVAL_HOUR` | BB | contrato T5 |
| `REEMPLAZO_RELIAZADO_EXITOSAMENTE` | toast INFO | **copy HIS** «Reemplazo realizado exitosamente» (nombre constante: rareza, no corregir) |
| `REEMPLAZO_QUITADO_EXITOSAMENTE` | toast INFO | «Reemplazo quitado exitosamente» |
| SP `Existen turnos solapados para este personal.` | ORA-20001 | **contrato** |
| SP `No se puede reemplazar parcialmente el personal de un turno otorgado.` | ORA-20000 | contrato |
