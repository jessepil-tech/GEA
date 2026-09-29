---
title: Proceso — SDD con paridad completa (anti-gap)
version: 1.5.0
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.proceso-sdd-paridad-completa
---

# Proceso — SDD de implementación (anti-gap)

Capa 4 de [`gobierno-migracion.md`](gobierno-migracion.md).  
Complementa [`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md),
[`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md),
[`regla-playwright-migracion.md`](regla-playwright-migracion.md) y
[`regla-evidencia-ejecutable.md`](regla-evidencia-ejecutable.md).  
**Prerrequisito:** relevamiento modular
([`relevamiento-modular-funcional.md`](relevamiento-modular-funcional.md), Fases A–C).

## Principio

| Permitido | Prohibido |
|-----------|-----------|
| Slice chico + **SDD hijo** linkeado (diferir) | Cerrar gate omitiendo capacidad legacy sin traza |
| WAIVE solo si legacy **no** tiene la función (evidencia) | WAIVE por velocidad del piloto |
| Diferir maestros/ABM/permisos con slug | Tratar seed demo como paridad de configuración |

El **mapa de migración** (inventario + matriz + backlog de slugs) se arma **antes**
de implementar. El developer elige del backlog; no inventa el inventario.

## Entrada de un SDD nuevo (obligatorio)

### 1. Relevamiento modular (prerrequisito)

Debe existir (o actualizarse) un relevamiento bajo
[`relevamiento-modular-funcional.md`](relevamiento-modular-funcional.md) con
Fases A–C. Sin eso, no pasar a Clarify.

### 2. Inventario de capacidades (no de pantallas)

Por cada acto de usuario / side-effect legacy, una fila:

| Capacidad | Evidencia legacy | ¿Escritura BD? | ¿Realtime? | ¿Después qué? |
|-----------|------------------|---------------|------------|----------------|
| … | path xhtml/SP/Vue | INSERT/UPDATE/DELETE | WS/socket | ciclo / fin |

### 3. Checklist Clarify (bloqueo de implement)

Responder **antes** de código:

1. **Pipeline de configuración** (maestros, permisos, habilitación) — ¿cubierto o diferido con slug?
2. Happy path (crear / leer / UI).
3. **Ciclo de vida** tras el happy path (flags, caducidad, borrado, histórico).
4. Errores / throttle / permisos.
5. Side-effects (anunciador, PDF, cola, mail…).
6. Qué queda **fuera** → WAIVE (evidencia) o **slug hijo** en `docs/sdd/<slug>/`.
7. **Paridad UI** ([`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md)):
   **Gate de arranque** (no cierre): abrir xhtml **antes** de template; contrato
   duro + criterio de diseñador (Buscar al final; botones compactos, no `authPrimary`).
   **Obligatorio al inicio del corte UI:** (a) inventario `msg.key` → valor → labels Web;
   (b) inventario **validaciones** BB / MessageBundle → UI + API (mismos mensajes;
   create **y** update); (c) **Feedback UX:** CRUD success/error = toast; eliminar =
   modal DS (no `window.confirm`/`alert`); error API manejado en pantalla **sin**
   navegar a `/500`|`/404` globales; (d) **Geometría DoD:** disposición/interacción/
   anchos/cols/`InputWid100`/misma fila alineada al xhtml **antes** de cablear-only
   API y **antes** de pedir smoke; excepciones solo como `diferido(slug)` o nota
   explícita en tasks/verify (sin silencio). Prohibido copy pragmático, omitir
   validaciones porque el happy path guarda, y tratar tamaño/posición como polish.
   Si hay **buscador:** inventariar filtros/cols del popup; no reinyectar label
   compuesto al reabrir; GET combos sin 404 global. Gap → `diferido(slug)`.
   Agents: rules `hospital-gate-ui-arranque` / `hospital-web-gate-ui-arranque` +
   skill `gate-ui-arranque`.
   **Chrome de campo (v1.15):** cada lupa/X/info del `panelGrid` HIS en inventario
   interacción (mismo orden); copy del `title` no cierra el botón.
   **Abrir dialog (v1.16):** `visible`/`display*`/`update` no auto-abren.
   **Volver buscador (v1.17):** `actionBtnVolver` — `set*Buscado(null)` vacía;
   sin callback = conservar. **Overlay (v1.18):** no cerrar por drag-select.
   **Footer leyenda (v1.19):** `dataTable` scrollable + facet footer → leyenda
   anclada (`gt-his-consulta-leyenda`); no `obsTableWrap` T4.
8. **Viaje Playwright** ([`regla-playwright-migracion.md`](regla-playwright-migracion.md)):
   ¿hay pantalla y acto de usuario? → `N/A` (motivo) / `e2e-migrado` /
   `diferido(fixture)` / `cerrado-pre-regla` (gate ya PASS antes de la regla).
   Sin silencio en verify. Si `e2e-migrado`: spec en `Hospital-Web/e2e/` (CI); legacy
   solo opt-in con fixture Oracle vigente. Playwright mocks ≠ G6. Universo de CU =
   matriz SDD, no la batería E2E. Skill `playwright-viaje-slice`.

Sin (1), (3), (6) y (7) resueltos → el SDD **no** pasa a implement.  
Clarify (8) se declara al abrir (tabla en verify); vacío en verify → **no PASS**.
(8) no bloquea implementar Api si la decisión es `N/A` o `diferido(fixture)`.  
Sin inventario copy **ni** inventario validaciones en verify → **no** PASS de paridad UI
(aunque API/happy path pasen).  
Sin paridad de disposición/geometría (o excepciones documentadas) → **no** solicitar smoke.

### 4. Matriz / backlog

- Actualizar matriz del dominio (ej. [`relevamiento-node-anunciador/matriz.md`](../relevamiento/relevamiento-node-anunciador/matriz.md)).
- Estados: **Migrado** | **Parcial** | **Diferido(`slug`)** | **WAIVE**.
- Entrada en [`backlog-orden-2026-08-14.md`](../planificacion/backlog-orden-2026-08-14.md) o sucesor.
- Mapa UI: [`mapa-menu-hospital-web.md`](../relevamiento/mapa-menu-hospital-web.md).
- Árbol menú Legacy↔Web: [`arbol-mapeo-menu-legacy-web.md`](../relevamiento/arbol-mapeo-menu-legacy-web.md).

### 5. Verify anti-gap

En `verify-report.md` del padre, sección fija:

```markdown
## Capacidades legacy
| Capacidad | Estado | Ref |
|-----------|--------|-----|
| … | done / diferido(slug) / WAIVE | … |

## Viaje Playwright
| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí / no |
| Decisión | N/A (motivo) / e2e-migrado / diferido(fixture) / cerrado-pre-regla |
| Viaje (pasos) | … |
| Fixture | mocks / … / — |
| Legacy e2e | no / opt-in (fixture Oracle vigente) |

## Ledger de evidencia
| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|-----------|-------|-----------|-----|----------|
| 1 | … | test / endpoint / escritura / e2e | comando · id · status · path | fecha o commit | verificado / no verificado / no ejecutado |
```

**PASS del padre** exige: ninguna capacidad en “silencio” (ni done, ni diferido, ni WAIVE)
**y** tabla Viaje Playwright resuelta (`N/A` / `e2e-migrado` / `diferido(fixture)` / `cerrado-pre-regla`; vacío no)
**y** **Ledger de evidencia** con artefacto por afirmación
([`regla-evidencia-ejecutable.md`](regla-evidencia-ejecutable.md)): capacidad `done` cuyas
filas queden todas en `no verificado` → **FAIL**; si el corte escribe, al menos una fila de
clase **escritura** con `id` vigente en PG (toast o 201 no alcanzan).
Cola corta: [`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md) § Disciplina
de diferidos (pagaré con carpeta + backlog; cobrar hijo antes de otro módulo).

Si el corte incluye pantallas: sección **Paridad UI (xhtml)** según
[`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md) (labels,
**validaciones BB/MessageBundle**, iconos, paginator, enable/disable, **disposición
+ geometría vs xhtml**, gate de arranque cumplido, excepciones pre-smoke
documentadas). No PASS smoke si la UI diverge del legacy sin `diferido`/nota en el SDD.
No PASS del slice con pantallas si se implementó API-first sin pasar
`gate-ui-arranque`.  
No PASS con pantallas si falta decisión Playwright o si `e2e-migrado` no tiene spec
en `Hospital-Web/e2e/` ([`regla-playwright-migracion.md`](regla-playwright-migracion.md)).
E2E mockeado **no** cierra G6 (Api + Reports + PG).

Si el corte toca home, sidebar o cuelga una ruta: sección **Paridad orientación** según
[`regla-paridad-orientacion-visual.md`](regla-paridad-orientacion-visual.md).

## Plantilla mínima de RF de escritura

Para cada RF que **escribe**:

1. **Crear** (qué fila/evento).
2. **Estado visible** (qué ve el usuario / TV).
3. **Transición** (quién cambia el estado).
4. **Fin** (N, delete, archivo).

Ejemplo deuda abierta: [`ciclo-vida-llamado-anunciador/`](../cortes/anunciador/ciclo-vida-llamado-anunciador/)
(B.1/M2 solo cubrieron 1–2).

## Roles

| Rol | Responsabilidad |
|-----|-----------------|
| Producto / lead migración | Firmar Clarify y backlog de slugs |
| Quien abre el SDD | Completar inventario + matriz + links hijos |
| Quien implementa | No ampliar alcance sin actualizar matriz |
| Verify | Rechazar PASS con gaps silenciosos |
