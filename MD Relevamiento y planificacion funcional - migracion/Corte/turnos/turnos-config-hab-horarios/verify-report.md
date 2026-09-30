---
title: Verify — T2 Turnos habilitación
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-config-hab-horarios
---

# Verify — T2

**Gate:** **PASS / gate-done** — smoke host 2026-08-27 (Identity:8080 · Api:8081 · Web:4200).  
**Paridad UI:** [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md).

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| ABM hab servicio-centro | **done** | API + UI `/configuracion/hab-turnos-serv` |
| ABM hab personal-servicio | **done** | API + UI `/configuracion/hab-turnos-pers` |
| ABM hab equipo-servicio | diferido(ABM; DDL+check in scope) | D-TUR-12 |
| Check vigencia `f_check_hab_turnos` (hab_*) | **done** | `POST /api/v1/turnos/hab/check` (ops) |
| Sync `atiende_turnos` servicio_centro / personal_servicio / equipo | diferido | D-TUR-11 · [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md) |
| Buscadores por nombre (servicio-centro / personal) | **done** (padre) · filtros → hijo | [`turnos-hab-buscadores/`](../turnos-hab-buscadores/) · [`turnos-hab-buscadores-filtros/`](../turnos-hab-buscadores-filtros/) |
| Horarios / grupos | diferido T3 | `turnos-horarios-grupos` |
| Generación grilla | diferido T4 | — |

## Paridad UI (xhtml)

Fuente: `habTurnosServCentro.xhtml` · `habTurnosPersServ.xhtml` · `Resources.properties`.  
Web: `hab-turnos-serv` / `hab-turnos-pers` + `hab-turnos-labels.ts`.

| Control | Estado | Nota |
|---------|--------|------|
| Módulo / menú CONFIGURACION | done | Dos hojas; títulos i18n |
| Título `hab_turnos_servicio` / `hab_turnos_profesional` | done | |
| Labels inputs / columnas / botones (`msg.*`) | done | |
| **Inventario validaciones BB/MessageBundle** | **diferido** | Gate v1.4 post-cierre T2 — [`deuda-validaciones-pre-hab-turnos.md`](../../../estado/deuda-validaciones-pre-hab-turnos.md) |
| Buscar: icono + texto | done | |
| Acciones: icono lápiz/basura + `title` | done | |
| Grilla: columnas + flags checkbox disabled | done | |
| Paginator `rows=12` + sortBy | done | |
| Agregar visible + disabled hasta Buscar | done | |
| Layout filtros: inputs → Buscar al final | done | |
| Botones compactos (no full-width) | done | `primarySolid` + anchos fijos; no `authPrimary` |
| Buscadores por nombre | **gate-done** padre · filtros diferidos | [`turnos-hab-buscadores/`](../turnos-hab-buscadores/) · [`turnos-hab-buscadores-filtros/`](../turnos-hab-buscadores-filtros/) |
| Campos WA en popup | parcial | En DDL/API/UI; xhtml base serv/pers sin WA — aceptado en corte |
| ABM UI equipo | diferido | D-TUR-12 |

## Smoke host (2026-08-27)

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Stack UP | Identity/Api/Web 200 |
| 2 | Login `admin` / `Admin123!` | 200 + Bearer |
| 3 | `GET …/hab/serv` sin Bearer | **401** |
| 4 | `GET …/hab/serv?idCentroAte=1001&idServicio=10` | ≥1 fila; tras check **1** `vigente=S` |
| 5 | `GET …/hab/pers?…&idPersonal=90001` | ≥1 fila |
| 6 | `POST …/hab/check` (`Content-Type: application/json`) | `resultado=ok`, `vigentesServ≥1`, `vigentesPers≥1` |
| 7 | `POST …/hab/serv` (vigencia futura) + DELETE cleanup | 200 create / 204 delete |
| 8 | `GET …/turnos/inicio` (regresión T1) | `loginName=admin`, CC=2 |
| 9 | Web `/configuracion/hab-turnos-serv` · `…-pers` · `/turnos/inicio` | 200 (shell) |

Plantilla UI manual (opcional): menú CONFIGURACION → Buscar 1001/10 → lista + Agregar disabled→enabled → popup Aceptar.

## Criterios

| CA | Resultado |
|----|-----------|
| Flyway hab_* + seed | **PASS** (V36/V37 en runtime) |
| API list/create/check/401 | **PASS** smoke host |
| UI CONFIGURACION + paridad chrome | **PASS** código + checklist UI; buscadores diferidos |
| Sin silencios inventario | Tabla capacidades completa |
| Regresión T1 | **PASS** |

## Resultado

**PASS / gate-done T2** con diferidos explícitos: D-TUR-11, D-TUR-12, `turnos-hab-buscadores`, T3+.

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí (HAB serv/pers) |
| Decisión | **cerrado-pre-regla** |
| Viaje (pasos) | smoke host 2026-08-27 (Identity:8080 · Api:8081 · Web:4200) |
| Fixture | — |
| Legacy e2e | no |

No se reabre e2e. Si se toca de nuevo la UI HAB → re-decidir (`regla-playwright-migracion.md`).
