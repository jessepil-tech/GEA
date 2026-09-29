---
title: Decisiones — Node ANUNCIADOR / llamado_paciente / 1:1 Oracle
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-08-20
phase_id: sdd.hospital.relevamiento-node-anunciador
---

# Decisiones y criterios

## 1. ¿Necesitamos mirror 1:1 de `LLAMADO_ANUNCIADOR`?

### Decisión vigente (2026-08-20) — SUPERSEDE 2026-08-18

**Sí: el canónico es `ts.llamado_anunciador` en el PostgreSQL migrado** (paridad de
nombres y columnas con Oracle). Regla de programa:

- [`regla-ddl-postgres-migrado.md`](../../canon/regla-ddl-postgres-migrado.md)
- Inventario: [`inventario-ddl-oracle-pg/`](../inventario-ddl-oracle-pg/)
- Cutover Api (plan, sin código aún): [`plan-cutover-api-schema-ts.md`](../../planificacion/plan-cutover-api-schema-ts.md)

La decisión del **2026-08-18** (diferir mirror; `llamado_paciente` como modelo de
display) queda **SUPERSEDED para schema**. Motivo: la estructura Oracle→PG ya existe
en `ts` con paridad de tablas/columnas; inventar shapes en `public` viola la regla
prioritaria de migración.

### Qué sigue siendo cierto del piloto

`llamado_paciente` (Flyway Api) fue un **puente temporal** del display:

1. `POST …/recepcion/colas/{id}/llamar` (y AGI) insertan fila.
2. `GET …/anunciadores/{id}/llamados` alimenta la TV.
3. `WS /ws/anunciadores/{id}` empuja la lista.

Misma **función de producto**; el **contrato de datos** debe realinearse a
`ts.llamado_anunciador` según el plan de cutover. Hasta ese PR, el puente es deuda
explícita — no diseño final.

### Implicaciones

| Área | Implicación |
|------|-------------|
| Front nuevo | DTOs pueden mapearse; SQL/persistencia → columnas legacy |
| Node Vue legacy | Apagar Node sigue vigente; datos desde Quarkus sobre `ts.*` |
| HOSPITAL_2 | Sigue en Oracle hasta corte de producto |
| Gaps no-tabla (packages, triggers, views) | [`pendientes-solo-oracle.md`](../../estado/pendientes-solo-oracle.md) |

---

## 2. Ocupación de ambientes (CU A3)

| | |
|--|--|
| Legacy | `GET …/ocupacionAmbientesActual/:id` + Socket `ocupacion-ambientes-cambio` + `OcupacionAmbientes.vue` |
| Destino | **No migrado** |
| Peso | Bajo–medio (pantalla auxiliar; no bloquea llamar/display) |

### Criterios para portar

Portar A3 en el próximo corte de anunciador **si**:

1. Alguna sala en piloto/UAT **muestra** ocupación hoy y no puede quedar en Vue+Node, **o**
2. Producto prioriza “apagar Node” completo en ese centro.

Diferir A3 **si**:

- Solo se usa en un subconjunto de anunciadores y el piloto es “llamados only”, **o**
- Los datos de ocupación aún dependen de tablas Oracle no seed-eadas en PG.

**Recomendación:** con decisión **apagar Node** (D-ANU-05), A3 pasa a corte
**N1** de [`apagar-anunciador-node`](../../cortes/anunciador/apagar-anunciador-node/) salvo Clarify = no se usa.

---

## 3. Diccionario / TTS

| | |
|--|--|
| Legacy | `GET …/diccionarioAnunciador` |
| Destino | **No migrado** |
| Criterio | Portar solo si el display nuevo debe **pronunciar** nombres con lexema hospitalario |

Sin TTS en sala → no bloquea. Si se porta: catálogo en PG + endpoint read; audio puede
seguir en el cliente.

---

## 4. Logos / fondo / packLogos

| | |
|--|--|
| Legacy | assets por anunciador + pack |
| Destino | **No migrado** (RF-6 ABM diferido en piloto) |
| Criterio | Portar con ABM anunciador (`catalogo` / feature anunciador-admin) |

Mientras tanto: assets estáticos en Hospital-Web o CDN.

---

## 5. Reloj (`fechaHora` / `actualizar-hora`)

**No portar** salvo requisito de reloj de servidor único en multi-sede. El cliente
puede usar hora local o NTP del SO.

---

## 6. Orden sugerido post-relevamiento

1. Smoke login + Cola A llamar + display WS — **hecho**.
2. N1–N3 + apagar Node (dev) — **hecho**.
3. Cutover persistencia → `ts.llamado_anunciador` — **hecho** (V27–V33).
4. **[`ciclo-vida-llamado-anunciador/`](../../cortes/anunciador/ciclo-vida-llamado-anunciador/)** — **gate-done** núcleo TV.
5. Writer clínico [`cu-llamar-atencion-medica/`](../../cortes/recepcion/cu-llamar-atencion-medica/) (Clarify).
6. Sync UAT / Cola B menú (P1).
7. **Config/ABM** — [`cortes.md`](cortes.md) corte 7 · slug `anunciador-agi-config-abm` (P3).
8. Gate perímetro cerrado (P5) con decisión explícita sobre config.

---

## 7. Registro de decisión

| Id | Decisión | Fecha | Estado |
|----|---------|-------|--------|
| D-ANU-01 | Display usa `llamado_paciente`; no mirror 1:1 por defecto | 2026-08-18 | **SUPERSEDED** 2026-08-20 → DDL canónico `ts.llamado_anunciador` ([regla-ddl-postgres-migrado](../../canon/regla-ddl-postgres-migrado.md)) |
| D-ANU-01b | Canónico schema = PG migrado `ts.*`; puente `llamado_paciente` = deuda hasta cutover | 2026-08-20 | vigente |
| D-ANU-02 | Socket.IO no se clona; WS Quarkus canónico | 2026-08-18 | vigente (`anunciador-ws`) |
| D-ANU-03 | A3 ocupación / TTS / logos | 2026-08-18 | **cerrado** en N1–N3 (`apagar-anunciador-node`) |
| D-ANU-04 | `api_seguridad_nodejs` → Identity; no adapter Node en Api | 2026-08-18 | vigente |
| D-ANU-05 | **Apagar Node**; Clarify: N1+N2+N3 obligatorios (todas las salas, paridad legacy) | 2026-08-18 | vigente (dev PASS) |
| D-ANU-06 | Ciclo `LLAMAR` S→N/delete **diferido** (no WAIVE) | 2026-08-18 | **cerrado** núcleo TV 2026-08-26 (`ciclo-vida-llamado-anunciador`); enganche CU → `cu-llamar-atencion-medica` |
| D-ANU-07 | Relevamiento capa 3 (pipeline+maestros) obligatorio; seed ≠ paridad config; deuda ABM = `anunciador-agi-config-abm` | 2026-08-26 | vigente |
