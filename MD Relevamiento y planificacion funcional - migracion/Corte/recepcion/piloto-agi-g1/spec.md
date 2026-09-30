---
title: SDD — Piloto AGI G1 recepción autogestión
description: Segundo vertical del piloto — autogestión recepción (tótem) en stack nuevo.
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-13
phase_id: sdd.hospital.piloto-agi-g1
---

# Spec — Piloto AGI G1 (recepción autogestión)

`phase_id:` `sdd.hospital.piloto-agi-g1`  
Estado: **reviewed** (2026-08-13) — Q1–Q3 aceptados (defaults).

Padre: [piloto-agi-anunciador](../../anunciador/piloto-agi-anunciador/) (CU #1 **gate-done**).  
Inventario: [inventario-cu.md](../../anunciador/piloto-agi-anunciador/inventario-cu.md) · **G1**.

## Problema

El CU #1 midió Identity → Hospital-Api → Angular en un vertical de **lectura**.
G1 es el flujo dominante de **AGI** (tótem recepción): identifica paciente → turnos /
convenio-credencial → ticket. Hay que **medir** ese costo en el stack nuevo, sin
cablear el WAR JSF ni arrastrar VALIDADORES/hardware en el primer corte.

## Resultado deseado

Un **camino feliz mínimo** en `Hospital-Api` + ruta Angular (tótem o shell) que:

1. Identifique un paciente (documento) contra datos en **Postgres** (seed).
2. Liste turnos / opciones disponibles para ese paciente (seed / read-model).
3. Confirme una recepción y emita un **ticket** (registro + payload imprimible mock).
4. Use **JWT Identity** (o API Key de terminal) — sin login propio del WAR.

## Actores

| Actor | Necesidad |
|-------|-----------|
| Paciente en tótem | Autogestionar recepción sin mostrador |
| Terminal AG (kiosk) | Sesión de terminal + opción de menú |
| Operación / piloto | Medir horas reales vs estimación Fase 3 |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Configurar / seleccionar **terminal AG** y opción (seed; sin ABM completo) |
| RF-2 | Identificar paciente por tipo + nro documento (lookup Postgres) |
| RF-3 | Listar turnos / prácticas disponibles del paciente (read-model seed) |
| RF-4 | Confirmar recepción del turno seleccionado → crear registro `recepcion_agi` + ticket |
| RF-5 | Front (Hospital-Web o ruta tótem) autentica vía Identity y consume Hospital-Api |
| RF-6 | Errores de negocio explícitos (paciente no encontrado, sin turnos) sin caer a 500 |
| NFR-1 | Postgres local / Dev Services; **no** Hibernate a Oracle 11.2 |
| NFR-2 | Auth solo Identity (`iss`/`aud` alineados); sin emisor propio en Api |
| NFR-3 | Slice medible en ≤ 1–2 sprints de piloto; sin ValidadorWS online en v1 |
| INV-1 | No microservicio Core; catálogo mínimo = tablas compartidas en Hospital-Api |
| INV-2 | Identity no guarda turnos ni pacientes clínicos |
| INV-3 | Paridad PL/SQL / golden master **fuera** de este slice (oleada G1-b) |

## Criterios de aceptación

1. **Dado** terminal seed activa y JWT válido, **cuando** `POST` identificación con doc seed, **entonces** 200 con paciente demo.
2. **Dado** paciente demo, **cuando** `GET` turnos, **entonces** ≥1 turno seed.
3. **Dado** turno seed, **cuando** `POST` confirmar recepción, **entonces** 201 con `ticketId` + datos de espera (lugar mock).
4. **Dado** doc inexistente, **cuando** identifica, **entonces** 404/422 de negocio (no 500).
5. Sin Bearer → 401 en endpoints G1.
6. UI mínima: identificar → listar turnos → confirmar → ver ticket (texto).
7. SDD con tasks + verify-report antes de ampliar a ValidadorWS / impresora / golden master.

## No objetivos (este slice)

- Cablear `AGI.war` / JSF a Identity
- ValidadorWS / afiliación online / token obra social
- Lectora física / impresora BIRT real (mock texto OK)
- Triage (G2), selector anunciador (G3) salvo enlace opcional post-ticket
- ABM completo de terminales / convenios / `catalogo`
- Oleada B Identity (hash legacy)
- Remotos ADO obligatorios

## Supuestos

- Paciente y turnos **seed** bastan para medir el stack (como CU #1).
- Flujo legacy de referencia: `BBIdentificacionPaciente` + `BBRecepcionarPaciente`
  (`DISPLAY`: TURNOS → CONVENIO/CREDENCIAL → TICKET); v1 colapsa convenio/credencial
  a datos del turno seed.
- API Key de terminal puede coexistir con JWT humano (operador de demo).

## Clarify cerrado (2026-08-13)

| Id | Decisión |
|----|----------|
| Q1 | UI en Hospital-Web `/agi/recepcion` (no app tótem aparte en v1) |
| Q2 | Solo JWT Identity; API Key de terminal → G1-b |
| Q3 | Ticket + tabla `recepcion_agi` (sin cola Oracle real) |

## Compuertas

| Gate | Criterio |
|------|----------|
| `…gate-spec` | Spec reviewed |
| `…gate-plan` | Plan reviewed |
| `…gate-tasks` | Tasks con verificación |
| `…gate-verify` | verify-report PASS/WAIVED |
| `…gate-done` | CA 1–6 + smoke |
