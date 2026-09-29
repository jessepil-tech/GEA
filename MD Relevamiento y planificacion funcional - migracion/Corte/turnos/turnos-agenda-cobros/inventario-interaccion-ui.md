---
title: Inventario interacción UI — T5.1d cobros
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-cobros
---

# Inventario interacción UI — Cobros (G0)

Fuente: `asignacionTurnos.xhtml` · `infoTurno.xhtml` · `BBAgenda` / `BBAsignacionTurnos`.

## Rechazo elegibilidad

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| `$popUpObservacionesPacientes` | rechazo o error conexión (HIS `mostrarObs`) | modal, no X | overlay + Aceptar |
| buscador paciente **Aceptar** | HIS `selectConvenio` → `validarElegibilidad` | misma validación | popup si rechazo |
| north afiliado | mismo | `nroAfiliadoPaciente = null` | vaciar input |
| north convenio/plan | mismo | ids default + session | combo/label default |
| Autorizado | change afiliado OK | busca paciente si falta apellido; **no** popup | no swap |

## Pagar / saldo

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| `$popUpInfoPagar` | otorgar si hay coseguro | texto + Aceptar/Cerrar | confirmar continuar |
| `$popUpSaldoCtaCtePaciente` | HIS display flag | deuda disabled rojo + Aceptar | |

## Info turno

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Fila saldo/coseguro | `rendered` si hay valor | InputWid100 derecha, rojo si > 0 | no mostrar fila vacía |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Swap convenio en autorizado | Prohibido |
| Cerrar P-ORA-010 | Prohibido |
| Inventar monto de caja | Prohibido |
| Pedir smoke sin popup HIS (header Información, sin X) | Prohibido |
