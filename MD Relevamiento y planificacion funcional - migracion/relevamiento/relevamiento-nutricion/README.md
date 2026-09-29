---
title: Relevamiento — Nutrición
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.relevamiento-nutricion
---

# Relevamiento — Nutrición

`phase_id:` **`sdd.hospital.relevamiento-nutricion`**  
Estado: **active** · **2026-09-17**  
Capa 3: [`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md) ·
[`gobierno-migracion.md`](../../canon/gobierno-migracion.md)

Entregable de relevamiento modular (**greenfield**). No sustituye SDD de implementación.
**No hubo demo de negocio** de este módulo: evidencia = menú O1 + xhtml/beans + `ts.*`.

| Doc | Rol |
|-----|-----|
| [pipeline.md](pipeline.md) | Configuración → internado → planilla (A1–A9) |
| [maestros.md](maestros.md) | Maestros / seguridad / ¿bloquea si falta? |
| [inventario.md](inventario.md) | Capacidades config + ops + ciclo |
| [matriz.md](matriz.md) | Tablas / UI / BIRT ↔ destino |
| [cortes.md](cortes.md) | N0–N6 por dependencia (**no** spec) |

## Contexto

| Pieza | Rol |
|-------|-----|
| Módulo menú `NUTRICION` **id=130000** | Tile HIS → `/pages/nutricion/inicioNutricion` |
| Hojas operación `130200` | Planilla, listado internado, indicaciones vigentes |
| Hojas `130300` consultas | **Prestadas** de admisión internados (no son el núcleo dietético) |
| ABM maestro **id=11804** | `/pages/configuracion/nutricion/nutricion` — padre `dominios_enfermeria` **11800** bajo `ADMINISTRACION_GENERAL_NA` |
| Rol funcional `NUTRICIONISTA` | Gate de `BBInicioNutricion` |
| Destino Web | Tile `moduleOnly('NUTRICION')` → hoja `pending()` (disabled) |
| Destino Api / Identity | **Sin** DDL `ts.nutricion*` · **sin** rol `NUTRICIONISTA` |

**Anti-sesgo:** la planilla del día (`planillaNutricion`) es A7, no el módulo.
El maestro de dietas cuelga en **Configuración**, no en el path `pages/nutricion`.

Uso producción (dossier): ~3.756 accesos / **16 usuarios** — nicho operativo, no circuito ambulatorio.

## Veredicto (Fase E) — 2026-09-07

| Pregunta | Respuesta |
|----------|-----------|
| ¿Viable con reglas actuales? | **Sí con prerrequisitos** — schema `ts` Oracle/PG ya tiene las tablas; **Flyway Api no** |
| ¿Paridad de configuración? | **No** — ABM nutrición/incompatibilidades ausente |
| Prerrequisitos bloqueantes | Internación viva (censo/cama/sector) + Identity `NUTRICIONISTA` + DDL maestros |
| Primer CU recomendado | **N1** maestros `ts.nutricion` (+ no-compatible) **después** de N0 gate centro/rol |
| Fuera de alcance inmediato | Prescripción desde internación (médico); consultas 130301/130302; BIRT |
| ¿Abrir spec de “migrar Nutrición”? | **No** — cortes N0/N1; no el módulo entero |

## Profundidad de análisis

A–C de pipeline y maestros. No es un grafo cerrado de internación ni del BODY
de indicaciones: N3–N5 dependen del censo, que este módulo no recorrió.

| Capa | Qué cerraría | Estado | Evidencia |
|------|--------------|--------|-----------|
| Pipeline A1–A9 | Configuración → internado → planilla | cerrado | [`pipeline.md`](pipeline.md) |
| Escritores cruzados | Internación / censo / indicaciones que alimentan la planilla | muestra | Bloqueante N3–N5 = censo internación; prescripción médica fuera de alcance |
| Procesos programados | Cada job se porta / difiere / N/A | muestra | No se corrió `--jobs nutricion` en este A–C |
| Firmas del package | Universo del BODY de nutrición / indicación | muestra | [`matriz.md`](matriz.md) tablas/UI; no catálogo cerrado de firmas |
| Integridad referencial | FK de `ts.nutricion*` y padres de internación | muestra | Schema `ts` en dump; Flyway Api **no**; padres de censo no levantados aquí |
| Reportes e integraciones | BIRT nutrición y prescripción médica | muestra | Veredicto: BIRT y prescripción fuera de alcance inmediato; sin slug de corte en este A–C |

**Riesgo:** tratar el listado de internados + seed de dietas como “Nutrición migrado”.
Eso es consumo de internación, no el pipeline (rol → centro → catálogo → censo → planilla).

**Firma de proceso:** Fases A–C documentadas. Seed **no** cuenta como cierre de maestros.
Capa 4: **no abierta**.
