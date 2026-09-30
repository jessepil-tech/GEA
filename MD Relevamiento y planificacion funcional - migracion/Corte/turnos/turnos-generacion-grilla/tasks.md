---
title: Tasks — T4 Turnos generación de grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-04
phase_id: sdd.hospital.turnos-generacion-grilla
---

# Tasks — T4

1. [x] **TSK-ops-g0-1** Abrir SDD + links relevamiento/cortes/T3 — **2026-09-01**
2. [x] **TSK-ops-g0-2** Clarify **FIRME** en [spec.md](spec.md) (filas 1–10) — **2026-09-01**
3. [x] **TSK-ops-g0-3** Borrador inventario copy (`msg.*` → labels) — [`inventario-copy-msg.md`](inventario-copy-msg.md) **2026-09-01**
4. [x] **TSK-ops-g0-4** Borrador inventario validaciones BB/MessageBundle → UI+API — [`inventario-validaciones.md`](inventario-validaciones.md) **2026-09-01**
5. [x] **TSK-app-g1-1** Inventariar gaps DDL — [`inventario-ddl-gaps.md`](inventario-ddl-gaps.md) **2026-09-01**
5b. [x] **TSK-app-g1-2** Flyway V43 tablas aux + FK grp pers/serv · V44 seed feriado — **2026-09-01**
6. [x] **TSK-app-g2-1** Golden fixtures + `TurnosGeneracionPort` generar (serv+pers) — **2026-09-02**
7. [x] **TSK-app-g2-2** API POST generar + IT — **2026-09-02**
8. [x] **TSK-app-g3-1** Port eliminar + API + IT — **2026-09-02**
9. [x] **TSK-app-g4-1** Query consulta agendas + API + IT — **2026-09-02**
10. [x] **TSK-web-g5-1** UI generar + eliminar + consulta (TURNOS / agenda) — **2026-09-02**: 3 páginas
    Angular (`grilla-turnos-generar` / `-eliminar` / `-consulta`) bajo
    `configuracion/pages`, wiring page → use-case (`TurnosGrillaUseCase`) →
    `ITurnosRepository`/`TurnosRepositoryImpl` (HttpClient solo en impl). API
    mínima agregada: GET `/api/v1/turnos/grilla/candidatos-eliminar` (lista
    previa a eliminar, no existía). Equipo diferido D-TUR-17 (radio
    deshabilitado). Imprimir consulta: hijo [`turnos-consulta-agendas-imprimir`](../turnos-consulta-agendas-imprimir/) **done** 2026-09-04.
10b. [x] **TSK-web-g5-2** Paridad disposición UI **generar** vs `generacionGrillaTurnos.xhtml`
    — **2026-09-02**: north cascada centro→servicio (serv) / buscador profesional
    (pers) + grupo + fechas/horas pares + días/feriado; center tabla observaciones;
    south Generar|Volver. Sin buscador HAB en modo servicio. Excepción: equipo
    diferido D-TUR-17.
10c. [x] **TSK-web-g5-3** Paridad disposición UI **eliminar** + **consulta** vs xhtml
    — **2026-09-02**: mismo north 200|1fr|200|1fr / InputWid100 que generar;
    eliminar: cascada + esp. rango horario + días + Consultar; consulta: cascada +
    grupo|año + Consultar; sin HAB apilado en modo servicio. Excepciones: equipo
    D-TUR-17; Imprimir → hijo [`turnos-consulta-agendas-imprimir`](../turnos-consulta-agendas-imprimir/). Drill-down calendario cobrado en hijo
    [`turnos-consulta-agendas-drilldown`](../turnos-consulta-agendas-drilldown/) (2026-09-02).
10d. [x] **TSK-web-g5-4** (hijo) Drill-down consulta agendas — [`turnos-consulta-agendas-drilldown`](../turnos-consulta-agendas-drilldown/) **2026-09-02**
10e. [x] **TSK-app-web-imprimir-1** Imprimir consulta agendas (BIRT) — [`turnos-consulta-agendas-imprimir`](../turnos-consulta-agendas-imprimir/) Api+Web **2026-09-04**
11. [x] **TSK-ops-g6-1** Smoke + [verify-report.md](verify-report.md) — **PASS** 2026-09-04
    (Identity:8080 · Api:8081 · Web:4200 · Reports:8082 · consulta → Imprimir PDF piloto).
12. [x] **TSK-web-e2e-gen** Playwright viaje generar/eliminar grilla (PG fixture
    nombrado) — **2026-09-04** · `Hospital-Web/e2e/grilla-turnos-generar-eliminar.spec.ts`
13. [x] **TSK-ops-g6-2** Actualizar matriz/pipeline/backlog/mapa-menu — **2026-09-04**
