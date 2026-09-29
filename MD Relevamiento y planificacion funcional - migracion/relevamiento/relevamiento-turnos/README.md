---
title: Relevamiento — Turnos / agenda / grilla
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.relevamiento-turnos
---

# Relevamiento — Turnos / agenda / grilla

`phase_id:` **`sdd.hospital.relevamiento-turnos`**  
Estado: **active** · **2026-09-17**  
Capa 3: [`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md) ·
[`gobierno-migracion.md`](../../canon/gobierno-migracion.md)

Entregable de relevamiento modular (**greenfield**). No sustituye SDD de implementación.

| Doc | Rol |
|-----|-----|
| [pipeline.md](pipeline.md) | Configuración → generación → operación (A1–A9) |
| [maestros.md](maestros.md) | Maestros / seguridad / ¿bloquea si falta? |
| [inventario.md](inventario.md) | Capacidades config + ops + ciclo |
| [matriz.md](matriz.md) | Package `TS.TURNOS` / UI / jobs ↔ destino |
| [cortes.md](cortes.md) | T0–T7 por dependencia |

## Contexto

| Pieza | Rol |
|-------|-----|
| Módulo menú `ATENCION_TURNOS` | Entrada HOSPITAL_2 (label TURNOS) |
| `HOSPITAL_2/pages/turnos/*` | Agenda, otorgar, consultas |
| `HOSPITAL_2/pages/atencionTurno/*` | Generación/suspensión/horarios de grilla |
| `HOSPITAL_2/pages/configuracion/.../habTurnos*` | Habilitación |
| Package `TS.TURNOS` | Lógica (hab, gen grilla, reserva, otorga, ciclo) |
| `ts.turno` | Slot canónico (DDL ya en Api V31) |
| AGI `/agi/recepcion` | **Consumidor** de turnos OTORGADO — no es este módulo |

## Veredicto (Fase E) — 2026-08-27

| Pregunta | Respuesta |
|----------|-----------|
| ¿Viable con reglas actuales? | **Sí con prerrequisitos** — DDL del slot existe; config T2–T4 **gate-done**; T5 operación **gate-done** 2026-09-07 |
| ¿Paridad de configuración? | **Parcial avanzada** — T2 hab gate-done; T3 **gate-done**; T4 generación **gate-done**; T5 operación **gate-done** |
| Prerrequisitos bloqueantes | T6 resto (suspender/reemplazo/cola/vencidos) / side-effects T7 antes de declarar módulo TURNOS **completo** |
| Primer CU recomendado | T6 padre diferido (`turnos-ciclo-vida`); T5.1b–e, persist, prep/req, T5.2 y T6.1 **gate-done** |
| Fuera de alcance inmediato | Múltiples/pre-agenda; APIs Apross; BIRT turno; SMS/mail |
| ¿Abrir spec de “migrar Turnos”? | **No** — cortes T5+ / hijos; no el módulo entero |

## Profundidad de análisis

El pipeline A1–A9 está cerrado. El grafo no: la matriz de firmas es una **muestra**,
las FK de `ts.turno` siguen diferidas y el índice Java→PL/SQL ve 3 literales `TURNOS.*`
frente a 77 funciones / 22 procedimientos del BODY. Los hijos T5.x no heredan esta
tabla: cada corte cierra las capas que toca
([`loop-migracion-corte.md`](../../canon/loop-migracion-corte.md) paso 3).

| Capa | Qué cerraría | Estado | Evidencia |
|------|--------------|--------|-----------|
| Pipeline A1–A9 | Etapas configuración → operación | cerrado | [`pipeline.md`](pipeline.md) |
| Escritores cruzados | Quién más escribe `ts.turno` y tablas de hab | muestra | AGI consume `OTORGADO`; Recepción marca `RECEPCIONADO`; `MigrarTurnoVencidoJob` también purga cola y anunciador |
| Procesos programados | Cada job se porta / difiere / N/A | muestra | Nombrados en [`inventario.md`](inventario.md); decisión por job en [`relevamiento-procesos-programados/`](../relevamiento-procesos-programados/) — no en este A–C |
| Firmas del package | Universo `TS.TURNOS` (77 fn / 22 proc) | muestra | [`matriz.md`](matriz.md) § «Función (muestra)»; `indice-legacy/java-package.tsv` = 3 literales |
| Integridad referencial | FK de `ts.turno` a personal, grupos, motivos, call center, internación | muestra | Padres conocidos; FK **diferidas** (`pendientes-solo-oracle.md` T1 / RF-3) |
| Reportes e integraciones | BIRT turno, mail/SMS, APIs / validadores | diferido(turnos-notificaciones-birt) | T7; P-ORA-010 elegibilidad en pantalla |

**Riesgo:** tratar el listado AGI de OTORGADO + seed IT como “Turnos migrado”. Eso es consumo de recepción, no el pipeline que da vida a la oferta.

**Firma de proceso:** Fases A–C documentadas. Seed **no** cuenta como cierre de maestros.  
Capa 4: … [`turnos-agenda-info-turno/`](../../cortes/turnos/turnos-agenda-info-turno/) (**T5.1e gate-done**) · [`turnos-agenda-info-turno-persist/`](../../cortes/turnos/turnos-agenda-info-turno-persist/) (**gate-done**) · [`turnos-agenda-info-turno-prest/`](../../cortes/turnos/turnos-agenda-info-turno-prest/) (**gate-done**) · [`turnos-agenda-reasignar/`](../../cortes/turnos/turnos-agenda-reasignar/) (**T6.1 gate-done**) · [`turnos-agenda-sobreturno/`](../../cortes/turnos/turnos-agenda-sobreturno/) (**T5.2 gate-done**) · [`turnos-agenda-repetidos/`](../../cortes/turnos/turnos-agenda-repetidos/) (**T5.3 gate-done**).
