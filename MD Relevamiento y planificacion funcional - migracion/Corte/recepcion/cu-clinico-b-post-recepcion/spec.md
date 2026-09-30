---
title: SDD — CU clínico B · Post-recepción
description: Tras ticket G1 → estado espera / enlace Anunciador.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-b-post-recepcion
---

# Spec — CU-B Post-recepción

Padre: [`cu-clinico/`](../cu-clinico/). Depende de G1 gate-done (no de CU-A).

## Problema

Tras confirmar recepción el paciente queda en “sala de espera”, pero el stack nuevo
no expone un **tablero post-ticket** ni un enlace explícito al Anunciador.

## Resultado

1. Read-model: recepciones del día / en espera (`ESPERA_ATENCION` / `ESPERA_RECEPCION`).
2. UI mínima `/agi/espera` (lista + filtro destino).
3. Opcional: disparar o enlazar llamado en Anunciador (si hay anunciador seed).
4. Smoke + IT.

## Requisitos

| Id | Requisito | Paridad legacy |
|----|-----------|----------------|
| RF-1 | `GET /api/v1/agi/recepciones?destino=&desde=` lista | Tablero/cola (read) |
| RF-2 | Detalle por id (reusa ticket PDF URL) | Ticket post-cola |
| RF-3 | UI lista espera | Vista cola |
| RF-4 | Acción **llamar / anunciar** (puesto recepción → Anunciador) | **Sí en legacy** — ver abajo |
| NFR-1 | No reimplementar cola Oracle | — |

### RF-4 y legacy (obligatorio contrastar)

| Superficie legacy | ¿Tiene “llamar/anunciar”? |
|-------------------|---------------------------|
| **AGI tótem** (`BBRecepcionarPaciente`) | **No** — solo genera cola + imprime ticket |
| **HOSPITAL_2 recepción** (`cabeceraRecepcion.xhtml` `btnLlamar` → `actLlamar` / `llamarPaciente`) | **Sí** — botón llamar (y por ítem de cola) alimenta Anunciador |
| **ANUNCIADOR display** | Reproduce llamado (`anunciar` / TTS) cuando `LLAMAR=S` |

**Conclusión:** no se puede marcar RF-4 como WAIVE “porque era opcional”.  
Para **paridad del puesto de recepción** es **requerido**.  
En el gate CU-B actual quedó **diferido explícito** → slice **`cu-clinico-b1-llamar-recepcion`** (planificado; no olvidar).

Regla de equipo: *antes de WAIVE, verificar legacy; si existe, o se implementa o se agenda etapa posterior con slug SDD.*

## Criterios de aceptación

1. Tras confirmar G1, la recepción aparece en la lista.
2. Filtro por destino funciona.
3. Smoke PASS; G1 no se rompe.
4. **RF-4** (llamar): **no** en este gate — ver [`../cu-clinico-b1-llamar-recepcion/`](../cu-clinico-b1-llamar-recepcion/) (paridad `btnLlamar` HOSPITAL_2).

## No objetivos (este slice)

- Triage
- TTS / hardware de voz en el display (ya cubierto por Anunciador piloto)
- Implementar `actLlamar` / `llamarPaciente` (→ B.1)

## Deuda de paridad HOSPITAL_2 (además de RF-4)

La lista `/agi/espera` cubre el **read-model post-ticket AGI**. La cola de recepción
legacy es más rica; **no WAIVE** — diferido (post B.1 / Fase recepción):

| Capacidad legacy | Evidencia | Estado |
|------------------|-----------|--------|
| Llamar / llamar ítem | `cabeceraRecepcion.xhtml` `btnLlamar`, `actLlamparPac` | → **B.1** |
| Mostrar cola / últimos 10 llamados | mismos xhtml + popup | diferido (tras B.1) |
| Filtros tipo servicio / servicio / profesional / especialidad / estado | `colaEspera.xhtml` | diferido |
| Poll auto-refresh ~59s | `colaEspera.xhtml` `p:poll` | diferido |
| Triage / ctd llamados / minutos espera | columnas `colaEspera.xhtml` | diferido (triage = no objetivo B) |
