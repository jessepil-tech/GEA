---
title: Plan — Paridad orientación Hospital-Web
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-31
phase_id: sdd.hospital.paridad-orientacion-web
---

# Plan — Paridad orientación Web

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL / Api | **Nada.** Solo Hospital-Web (catálogo, dashboard, una página índice). |
| Modelo nav | `SidebarNavItem.moduleEntryRoute?: string`. Tiles leen eso, no el primer hijo. |
| Catálogo | `hospital-menu.catalog.ts` + specs. Satélites filtrados en `buildHospitalModuleTiles`. |
| Rutas | Nueva `/recepcion/inicio`. Hab: mismas rutas, otro padre en el árbol. |
| i18n | Labels actuales OK (Title Case). No clonar MAYÚSCULAS. |

Orden: W0 (este spec) → W1 modelo+tiles → W2 catálogo TURNOS/config → W3 satélites → W4 iconos hojas → W5 índice recepción → W6 verify.

## Cortes

| Corte | Exit |
|-------|------|
| W0 | Clarify propuesto en spec (esta carpeta) |
| W1 | `moduleEntryRoute` + tests (RECEPCIÓN/TURNOS/HC) |
| W2 | Hab bajo TURNOS; Admin General único; Convenios no son el tile |
| W3 | Satélites fuera de grilla HIS |
| W4 | Hojas sin `layers` |
| W5 | `/recepcion/inicio` + tile |
| W6 | Smoke UI + verify orientación + mapa-menu |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Ops sigue yendo a cola “de memoria” (bookmark) | Rutas cola/espera **no** se borran |
| Confundir URL `/configuracion/hab-*` con padre de menú | Spec RF-3; verify mira sidebar, no el path |
| Scope 1240 hojas / prune FARMACIA | Clarify #4 y #10 |
| Gate recepción silenciado | Hijo `paridad-recepcion-gate` + fila verify |
| Nested Dominios | v1 plano; no abrir chrome de 3 niveles |

## Dependencias

- O1 dump (hecho).
- T1 `/turnos/inicio`.
- T2 hab pantallas (siguen existiendo).

## Fuera del plan de código

M2 Identity; T3 horarios; gate recepción; Api.
