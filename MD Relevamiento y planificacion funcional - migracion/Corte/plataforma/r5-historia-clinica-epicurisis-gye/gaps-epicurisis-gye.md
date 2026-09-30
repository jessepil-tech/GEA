# Gaps dialect / packages — EpicrisisGYE (F3)

Inventario post-conversión mecánica (2026-08-26). Diseño en `Hospital-Reports/designs/EpicrisisGYE.rptdesign`.

## Dialect (convertido)

| Constructo | Antes | Después | Notas |
|------------|------:|---------|-------|
| `ROWNUM` (columna / wrap) | 17 en queryText | `ROW_NUMBER() OVER () as rownum` | Metadata BIRT `columnName=ROWNUM` se mantiene vía alias |
| `ROWNUM = 1` | 3 | `LIMIT 1` | Subqueries diagnostico_pri / procedimiento / firma |
| `NVL` | 7 | `COALESCE` | |
| `DECODE(x, null, …)` | 5 | `CASE WHEN x IS NULL …` | |
| `SYSDATE` + resta días | 4 | `CURRENT_TIMESTAMP` + `CEIL(EXTRACT(EPOCH…)/86400)` | Duración internación / cirugía |
| `ts.personas.f_get_persona_full` | 4 | `personas.f_get_persona_full` | V11 |
| `ts.general.f_get_edad_anio` | 1 | `general.f_get_edad_anio` | V11 |

Herramienta: `Hospital-Reports/tools/convert-oracle-dialect-rptdesign.py`.

## Packages pendientes (blockers de paridad)

| Dependencia | × en diseño | Estado PG | Datasets |
|-------------|------------:|-----------|----------|
| `ts.historia_clinica.p_get_valor_det_form_col` | 21 | **No existe** | ANTECEDENTES PAC/SERV, ANAMNESIS, EXAMEN_FISICO, EVALUACION_ENF |
| `ts.personas.f_get_matricula_full` | 2 | **No existe** | MARICULA_* |
| `ts.personas.f_get_especialidad_full` | 2 | **No existe** | MARICULA_* |
| `ts.personas.f_get_personal_matricula` | 2 | **No existe** | EVOLUCIONES |
| Tablas dominio `ts.*` (internacion, …) | — | **Fuera de V11** | Casi todos — hace falta ETL/BD clínica, no solo dialecto |

**Sin TMP / REF CURSOR** en este diseño (a diferencia de HistoriaClinica).

## Criterio R5

- Dialect Oracle→PG en queryText: **hecho**.
- Driver ODA: **hecho** (`org.postgresql.Driver` + binding `DB_JDBC`) — 2026-08-26.
- PDF BIRT E2E Docker (`hospital-reports-local`): **PASS smoke 2026-08-26** — `public.*` + seed + bindings **minúsculas** ([`regla-birt-columnas-minusculas.md`](../../../canon/regla-birt-columnas-minusculas.md)). Cabecera con datos (Pérez/Juan, motivo, fechas, edad). Worker tolerante a warnings de secciones vacías. **No es canónico `ts`**.
