---
title: Regla — DDL canónico = PostgreSQL migrado
status: canonical
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.regla-ddl-postgres-migrado
indice_blurb: DDL = schema ts; sin public ni *_agi de dominio
---
# Regla prioritaria — DDL canónico = PostgreSQL migrado

`phase_id:` **`sdd.hospital.regla-ddl-postgres-migrado`**  
Fecha: **2026-08-20**  
**Estado:** **CANÓNICA** (prioridad de programa; aplica a todo el proceso de migración)

Complementa (no reemplaza) la paridad **funcional**:

- [`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md) — mismo comportamiento de negocio
- Esta regla — **mismo contrato físico de datos** hacia el destino

Evidencia de paridad tablas/columnas (corrida 2026-08-20):

- [`inventario-ddl-oracle-pg/README.md`](../relevamiento/inventario-ddl-oracle-pg/README.md)

---

## Principio

| Pregunta | Respuesta por defecto |
|----------|------------------------|
| ¿Sobre qué schema trabaja el stack nuevo (Api, Reports, ETL, seeds)? | **PostgreSQL migrado**, schema **`ts`** |
| ¿Podemos inventar tablas/columnas “simplificadas” en `public` para un slice? | **No** como canónico. Solo puente temporal documentado + fecha de retiro |
| ¿Podemos renombrar tablas/columnas respecto de Oracle? | **No** |
| ¿Podemos agregar/quitar columnas respecto de Oracle? | **No** |
| ¿Qué sí puede cambiar? | **Tipos de datos** por compatibilidad PG, siempre en el [catálogo](../relevamiento/catalogo-tipos-oracle-pg.md) |

**Fuente de verdad DDL destino:** la base PostgreSQL que ya contiene la estructura migrada
(hoy: host de desarrollo `grupogea-hospital_dev`, schema `ts`).  
**Oracle 11.2** sigue siendo oráculo / legacy en producción-copia hasta el cutover, no el
modelo a clonar otra vez en Flyway “a mano”.

---

## Regla de trabajo (obligatoria)

1. **Diseño e implementación** de CUs, JDBC, BIRT y migraciones de app usan nombres
   `ts.<tabla>` / columnas legacy (case-folding a minúsculas en PG es aceptable si el
   nombre lógico es el mismo). En **packages PG** (`Hospital-Reports/sql/packages-pg`)
   el SQL del callable califica tablas `ts.<tabla>`; el schema del package
   (`personas`, `historia_clinica`, …) es solo del *callable*, no de las tablas.
   Prohibido `FROM persona` o `search_path` como sustituto.
2. **Prohibido** promover a canónico un read-model paralelo (`llamado_paciente`, UUIDs
   donde Oracle tenía `NUMBER`, etc.) salvo waiver de **negocio** con plan de retiro.
3. Si un objeto hace falta y **aún no está en PostgreSQL** pero **sí en Oracle** →
   **no se inventa un sustituto silencioso**. Se registra en
   [`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md) y se aborda como trabajo
   explícito (migrar DDL/objeto o decidir equivalente documentado).
4. Flyway / seeds del Api **no** deben diverger del shape `ts` migrado. El catálogo
   HIS no se recrea en Flyway: ya está en el dump (2279 tablas). Ver § Flyway abajo.

---

## Flyway sobre el dump compartido

La PG de trabajo es **una**, resultado de migrar Oracle→Postgres, **solo estructura**,
sin datos. Ese schema HIS es el estado de tablas/columnas. No crece al abrir un stream.

El Api arranca con `quarkus.flyway.migrate-at-start=true` contra esa misma URL. Las
`V27`–`V32` que hacían `CREATE TABLE ts.*` por grupo funcional eran para **otro**
ambiente (`hospital_api` / `-test`) que no tenía el dump; no son el modelo a repetir
sobre la base compartida (el objeto ya existe → el `CREATE` falla).

| Va en un `Vnn` nuevo | No va en Flyway |
|----------------------|-----------------|
| Identity y tablas de app que **no** están en Oracle HIS | `CREATE TABLE` de una tabla que ya está en `ts` |
| Delta real vs dump: tipo (p. ej. BYTEA), vista, función, package PG, trigger puntual | Seed de negocio (pacientes, turnos, convenios de trabajo) |
| Objeto que el inventario marcó faltante (`pendientes-solo-oracle`) y este corte porta | «Simplificar» o agregar columnas HIS que Oracle no tiene |
| Retiro documentado de `public.*` piloto | Recrear las 2279 «por si el host se restaura» — el restore es el dump, no Flyway |

**Datos:** el dump está vacío. Lo llena el CU que escribe (`criterio-avance-e2e-datos`)
o un bootstrap **#2** de padres. Fixture de prueba (INSERT) =
[`Hospital-Api/scripts/sql/seeds/`](../../../Hospital-Api/scripts/sql/seeds/)
(`padres/<tabla>/` · `sec-id/` · `oraculo/`) —
**no** un `Vnn` y **no** `db/dev-seed/` para seeds nuevos. Quarkus no los aplica al arranque.

**Lock del corte** ([`loop-migracion-corte.md`](loop-migracion-corte.md) paso 2): si hay
delta DDL vs dump, reservar rango `Vnn` (un escritor). Si el corte solo usa tablas
ya presentes → `Rango Flyway: n/a`. Identity no comparte este historial: otra base.

### Instalador Flyway del HIS — al cierre del programa, no ahora

Recrear preprod/prod **hoy** = dump de estructura + Flyway de Identity + deltas.
Transcribir las 2279 a `Vnn` **no** es trabajo de cada corte. Cuando el schema
destino esté cerrado (HIS + Identity + packages PG), un corte de cierre puede
generar el juego de migraciones instalador. Hasta entonces, prohibido “ir
armando el instalador” copiando tablas del dump al Api.

### Preprod / prod — base limpia (no reejecutar V1–V58)

La PG actual es de **prueba**. El historial Flyway de hoy **no** es el instalador de
un entorno nuevo: está incompleto (solo los grupos `V27`–`V32`, no las 2279) y
contaminado (`V*seed*` de DNI demo, hab, turnos). Reproducir preprod/prod con
`migrate-at-start` sobre vacío daría un HIS a medias **y** datos de laboratorio.

Instalación limpia, en este orden:

1. **Schema HIS** = el artefacto Oracle→PG (`pg_dump --schema-only` del dump
   canónico, o el mismo pipeline que lo generó). Eso recrea las 2279 tablas vacías.
2. **Flyway fino** = Identity + deltas que el dump no trae (tipos, vistas, packages
   PG). **Sin** seeds.
3. **Datos** = carga de negocio (cutover / bootstrap de padres reales), no `V47`.

Sobre la PG de prueba **no** se borra ni se condensa V1–V58 (rompe checksums del
equipo). El día del primer entorno limpio se hace el corte de instalador: dump
versionado + classpath Flyway **prod** (sin `*seed*`, sin `CREATE TABLE ts.*`
redundante). En esa base nueva, `baseline` en Flyway (el dump ya aplicó el
catálogo HIS) y a partir de ahí solo `Vnn` posteriores, prod-safe.

No se espera a producción para separar seeds: cada `V*seed*` nuevo en
`db/migration` es una bomba para el paso 2. Los nuevos van a
`Hospital-Api/scripts/sql/seeds/` (`padres/<tabla>/` · `sec-id/` · `oraculo/`).

---

## Pendientes solo-Oracle (proceso)

Cuando alguien detecte un gap:

| Campo | Contenido |
|-------|-----------|
| Objeto Oracle | `TS.<NOMBRE>` + tipo (`TABLE`, `VIEW`, `PACKAGE`, `SEQUENCE`, `TRIGGER`, …) |
| ¿Existe en PG `ts`? | No / parcial |
| Impacto | Qué CU / reporte / adapter lo necesita |
| Acción | Migrar estructura · portar lógica a Quarkus · diferir con fecha |
| Evidencia | Query diccionario / ruta SDD |

Plantilla viva: [`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md).

---

## Relación con D-ANU-01

La decisión 2026-08-18 de **diferir** el mirror 1:1 de `LLAMADO_ANUNCIADOR` a favor de
`llamado_paciente` queda **SUPERSEDED** para schema por esta regla y por el inventario
2026-08-20 (la tabla ya existe en PG migrado).

Ver actualización en
[`relevamiento-node-anunciador/decisiones.md`](../relevamiento/relevamiento-node-anunciador/decisiones.md).

## Relación con otras reglas

- Identificadores SQL sin comillas en PG → **minúsculas**; bindings BIRT deben coincidir: [`regla-birt-columnas-minusculas.md`](regla-birt-columnas-minusculas.md).
- Reportes BIRT: [`regla-migracion-reportes-birt.md`](regla-migracion-reportes-birt.md) (datasets `ts.<tabla>`; packages-pg).
- Waivers de comportamiento: [`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md).

---

## Plan de alineación del Api (sin código aún)

[`plan-cutover-api-schema-ts.md`](../planificacion/plan-cutover-api-schema-ts.md) ·
retiro de puentes: [`retiro-tablas-piloto-public.md`](../arquitectura/retiro-tablas-piloto-public.md)

---

## Dónde aplica

Todo el programa Hospital: Api, Web (contratos), Reports/BIRT, Identity (si toca datos
de dominio), Migration docs, seeds y golden master.
