# Catálogo de tipos Oracle → PostgreSQL (canónico)

`phase_id:` **`sdd.hospital.catalogo-tipos-oracle-pg`**  
Fecha: **2026-08-20**  
Estado: **CANÓNICO** (única modificación de DDL permitida sin waiver)

Regla madre: [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md)  
Inventario medido: [`inventario-ddl-oracle-pg/`](inventario-ddl-oracle-pg/) (`diff_type_pairs.csv`)

---

## Principio

- **Nombres** de tablas/columnas y **cantidad** de columnas = Oracle.
- **Tipos** pueden diferir solo según este catálogo (o ampliación documentada aquí).
- Toda excepción nueva (tipo no listado, nullability distinta, `BLOB`→otro) se agrega
  a este archivo con **qué / por qué** antes de usarla en app.

---

## Mapeo observado en `grupogea-hospital_dev` (2026-08-20)

| Oracle | PostgreSQL | Columnas | Motivo / notas |
|--------|------------|----------|----------------|
| `NUMBER` (genérico) | `NUMERIC` | 14585 | Preserva escala/precisión variable de Oracle |
| `NUMBER` | `BIGINT` | 150 | IDs / enteros grandes afinados por herramienta de migración |
| `NUMBER` | `INTEGER` | 6 | Enteros acotados |
| `NUMBER` | `SMALLINT` | 62 | Enteros pequeños / flags numéricos |
| `VARCHAR2(n)` | `CHARACTER VARYING(n)` | 12807 | Longitud de carácter alineada en corrida |
| `CHAR(n)` | `CHARACTER(n)` | 2801 | Incluye flags `S`/`N` |
| `DATE` | `TIMESTAMP WITHOUT TIME ZONE` | 4823 | Oracle `DATE` incluye hora; no usar `date` PG |
| `TIMESTAMP(6)` | `TIMESTAMP WITHOUT TIME ZONE` | 5 | Equivalente práctico |
| `BLOB` | `TEXT` | 78 | **Atención:** no es binario (`BYTEA`). Revisar encoding (p.ej. base64) en consumidores (`ANUNCIADOR.LOGO`, `IMG_FONDO_ANUNCIADOR`) |
| `CLOB` | `TEXT` | 74 | Texto largo |
| `ROWID` | `CHARACTER VARYING` | 2 | Pseudo-columna / legado; uso excepcional |

---

## Excepción por objeto: `ANUNCIADOR.LOGO` / `IMG_FONDO_ANUNCIADOR` → `BYTEA`

**Fecha:** 2026-08-21 · **Regla:** excepción documentada según sección "Cómo ampliar".

| Columna | Oracle | PG inicial (ETL) | PG corregido | Motivo |
|---------|--------|------------------|--------------|--------|
| `ANUNCIADOR.LOGO` | `BLOB` | `TEXT` | **`BYTEA`** | Asset de imagen binaria (logo); el `TEXT` era defecto del ETL |
| `ANUNCIADOR.IMG_FONDO_ANUNCIADOR` | `BLOB` | `TEXT` | **`BYTEA`** | Fondo de sala (imagen); idem |

Se aplica en la migración **`V29__ts_anunciador_assets_bytea.sql`** del Api.

> **Precaución:** el resto de los 78 `BLOB` del esquema siguen en `TEXT` (mapa de la tabla de arriba). No aplicar `BYTEA` en masa: evaluar por objeto según si es binario real (`LOGO`, documentos) o contenido codificado (base64). P-ORA-006 (`pendientes-solo-oracle.md`).

---

## Nullability (excepciones medidas)

Solo **3** columnas con Oracle `NOT NULL` y PG nullable — ver
`inventario-ddl-oracle-pg/diff_nullability.csv`: 

| Tabla | Columna | Oracle | PG |
|-------|---------|--------|-----|
| `DOC_RECETA_PAC_OLD` | `DOC_RECETA` | N | Y |
| `DOC_REQ_ADMISION` | `DOC_ADMISION` | N | Y |
| `EPICRISIS_RESUMEN_INT_PAC` | `EPICRISIS_RESUMEN_INT` | N | Y |

Tratarlas como deuda de fidelidad DDL (corregir en PG o documentar waiver por objeto).

---

## Orden de columnas

**181** tablas tienen el mismo set de columnas en distinto orden físico.  
La regla de producto exige nombres y cantidad, **no** el mismo `COLUMN_ID`.  
Apps deben proyectar por nombre de columna, no por posición ordinal.

---

## Cómo ampliar este catálogo

1. Detectar par Oracle→PG no listado (nueva migración o objeto puntual).
2. Agregar fila con conteo o referencia de objeto + justificación.
3. Actualizar fecha del encabezado.
4. Si el cambio rompe consumidores (p.ej. `BYTEA` vs `TEXT`), abrir ítem en
   [`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md) o SDD del slice.
