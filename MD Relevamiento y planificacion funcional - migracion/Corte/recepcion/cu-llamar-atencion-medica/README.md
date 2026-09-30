---
title: SDD — CU Llamar atención médica (escritor clínico anunciador)
description: Primer writer P1 — lista espera ATENCION_MEDICA → ts.llamado_anunciador.
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# CU — Llamar atención médica (escritor clínico)

**Estado:** **gate-done** 2026-09-16 (paso 8). Clarify **1–4 firmado**. E3 UI
(`/ambulatoria/espera-atencion`, gear → Llamar → TV). Writer E1–E2 en
[`cola-b-llamar/`](../cola-b-llamar/) (`POST …/espera-amb/{id}/llamar`)
**gate-done**. Acceso = `diferido(acceso)` (emisor Identity).

Primer vertical **P1** del circuito anunciador clínico (no recepción). Reutiliza
[`AnunciadorWritePort`](../../../../../Hospital-API/core/src/main/java/com/grupogea/hospital/starter/core/anunciador/AnunciadorWritePort.java)
y el ciclo TV ya **gate-done** en
[`ciclo-vida-llamado-anunciador/`](../../anunciador/ciclo-vida-llamado-anunciador/).

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Inventario · RF · Clarify · CA |
| [plan.md](plan.md) | Cortes E0–E4 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger E3 2026-09-16 |

Instalación de referencia: **genérica (`cliente="TS"`)**. `diferido(multi-instalacion)`.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrado) |
| Reservas | liberadas |
| Universo firmado | [spec.md](spec.md) · Clarify 1–4 |
| Fixture | resuelto (turno 6 / cola 5010 vigentes; llamado 6 → 26) |
| Evidencia | 11 filas **verificado** ([verify-report.md](verify-report.md)) |
| Diferidos abiertos | `diferido(acceso)` Identity · `cu-llamar-atencion-medica-ampliar` · `cu-llamar-atencion-medica-norte` · jobs · `diferido(multi-instalacion)` |
| Próximo paso | ninguno en este corte |

**gate-done** 2026-09-16. Ruta `/ambulatoria/espera-atencion`, menú ATENCION MEDICA,
acto gear → LLAMAR PACIENTE → confirm → TV. Writer E1–E2 =
[`cola-b-llamar/`](../cola-b-llamar/) **gate-done**. Acceso `diferido(acceso)`.

Jobs: `AtencionAutomaticaColaEsperaAmbJob` → **N/A** este corte (genera atención).
Purga cola / apaga TV → `diferido(relevamiento-procesos-programados)`.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Recepción (circuito ya migrado) + ATENCION MEDICA UI | **liberado** |
| BODY / package | no se porta ATENCION ni ANUNCIADORES; consume writer `cola-b-llamar` | n/a |
| Rango Flyway | ninguno de este slug | n/a |
| Tablas `ts` que escribe | `llamado_anunciador` vía POST Cola B (owner `cola-b-llamar`) | UI dispara el mismo INSERT |
| Rama | local Hospital-Web | sin commit pedido |

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.turno` id=**6** `RECEPCIONADO` | no | T5 e2e-mostrador | **vigente** — no borrar (tampoco 1–5) |
| `ts.cola_espera_serv_amb` id=**5010** (`CLINICA MEDICA`, turno 6, recep_amb 11) | no | AGI confirmar | **vigente** — no borrar (tampoco 5005–5009) |
| `ts.llamado_anunciador` id=**6** (`ATENCION_MEDICA`, cola 5010, ANU-DEMO 1001) | **sí** (UI Llamar) | NFR re-llamar 5010 en 6b | **reemplazado** por id=**26** (no borrar id=5) |
| Paciente `20001` · centro `1001` · anunciador `1001` | no | seed | **vigente** |

## Legacy (happy path)

`listaEsperaAtencionMedica.xhtml` → `BBListaEsperaAtencionMedica.actionBtnLlamarPacienteColaEsperaPersonal`
→ `Anunciadores.insertAnunciarPacienteConCola` → `TS.ATENCION.f_set_paciente_anun_cola`
→ `TS.ANUNCIADORES.p_insert_llamado_anunciador` (`tipo_atencion='ATENCION_MEDICA'`).

## No es este SDD

| Capacidad | Destino |
|-----------|---------|
| Llamar Cola A / AGI espera | Ya migrado (B.1 / M2) |
| Cabecera re-call / cancelar→quitar | Diferido hijo / Fase 4 |
| GYE / consultorio / especialidades | Clones P1–P2 (otros slugs) |
| Llamar en menú Cola B recepción | **Clarify aparte** — no inventar aquí |
| ABM anunciador / diccionario | P3 |

Padres / matriz: [`relevamiento-node-anunciador/matriz.md`](../../../relevamiento/relevamiento-node-anunciador/matriz.md),
[`mapa-menu-hospital-web.md`](../../../relevamiento/mapa-menu-hospital-web.md).
