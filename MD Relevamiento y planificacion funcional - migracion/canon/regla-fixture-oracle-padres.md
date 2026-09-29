---
title: Fixture padres — Oracle antes que mock
status: canonical
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.regla-fixture-oracle-padres
---

# Fixture padres — Oracle antes que mock

Copia Oracle = **bootstrap de padres**, no prueba de ABM. El CU sigue
naciendo en PG.

## Orden (paso 4 del loop)

1. Filas ya en el dump PostgreSQL `ts`.
2. Copia **solo lectura** desde Oracle HIS (credenciales en env, no en git).
3. Seed mock en `Hospital-Api/scripts/sql/seeds/padres/<tabla>/` **solo** si
   no hay conexión (declarar `origen=mock(sin-oracle)`).

No seedear la tabla que este corte ABMea. No mutar Oracle.
