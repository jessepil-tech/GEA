---
title: Verify — Turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-horarios-equipo.verify
---

# Verify — Turnos por equipo

Clarify **FIRME** 2026-09-23. Código en `dev/tur-horarios-equipo`. Smoke de ealbo el 2026-09-23.

**Gate:** **PASS / gate-done** 2026-09-23.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Cascarón + buscador + datos | **done** | ledger 5 · 8 · 9 |
| Grupo, prestaciones, horario y días | **done** | ledger 4 · 5 · 8 · 9 |
| Reserva plan-conv del día | **done** | lápiz del horario de equipo |
| Horario especial | **diferido** | [`turnos-horarios-especiales`](../turnos-horarios-especiales/) |
| Inhibición | **diferido** | [`turnos-horarios-inhibiciones`](../turnos-horarios-inhibiciones/) |
| Ocupación conv/plan | **diferido** | T3, sin slug de equipo |
| Generar grilla modo equipo | **diferido** | D-TUR-17 |
| Jobs laboratorio | **N/A** | otro dominio |
| Auditoría campo a campo | **diferido(auditoria)** | tablas `aud_*` del dump |
| Volumen | **diferido(perf-volumen)** | medido en vacío |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** |
| Viaje (pasos) | Buscar `EQDEMO1001` → grupo → prestación `CONS` → horario con un día (Domingo 08:00) |
| Spec | `Hospital-Web/e2e/horarios-turnos-equipo.spec.ts` (mock; no es el smoke) |
| Fixture | `EQDEMO1001` · hab 2026-09-23 · prestación `CONS` |
| Legacy e2e | no |

## Paridad UI (xhtml)

Fuente: `turnosEquipo/*` de datos, grupo, prestaciones y horario. Web: `horarios-turnos-equipo` + buscadores de la hab y de prestaciones.

| Control | Estado | Nota |
|---------|--------|------|
| Inventario copy | **done** | [`inventario-copy-msg.md`](inventario-copy-msg.md) |
| Inventario validaciones | **done** | [`inventario-validaciones.md`](inventario-validaciones.md) |
| Inventario geometría | **done** | [`inventario-geometria.md`](inventario-geometria.md). Anchos del xhtml. La hoja reusa el chrome de T3 |
| Inventario interacción | **done** | [`inventario-interaccion-ui.md`](inventario-interaccion-ui.md) |
| Menú lateral sin fusionar | **done** | especial, inhibición y ocupación visibles y disabled |
| Feedback | **done** | toast y confirm modal. El mensaje técnico de ids del convenio no se publica |
| Smoke visual | **done** | ealbo 2026-09-23: «si funciona bien el flujo» |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Verdicto |
|---|------------|-------|-----------|----------|
| 1 | Índice semilla dentro del techo | build | `indice-legacy.sh --semilla turnosEquipo` → 3/8 xhtml, 2/15 beans, 0 firmas, 0 reportes | **verificado** |
| 2 | Jobs equipo fuera de este circuito | build | `--jobs equipo` → 5 jobs laboratorio/ANMAT | **verificado** |
| 3 | Padres del fixture | consulta | `EQDEMO1001` centro 1001 servicio 10. La cadena del CU ya no está vacía: grupo id=2 (fila 4) | **verificado** |
| 4 | Fila de grupo nacida del CU | escritura | id=2 `Equipo Demo` · centro 1001 · servicio 10 · `EQDEMO1001` · `actualizado_por` admin. Horario id=1 vigencia 2026-09-23/2026-09-26. Prestación `CONS` id_prestacion=80001. Día domingo 08:00–12:00 `INICIO`. `SELECT` en `ts.grp_prest_tur_equipo` / `horario_tur_grp_equipo` / `prest_grp_prest_tur_equipo` / `dia_horario_tur_grp_equipo` el 2026-09-23 | **verificado** |
| 5 | Listar la cadena | endpoint | `GET /api/v1/turnos/horarios/equipo/grupos?idCentroAte=1001&idServicio=10&codEquipo=EQDEMO1001` **200** (id=2). Prestaciones del grupo 2 **200** (`CONS`). Horarios del grupo 2 **200** (id=1). Días del horario 1 **200** (domingo 08:00). Sin Authorization → **401**. localhost:8081 2026-09-23 | **verificado** |
| 6 | Actor sin la entrada de menú | acceso | perfil TURNOS `ATENCION_TURNOS`, hoja 15416; rol funcional ninguno. `npx tsx --tsconfig tsconfig.app.json e2e/acceso-horarios-equipo.check.ts` → `ok acceso horarios-turnos-equipo` exit 0. `RECEPCION` + `showAll=false` oculta TURNOS y `nav-horarios-turnos-equipo`. Con `ATENCION_TURNOS` la ruta está. `ng test` del repo sigue bloqueado por jasmine ajeno | **verificado** |
| 7 | p95 y dos vigencias iguales | no funcional | Listar grupos n=20 · p95=143,4 ms · min 92,5 · max 636,6. Una sola fila: medido en vacío; volumen sigue **diferido(perf-volumen)**. POST de la vigencia 2026-09-23/2026-09-26 → **400** «Existen fechas solapadas.» sin fila nueva. Dos POST paralelos 2099-06-01/2099-06-03 → **201** id=3 y **400** solapadas. DELETE id=3 → **204**. `SELECT` posterior: solo id=1, fin 2026-09-26 | **verificado** |
| 8 | Viaje Buscar → día | e2e | `npx playwright test e2e/horarios-turnos-equipo.spec.ts` → 1 passed (3,4 s). Mock, no es el smoke | **verificado** |
| 9 | Smoke del operador | smoke | ealbo, 2026-09-23: «si funciona bien el flujo» | **verificado** |

## Resultado

**PASS / gate-done** 2026-09-23. Diferidos: horario especial, inhibición, ocupación conv/plan, generar grilla (D-TUR-17), `diferido(auditoria)`, `diferido(perf-volumen)`.
