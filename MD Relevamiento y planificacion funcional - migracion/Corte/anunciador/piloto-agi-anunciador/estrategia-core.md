# Estrategia Core / cross — piloto

Cómo atender funcionalidades compartidas (sectores, ambientes, personal, etc.)
sin inventar microservicios ni bloquear el CU #1.

## Regla F2 (recordatorio)

Hay **una** solución Quarkus de negocio (`Hospital-Api`). Identity es la única
excepción de plataforma. Lo “Core” **no** es otro deploy: son **features/módulos
compartidos** dentro del mismo `application` + tablas en el mismo Postgres.

```text
Hospital-Api
├── features/catalogo     ← sectores, ambientes, servicios… (ABM futuro)
├── features/anunciador   ← consume catálogo (lectura)
├── features/agi          ← tótem (después)
└── infrastructure        ← persistencia compartida
```

## En el piloto (ahora)

| Necesidad | Tratamiento |
|-----------|-------------|
| Sector / ambiente / anunciador | Tablas + **seed** en Flyway; **solo lectura** vía API anunciador |
| ABM completo de catálogo | **Diferido** a feature `catalogo` (misma API, mismos IDs) |
| Datos vivos Oracle 11.2 | Después: sync/ETL o golden master; no ORM a 11.2 |
| Llamados | Tabla read-model `llamado_paciente` (en legacy sale de PL/SQL) |

## Anti-patrones a evitar

- Microservicio “Core” solo para sectores/ambientes
- Duplicar catálogo en Identity
- Bloquear CU #1 hasta tener ABM UI de configuración

## Evolución

1. CU #1 lee seed (este corte).
2. Feature `catalogo` agrega POST/PUT de sector/ambiente sin cambiar contratos de lectura del anunciador.
3. Cuando migre configuración real, se reemplaza seed por datos sync; los IDs de anunciador se estabilizan con mapa legacy↔UUID si hace falta.
