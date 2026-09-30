---
title: Verify — T3 Turnos grupos + horarios
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-01
phase_id: sdd.hospital.turnos-horarios-grupos
---

# Verify — T3

**Gate:** **PASS / gate-done** — smoke host 2026-09-01 (Identity:8080 · Api:8081 · Web:4200).  
Clarify **FIRME** 2026-08-28. UI revisada por ops (buscador multi + dark).  
**Paridad UI:** [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) v1.6+ (canon buscadores + dark).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| ABM grupo + prest + horario + días personal | **done** | API + UI `/configuracion/horarios-turnos-pers` |
| ABM grupo + prest + horario + días servicio | **done** | API + UI `/configuracion/horarios-turnos-serv` |
| Buscador prestaciones multi-select (alta) | **done** | `GET …/buscadores/prestaciones` + dialog Web + batch |
| Equipo cadena | diferido(ABM) / DDL según C2 | D-TUR-13 |
| Inhibiciones | diferido | [`turnos-horarios-inhibiciones/`](../turnos-horarios-inhibiciones/) |
| Horario especial | diferido | [`turnos-horarios-especiales/`](../turnos-horarios-especiales/) |
| Ocupación conv/plan | diferido | hijo / T5 |
| Grupo múltiple | diferido | — |
| Vista atencionTurno horarioGrp* | diferido | — |
| Generación grilla | diferido T4 | [`turnos-generacion-grilla/`](../turnos-generacion-grilla/) |
| Agenda | diferido T5 | — |

## Paridad UI (xhtml)

Fuente: `turnosPersonal/*` · `turnosServicios/*` · `buscadorPrestacion.xhtml` · `Resources.properties`.  
Web: `horarios-turnos-pers` / `horarios-turnos-serv` + `prestacion-buscador-dialog` + labels.

| Control | Estado | Nota |
|---------|--------|------|
| Inventario msg.* ↔ labels.ts | **done** | [`inventario-copy-msg.md`](inventario-copy-msg.md) |
| Inventario validaciones BB/MessageBundle | **done** | [`inventario-validaciones.md`](inventario-validaciones.md) UI+API |
| Labels msg.* (sin inventar/acortar) | **done** | |
| Iconos vs texto | **done** | |
| Botones compactos (no full-width) | **done** | |
| Paginator + sort | **done** | grillas + buscador (16) + lista seleccionados (10) |
| Enable/disable | **done** | |
| Layout filtros (Buscar al final) | **done** | |
| Cadena padre+buscador (ambos xhtml) | **done** | input+Buscar → multi → batch; no alta cod/id |
| Multi-select + batch si legacy | **done** | |
| Chrome dialog (size/rows/sort/filtro) | **done** | ~1200px / tabla ~465px / sort API |
| Dark mode (light + dark legibles) | **done** | ops smoke visual 2026-09-01 |
| Menú west sin fusionar en tabs | **done** (serv) · **done** (pers — hijo [`turnos-horarios-pers-shell/`](../turnos-horarios-pers-shell/) 2026-09-01) | pers tenía tabs post-gate; corregido |

## Smoke host (2026-09-01)

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Stack UP | Identity/Api/Web 200 + health UP |
| 2 | Login `admin` / `Admin123!` | 200 + Bearer |
| 3 | `GET …/horarios/serv/grupos` sin Bearer | **401** |
| 4 | `GET …/horarios/serv/grupos?idCentroAte=1001&idServicio=10` | ≥1 (seed `91101` + DEV) |
| 5 | `GET …/horarios/pers/grupos?…&idPersonal=90001` | ≥1; seed `91001` |
| 6 | `GET …/pers/grupos/91001/prestaciones` | ≥1 |
| 7 | `GET …/pers/grupos/91001/horarios` + `…/horarios/92001/dias` | 1 horario; ≥1 día |
| 8 | `GET …/buscadores/prestaciones?q=CONS` | `total≥1` |
| 9 | `POST …/serv/grupos` + `DELETE` cleanup | 201 / 204 |
| 10 | `GET …/turnos/inicio` (regresión T1) | `loginName=admin`; callCenters≥1 |
| 11 | Web shells | `/configuracion/horarios-turnos-serv` · `…-pers` · `/turnos/inicio` → 200 |

## Criterios

| CA | Resultado |
|----|-----------|
| Flyway grupos/horarios + seed | **PASS** (V41/V42; seed `91001`/`91101`) |
| API list/create/401 + buscador | **PASS** smoke host |
| UI CONFIGURACION + paridad chrome + buscador | **PASS** código + ops UI + dark |
| Sin silencios inventario | Tabla capacidades completa |
| Regresión T1 | **PASS** |

## Resultado

**PASS / gate-done T3** con diferidos explícitos: D-TUR-13, `turnos-horarios-inhibiciones`, `turnos-horarios-especiales`, T4 `turnos-generacion-grilla`, T5 agenda/ocupación.

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí (horarios pers/serv) |
| Decisión | **cerrado-pre-regla** |
| Viaje (pasos) | smoke host 2026-09-01 (Identity:8080 · Api:8081 · Web:4200) |
| Fixture | seed grupos `91001`/`91101` |
| Legacy e2e | no |

No se reabre e2e. Hijos inhibiciones/especiales deciden al cobrarse. Si se toca de nuevo la UI T3 → re-decidir.
