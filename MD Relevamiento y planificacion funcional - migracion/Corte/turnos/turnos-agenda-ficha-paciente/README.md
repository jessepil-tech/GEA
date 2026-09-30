---
title: SDD — T5.1 · agenda ficha paciente + west paridad
description: Buscadores north paciente/convenio; west agenda (ficha, rango horas, otros centros); extiende /turnos/agenda.
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-ficha-paciente
---

# T5.1 — Ficha paciente en agenda (`turnos-agenda-ficha-paciente`)

**Estado:** **gate-done** · Clarify **FIRME** Camino 1 · G0–G6 **PASS** 2026-09-08.  
Capa 4: [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md)

Padre capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) · hijo **T5** [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (diferido D-TUR-24).  
Prerrequisito: **T5 gate-done** (núcleo reservar/otorgar/liberar).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance, Clarify, RFs, fuera de alcance |
| [plan.md](plan.md) | Capas + cortes G0–G6 + riesgos |
| [tasks.md](tasks.md) | Checklist implementación |
| [verify-report.md](verify-report.md) | **PASS / gate-done** · smoke ops 2026-09-08 |
| [inventario-copy-msg.md](inventario-copy-msg.md) | Copy delta T5.1 — **G0 done** |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | Disparadores north/west — **G0 done** |
| [inventario-validaciones.md](inventario-validaciones.md) | Validaciones buscadores — **G0 done** |
| [inventario-ddl-gaps.md](inventario-ddl-gaps.md) | DDL G1 — **G0 done** |

**No es** el shell turnero 170px (`asignacionTurnos.xhtml` accordion completo) ni la pantalla `datosPaciente.xhtml` (tabs ABM) ni elegibilidad WS (`turnos-agenda-elegibilidad-cobros`).

**Sí es:** completar la **misma ruta** `/turnos/agenda` con contexto operativo de paciente (north + west legacy `agenda.xhtml`) que T5 dejó en ids demo / panel incompleto.

Legacy: `BBAgenda.buscarPacienteAgenda` · `buscarConvenioAgenda` · `formMenuPaciente` · `formMenuTurnosOtrosCentros` · `Turnos.selectTurnosOtrosCentros`.
