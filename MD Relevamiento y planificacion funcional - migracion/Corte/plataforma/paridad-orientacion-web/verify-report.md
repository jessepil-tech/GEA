---
title: Verify — Paridad orientación Hospital-Web
version: 0.2.0
status: gate-done
owner: grupogea
last_updated: 2026-08-31
phase_id: sdd.hospital.paridad-orientacion-web
---

# Verify — Paridad orientación Web

**Gate:** **GATE-DONE** — 2026-08-31 (W6 smoke UI + unit 9/9).

Canon: [`regla-paridad-orientacion-visual.md`](../../../canon/regla-paridad-orientacion-visual.md) ·
[`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) (solo chrome del índice
recepción / sidebar; no reabrir CU hab).

Stack: Identity `:8080` · Api `:8081` · Web `:4200` · PG local `hospital_identity` /
`hospital_api`. Login `admin`.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Tile → inicio de módulo | **done** | RF-1 / RF-2 |
| Hab bajo TURNOS Dominios (15411/15412) | **done** | RF-3 |
| Raíces TURNOS visibles (pending OK) | **done** | RF-3 |
| Satélites fuera de grilla HIS | **done** | RF-5 |
| Hoja = texto | **done** | RF-6 |
| Admin General único (no tile→Convenios) | **done** | RF-4 |
| Gate call center Turnos | N/A | T1 hecho |
| Gate puesto Recepción | diferido(`paridad-recepcion-gate`) | Clarify #6 |
| Copia hab 10811 / árbol 1240 / M2 | diferido (M2 / fuera) | spec deudas |
| Look Verona | WAIVE | contrato D |

## Paridad orientación

| Ítem | Estado | Nota |
|------|--------|------|
| Padre MENU_APLICACION | **done** | hab = TURNOS (hijos 15411/15412) |
| Tile → módulo (no primer CU) | **done** | RECEPCIÓN `/recepcion/inicio`; TURNOS `/turnos/inicio` |
| Gate contexto | diferido(`paridad-recepcion-gate`) / N/A Turnos | |
| Hoja texto (sin icono genérico) | **done** | hab serv/pers sin `app-icon` |
| Satélite vs HIS | **done** | AGI/ANUNCIADOR fuera de grilla; sidebar al final |

## Paridad UI (xhtml)

Índice `/recepcion/inicio`: labels claros a cola / espera; botones compactos DS.
No clonar `inicioRecepcionCentro` (eso es el hijo gate).

| Control | Estado | Nota |
|---------|--------|------|
| Índice no es la cola | **done** | tile aterriza en inicio |
| Enlaces a CUs ya migrados | **done** | cola, espera |

## Smoke

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Tile RECEPCIÓN → `/recepcion/inicio` | **PASS** |
| 2 | Desde índice → cola (ruta intacta) | **PASS** `/recepcion/cola` |
| 3 | Tile TURNOS → `/turnos/inicio`; hab en sidebar TURNOS | **PASS** |
| 4 | Grilla sin AGI/ANUNCIADOR; sidebar Satélites sí | **PASS** (último: AGI, ANUNCIADOR) |
| 5 | Tests `sidebar-nav.config.spec.ts` | **PASS** 9/9 |

## Resultado

**GATE-DONE** — shell orientación Web. Hijo [`paridad-recepcion-gate/`](../../recepcion/paridad-recepcion-gate/)
sigue abierto. Identity `GET /menus` (M2) fuera.
