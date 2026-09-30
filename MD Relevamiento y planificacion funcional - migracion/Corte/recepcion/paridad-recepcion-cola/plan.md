---
title: Plan — Paridad recepción / cola
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.paridad-recepcion-cola
---

# Plan — Paridad recepción / cola

## Principio

| Hacer | No hacer |
|-------|----------|
| Mapear Oracle → PG por tabla/columna | Extender `recepcion_agi` como canónico |
| Portar punto de invocación (`f_llamar_paciente_recepcion`) | Clonar package entero RECEPCIONES |
| Reusar display Anunciador si contrato de `llamado_anunciador` es 1:1 | Mezclar Cola A y Cola B en un solo endpoint |

## Cortes

| Slice | Entrega | Dependencias |
|-------|---------|--------------|
| **M1** | Flyway `cola_espera_recep` (+ FKs mínimas recepción/puesto/centro) + GET lista pendientes + conteo prefijo | Clarify cerrado (PG + seed) |
| **M2** | Command `LlamarColaRecepcion` (null \| id) + UPDATE + insert display anunciador + throttle | M1; WS anunciador |
| **M3** | UI puesto recepción (lista + llamar + últimos 10 + poll) | M1+M2 |
| **M4** | Cola B read-model + filtros (`f_cola_espera_serv_amb` o SQL equivalente) | Separado; no bloquea M1–M3 |

## Schema Cola A (columnas críticas → PG)

Fuente DTO: `HOSPITAL-BUSINESS/.../ColaEsperaRecep.java`

| Oracle | PG (propuesto) | Notas |
|--------|----------------|-------|
| `ID_COLA_ESPERA_RECEP` | `id_cola_espera_recep` BIGINT/PK | next-id GENERAL |
| `ID_RECEPCION` | `id_recepcion` | scope lista |
| `ID_PUESTO_RECEPCION` | `id_puesto_recepcion` | set al llamar |
| `ID_CENTRO_ATE` | `id_centro_ate` | |
| `PREFIJO_TICKET_AG` / `NRO_TICKET_AG` | mismos | display |
| `PRIORIDAD_RECEP` | `prioridad_recep` | default 4 |
| `FECHA_HORA_INGRESO_COLA` | `fecha_hora_ingreso_cola` | FIFO |
| `LLAMADO` | `llamado` CHAR(1) S/N | |
| `CTD_LLAMADOS` | `ctd_llamados` | |
| `RECEPCIONADO` | `recepcionado` | filtro últimos 10 |
| `FECHA_HORA_LLAMA_RECEP` | `fecha_hora_llama_recep` | |
| `ID_PERSONAL_LLAMA_RECEP` | `id_personal_llama_recep` | |
| `ID_PACIENTE` | `id_paciente` | nullable |
| … | resto bajo demanda | convenio, triage, terminal |

`llamado_anunciador` Oracle 1:1: **diferido**. M2 escribe **`ts.llamado_anunciador`** (V27)
con `id_cola_espera_recep` / `id_puesto_recepcion` (mismo contrato REST/WS del display Angular).

## Relación con piloto B.1

| Piloto B.1 | Este SDD |
|------------|----------|
| `POST .../recepciones/{id}/llamar` sobre AGI | M2 sobre `ts.cola_espera_recep` / `ts.llamado_anunciador` |
| Seed `ANU-DEMO` | Anunciadores por ambiente de recepción |
| Demostró patrón | **No** es el destino de paridad |

> **Avance 2026-08-25 (cutover ts):** el piloto `recepcion_agi` fue **retirado en V33**; el
> AGI y la cola operan sobre `ts.*` (`ts.cola_espera_recep` / `ts.cola_espera_serv_amb` /
> `ts.llamado_anunciador`). La paridad es sobre `ts`, no sobre `public.*` ni `*_agi`.

Puede quedar como fachada tótem; el puesto recepción migra aquí.

## Clarify (cerrado 2026-08-18)

| # | Pregunta | Decisión |
|---|----------|----------|
| 1 | `id_recepcion` / `id_puesto` | Tablas PG mínimas `recepcion` + `puesto_recepcion` (+ `centro_ate`) alineadas a Oracle; **no** bridge runtime a Oracle |
| 2 | Datos M1 | **Seed controlado** en Flyway. Sync/extract Oracle → **diferido a UAT** |
| 3 | Next-id | Reusar `sec_id_cola_espera_recep` (V10) vía `NextIdService` cuando M2 inserte |

**Destino BD = PostgreSQL desde ya.** M2 comando en Java (sin llamar Oracle en runtime del Api).

## Orden de trabajo

1. [x] Clarify → plan reviewed  
2. [x] M1 Flyway + GET + IT  
3. [x] M2 llamar + IT/smoke  
4. [x] M3 UI  
5. [x] M4 Cola B read + UI  
