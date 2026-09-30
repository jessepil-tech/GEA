---
title: Inventario DDL gaps — prep / req infoTurno
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-prest.ddl
---

# Inventario DDL gaps

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md) · [`catalogo-tipos-oracle-pg.md`](../../../relevamiento/catalogo-tipos-oracle-pg.md).  
Fuente: `PreparacionPrest.hbm.xml` · `ReqRealizaPrest.hbm.xml` · `ReqRealizacion.hbm.xml`.

| Objeto | ¿En Flyway? | ¿Bloquea v1? | Acción |
|--------|-------------|--------------|--------|
| `ts.req_realizacion` (`id_req_realizacion`, `req_realizacion` varchar 45, audit) | **Sí V52** | No | CREATE |
| `ts.preparacion_prest` (id, cod/id prestación, `edad_desde`/`edad_hasta`, `preparacion` TEXT, audit) | **Sí V52** | No | CREATE; BLOB→TEXT |
| `ts.req_realiza_prest` (PK id_req + cod + id_prest; `observaciones` varchar 250, audit) | **Sí V52** | No | CREATE |
| Seed CONS/`80001` | V53 | No | HTML + fila req |
| `ts.req_realiza_prest_equipo` | **No** | No Camino 1 | **diferido** D-TUR-17 |
| `ts.prestacion` CONS/`80001` | Sí V42 | No | seed hijo apunta a esa PK |
| `ts.turno` | Sí V31 | No | **no** ALTER |

G1: Flyway **V52** CREATE + **V53** seed. Sin ALTER de `turno`.

Sin tablas `public` ni `*_agi`.
