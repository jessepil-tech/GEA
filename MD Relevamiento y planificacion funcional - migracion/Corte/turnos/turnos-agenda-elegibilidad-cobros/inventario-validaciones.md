---
title: Inventario validaciones — T5.1c elegibilidad
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros.validaciones
---

# Inventario validaciones — Elegibilidad (G0)

Fuente: `BBAgenda.validarElegibilidad` / `validarElegibilidad(boolean)`.  
Copy MessageBundle: mismas constantes T5 [`inventario-validaciones.md`](../turnos-agenda-otorgar/inventario-validaciones.md).

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| Convenio no exige validador | `convenioValidElegibilidad` false | campos disabled; sin icono | no llamar seed |
| Afiliado requerido si valida por carnet | `NRO_AFILIADO_ELEGIBILIDAD_REQUIRED` | toast/warn | mismo mensaje |
| Documento requerido si `validaNroDocumento` | `NRO_DOCUMENTO_ELEGIBILIDAD_REQUIRED` | toast/warn | mismo mensaje |
| Autorizado | `mensajeValidador.estadoAutorizado` | icono verde | seed `autorizado=true` |
| Rechazado | `estadoRechazado` | icono rojo + mensaje | seed `autorizado=false` + `mensaje` |
| Error conexión | `estadoErrorConexion` | icono plug naranja | seed/API error mapeado (v1: mensaje técnico acotado; WS real diferido) |
| Pendiente | validador on, sin resultado | exclamation + `PENDIENTE` | — |
| Parse máscara | `Pacientes.parsearNroAfiliado` | máscara UI | strip `-` / espacio como HIS |
| Doc req `req_paciente='S'` | `initInfoConvenio` | solo esas filas | WHERE |

Sin CRUD. Error API en pantalla (toast), **sin** `/500`.
