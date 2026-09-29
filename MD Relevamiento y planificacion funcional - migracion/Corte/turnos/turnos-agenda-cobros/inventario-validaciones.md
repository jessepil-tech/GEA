---
title: Inventario validaciones — T5.1d cobros
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-cobros.validaciones
---

# Inventario validaciones — Cobros (G0)

Fuente: `BBAgenda.validarElegibilidad` (rama rechazo) · `BBAsignacionTurnos` mensaje pagar.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| Rechazo o error conexión (salvo padrón activo) | swap convenio dflt | popup + north | devolver ids default + leyenda |
| Convenio default inexistente | HIS lee `ParamGeneral` | no 500 | mensaje + no swap ciego |
| Aceptar popup | `actionBtnCerrarMensajePaciente` | cierra | no write |
| Pagar: continuar / cerrar | `actionBtnAceptarMensajePagar` / Cerrar | sí otorga / no | igual T5 otorgar si Aceptar |
| Saldo > 0 en popup obs | `saldoCtaCte.saldo gt 0` | muestra monto | lectura |

Sin CRUD de facturación. Error API en toast, **sin** `/500`.
