---
title: Regla — columnas BIRT en minúsculas
status: canonical
owner: grupogea
last_updated: 2026-08-26
phase_id: sdd.hospital.regla-birt-columnas-minusculas
indice_blurb: Bindings BIRT / columnas JDBC en minúsculas (PG)
---
# Regla — columnas BIRT en minúsculas (PostgreSQL)

`phase_id:` **`sdd.hospital.regla-birt-columnas-minusculas`**  
Fecha: **2026-08-26**  
**Estado:** **CANÓNICA** (frente reportes / R5+)

Complementa:

- [`regla-ddl-postgres-migrado.md`](regla-ddl-postgres-migrado.md) — nombres físicos en PG
- [`register-report-sidecar.md`](../arquitectura/register-report-sidecar.md) — alta de un `.rptdesign` en el sidecar

---

## Principio

| Pregunta | Respuesta |
|----------|-----------|
| ¿Cómo llegan los nombres de columna del JDBC/PostgreSQL? | **minúsculas** (identificadores sin comillas) |
| ¿Cómo deben quedar bindings BIRT (`resultSet`, `row["…"]`, `dataSetRow["…"]`)? | **minúsculas**, mismo nombre lógico que en PG |
| ¿Qué hacer con diseños Oracle (MAYÚSCULAS)? | Convertir al migrar al sidecar; no dejar dual |

Oracle fold-upper; PostgreSQL fold-lower. Un `.rptdesign` migrado que conserve `row["PACIENTE"]` contra un result set `paciente` **no pinta datos** (bindings rotos / `row is not defined`).

---

## Regla de trabajo (obligatoria)

1. En todo `.rptdesign` destinado a PG: metadatos de dataset (`name`, `nativeName`, `columnName`, `alias`), ítems de layout (`resultSetColumn`) y expresiones `row["col"]` / `dataSetRow["col"]` en **minúsculas**.
2. SQL del dataset: preferir identificadores sin comillas (PG → minúsculas) o aliases explícitos en minúsculas. Evitar `AS "PACIENTE"` salvo waiver.
3. **No** renombrar parámetros escalares del reporte (`idInternacion`, `DB_USER`, `titulo`, …): son contrato HTTP/sidecar, no columnas JDBC.
4. Herramienta de conversión: `Hospital-Reports/tools/lowercase-birt-column-bindings.py` (solo pliega `UPPER_SNAKE` Oracle; no toca camelCase).
5. Al registrar un reporte nuevo ([`register-report-sidecar.md`](../arquitectura/register-report-sidecar.md)): checklist incluye “column bindings / resultSetColumn en minúsculas”.

---

## Verificación rápida

| Check | OK |
|-------|----|
| `rg 'row\["[A-Z]' designs/<reportId>.rptdesign'` | 0 hits |
| PDF BIRT muestra valores de cabecera (no solo labels vacíos) | Sí |
| Params del POST siguen camelCase / `DB_*` | Sin cambio |

---

## Relación con el puente PoC `public.*`

El PoC puede leer tablas en `public` mientras el canónico de dominio sigue siendo `ts` (regla DDL). **Esta regla de minúsculas aplica igual** en `public` o `ts`: el fold de identificadores es del motor PG, no del schema.
