---
title: Inventario copy — T6.4 suspender grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender.copy
---

# Inventario copy — Suspender / quitar suspensión

Fuente: `Resources.properties` + xhtml.

| msg.key | Uso HIS | Este corte |
|---------|---------|------------|
| `suspender_agenda_turnos` | pageTitle suspender | sí |
| `quitar_suspension_agenda` | pageTitle quitar | sí |
| `opcion` / `servicio` / `profesional` / `equipo` | radio | equipo **label sí, filtro no** D-TUR-17 |
| `centro_atencion` / `servicio` | north | sí |
| `fecha_desde` / `fecha_hasta` / `hora_desde` / `hora_hasta` | north | sí |
| días sem | check north | sí (como T4 eliminar) |
| `consultar` | botón | sí |
| `suspender` / `quitar_suspension` | south | sí |
| motivo (popup) | `$popUpSeleccionMotivo` | sí suspender |
| `DEBE_SELECCIONAR_UN_MOTIVO_DE_SUSPENSION` | BB | toast WARN |
| `EQUIPO_REQUIRED_ERROR` | BB modo equipo | N/A D-TUR-17 |
| SP `Debe seleccionar algun turno a cancelar.` | ORA-20001 | **contrato** (plural/ortografía HIS) |
| SP `Debe seleccionar el motivo de cancelacion.` | ORA-20001 | contrato |
| SP `No se puede suspender parcialmente un turno otorgado.` | ORA-20000 | contrato |
