# Inventario DDL — Oracle `TS` vs PostgreSQL `ts`

Fecha corrida: **2026-08-20**  
Oráculo: `127.0.0.1:1521` SID `HOSPROD` (VPN Hospital-Infra)  
Destino migrado: `10.0.0.35:10520` / `grupogea-hospital_dev` / schema `ts`

## Veredicto

| Criterio | Resultado |
|----------|-----------|
| Tablas (`BASE TABLE` / `ALL_TABLES`) | **2279 = 2279** (mismo set de nombres) |
| Columnas totales | **35393 = 35393** |
| Nombres de columnas / cantidad por tabla | **Paridad total** (0 mismatches) |
| Orden físico de columnas | **181** tablas con orden distinto |
| Nullability | **3** columnas difieren (Oracle `N` → PG `Y`) |
| Tipos | Solo transformaciones Oracle→PG (catálogo abajo) |

La BD migrada **cumple** la regla de producto: mismos nombres de tablas/columnas y misma cantidad; solo cambian tipos (y en 181 tablas el orden).

## Catálogo de tipos (conteo)

| Oracle | PostgreSQL | Columnas |
|--------|------------|----------|
| `NUMBER` | `NUMERIC` | 14585 |
| `VARCHAR2` | `CHARACTER VARYING` | 12807 |
| `DATE` | `TIMESTAMP WITHOUT TIME ZONE` | 4823 |
| `CHAR` | `CHARACTER` | 2801 |
| `NUMBER` | `BIGINT` | 150 |
| `BLOB` | `TEXT` | 78 |
| `CLOB` | `TEXT` | 74 |
| `NUMBER` | `SMALLINT` | 62 |
| `NUMBER` | `INTEGER` | 6 |
| `TIMESTAMP(6)` | `TIMESTAMP WITHOUT TIME ZONE` | 5 |
| `ROWID` | `CHARACTER VARYING` | 2 |

**Notas de compatibilidad a documentar formalmente:**

1. `DATE` → `TIMESTAMP` (Oracle `DATE` incluye hora).
2. `BLOB`/`CLOB` → `TEXT` (p.ej. `ANUNCIADOR.LOGO`, `IMG_FONDO_ANUNCIADOR`) — no es `BYTEA`; asumir encoding texto/base64 o revisar consumidores binarios.
3. Subconjunto de `NUMBER` afinado a `BIGINT`/`INTEGER`/`SMALLINT` (no todo queda `NUMERIC`).
4. Tres nullability relajadas: ver `diff_nullability.csv`.

## Anunciador (foco piloto)

`LLAMADO_ANUNCIADOR`, `ANUNCIADOR`, `ANUNCIADOR_AMBIENTE_AMB`, `DICCIONARIO_ANUNCIADOR`: **mismos nombres, misma cantidad, mismo orden**; solo tipos según tabla.

Esto **obsoleta** el read-model Flyway `public.llamado_paciente` de Hospital-Api como schema canónico.

## Artefactos

| Archivo | Contenido |
|---------|-----------|
| `oracle_ts_columns.csv` | Catálogo Oracle (tablas) |
| `pg_ts_columns.csv` | Catálogo PG |
| `diff_type_pairs.csv` | Pares de tipos |
| `diff_nullability.csv` | Nullability distinta |
| `diff_only_*.txt` | Tablas solo en un lado (vacío en esta corrida) |
| `diff_col_count.csv` / `diff_col_names.csv` | Vacíos = paridad |

## Documentación de programa (2026-08-20)

| Doc | Rol |
|-----|-----|
| [`../regla-ddl-postgres-migrado.md`](../../canon/regla-ddl-postgres-migrado.md) | Regla prioritaria canónica |
| [`../catalogo-tipos-oracle-pg.md`](../catalogo-tipos-oracle-pg.md) | Catálogo de tipos |
| [`../pendientes-solo-oracle.md`](../../estado/pendientes-solo-oracle.md) | Gaps no-tabla (views, packages, triggers, …) |
| [`../plan-cutover-api-schema-ts.md`](../../planificacion/plan-cutover-api-schema-ts.md) | Cutover Api (plan; sin código aún) |

Credenciales **no** van en este directorio.
