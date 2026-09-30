---
title: Tasks — M1b servicio + vínculo
status: gate-done
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1b-servicio.tasks
---

# Tasks — M1b

## Loop 1–4

- [x] Elegir (encadena a M1a)
- [x] Reservar (tablas `servicio` · `servicio_centro`; Flyway n/a; no BODY)
- [x] Universo firmado (semillas `servicio` + path `servicioCentro/servicioCentro`; especialidad afuera)
- [x] Fixture: centro = M1a id `1002` (dump)
- [x] G0 inventarios
- [x] Playwright `diferido(fixture)`
- [x] Jobs: N/A Lab job; hab `diferido(jobs)`

## Implementación

- [x] UI + API catálogo servicio (listado HAB)
- [x] Ledger `id_servicio=11` M1B-P6-DUMP (dump)
- [x] ABM vínculo datos (API + listado HAB + dialog)
- [x] Ledger par `(id_centro_ate=1002, id_servicio=11)` dump
- [x] **No** `Vnn`
- [x] Playwright e2e — sigue `diferido(fixture)` (no cierra el gate)
- [x] Actor sin tile 10002/10204 (`m1a_sintile` · solo RECEPCIÓN)
- [x] NFR volumen — `diferido(perf-volumen)` medido en vacío (COUNT 3 / 2)
- [x] Paso 7 `verificar-sdd.sh` / gate PASS 2026-09-18
