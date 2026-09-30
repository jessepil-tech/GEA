# SDD — Helpers BIRT (PERSONAS + edades) en Postgres

`phase_id:` **`sdd.hospital.birt-helpers-pg`**

Fecha: 2026-08-14  
Estado: **implementación inicial** (V11 + Java `PersonaFullFormatter`)

## Objetivo

Portar al destino las funciones que más usan los `.rptdesign`, sin tocar reportes sanos:

| Función | Destino |
|---------|---------|
| `personas.f_get_persona_full(id)` | SQL + tabla seed `personas.persona` |
| `general.f_get_edad_anio(date)` | SQL (`CURRENT_DATE`) |
| `general.f_get_edad_string(date)` | SQL (rarezas Oracle) |
| Interleaved / AFIP | Ya en Java; SQL EAN-13 no (catálogo vacío) |

## Artefactos

- Flyway: `Hospital-Api/.../V11__birt_general_personas_helpers.sql`
- Java: `PersonaFullFormatter` (+ test)
- Contexto piloto: [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md)

## Pendiente

- Cablear BIRT del entorno nuevo al schema `general`/`personas`
- Ampliar seed `persona` / ETL desde Oracle en corte
- `f_get_edad_full_string` / `ano_mes_dia` en SQL (lógica ya en Java)
- Interleaved como función PG solo si BIRT lo exige en SQL
