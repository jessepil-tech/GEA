---
title: SDD — Paridad recepción / cola HOSPITAL_2
description: Puesto recepción legacy — cola ticket + llamar + (fase) cola atención.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.paridad-recepcion-cola
---

# Spec — Paridad recepción / cola

## Problema

El piloto AGI midió stack (ticket + lista `recepcion_agi` + llamar seed). El
**puesto de recepción HOSPITAL_2** opera sobre tablas Oracle reales y dos colas
distintas. Seguir ensanchando `*_agi` crea un **fork**. Este SDD ancla paridad
al legacy.

## Dos colas (no mezclar)

| Cola | Tabla Oracle | UI legacy | Propósito |
|------|--------------|-----------|-----------|
| **A — Ticket recepción** | `TS.COLA_ESPERA_RECEP` | `cabeceraRecepcion.xhtml` + popups | Pendientes de llamar / llamar → Anunciador |
| **B — Atención post-recepción** | `TS.COLA_ESPERA_SERV_AMB` | `colaEspera.xhtml` | Filtros ricos + grid pacientes recepcionados |

## Evidencia legacy (rutas)

| Pieza | Path |
|-------|------|
| Cabecera llamar | `HOSPITAL_2/WebRoot/pages/recepcion/recepcionPaciente/cabeceraRecepcion.xhtml` |
| Cola B | `.../colaEspera.xhtml` |
| Bean Cola A | `HOSPITAL_2/.../BBRecepcionColaEsperaRecep.java` |
| Bean Cola B | `HOSPITAL_2/.../BBConsultaColaEsperaRecepcion.java` |
| SP llamar | `TS.RECEPCIONES.f_llamar_paciente_recepcion(id_cola?, id_recepcion, id_box)` |
| SP anunciador | `TS.ANUNCIADORES.p_insert_llamado_anunc_recep` |
| Body SP | `RDBMS/.../HIS 817 - PACKAGE RECEPCIONES BODY.sql` (~llamar) |

## Resultado deseado (programa)

1. **M1** Read-model Cola A en PG (schema `cola_espera_recep` 1:1 columnas críticas).
2. **M2** Comando llamar (siguiente + por id) portando lógica del SP + side-effect Anunciador.
3. **M3** UX: mostrar cola, últimos 10, poll/refresh, highlight pendientes.
4. **M4** (hermano) Read-model Cola B + filtros (tipo servicio / servicio / profesional / especialidad / estado).

Sesión: **box recepción** obligatorio para llamar (paridad `UserSession.boxRecepcion`).

## Requisitos (M1+M2 primero)

| Id | Requisito | Legacy |
|----|-----------|--------|
| RF-A1 | Listar pendientes `llamado='N'` por `id_recepcion`, orden prioridad+FIFO | J1 |
| RF-A2 | Conteo por `prefijo_ticket_ag` | cabecera |
| RF-A3 | Llamar siguiente (`id_cola` null) | `actLlamar` |
| RF-A4 | Llamar ítem seleccionado | `actLlamparPac` |
| RF-A5 | Throttle ~5s por recepción+puesto+tipo RECEPCION | SP |
| RF-A6 | Side-effect `llamado_anunciador` | `p_insert_llamado_anunc_recep` |
| NFR-1 | Schema PG alineado a Oracle (no `recepcion_agi` canónico) | — |
| NFR-2 | Regla WAIVE: no descartar actos legacy | [`regla-waiver-paridad-legacy`](../../../canon/regla-waiver-paridad-legacy.md) |

### M3 / M4 (diferidos con slug, no WAIVE)

| Id | Capacidad | Slice |
|----|-----------|-------|
| RF-A7 | Popup mostrar cola / últimos 10 | M3 |
| RF-A8 | Poll refresh | M3 |
| RF-B1… | Filtros + grid Cola B | M4 |

## Criterios de aceptación (gate M1+M2)

1. Tabla(s) PG documentadas vs columnas Oracle § plan.
2. IT: listar pendientes + llamar siguiente + llamar por id + throttle.
3. Tras llamar, existe fila consumible por display (`ts.llamado_anunciador` + evento WS `nuevos-llamados`).
4. Smoke / IT Quarkus PASS (`RecepcionColaResourceIT`).
5. **No** inventar columnas/read-models piloto en `public` ni reflotar `recepcion_agi`
   (retirado en V33) como destino de paridad de cola — la paridad es sobre `ts.*` según
   [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

## No objetivos

- Ensanchar piloto `*_agi` / UI `/agi/espera` como producto final — superado: el stack
  migró a `ts.*` (Fase 2 cutover) y el piloto `recepcion_agi` fue retirado en V33
- Triage GYE / `COLA_ESPERA_TRIAGE`
- Demanda espontánea ambulatoria + HC (`BBDemandaEspontanea`)
- Acciones menú Cola B (anular, reemplazar, CI, print) → M5 futuro.
  Llamar de la fila Cola B → [`cola-b-llamar/`](../cola-b-llamar/) (no es el menú).
- TTS en Hospital-Web (Anunciador)
- UAT impresora térmica (pospuesto hasta UAT + hardware)
