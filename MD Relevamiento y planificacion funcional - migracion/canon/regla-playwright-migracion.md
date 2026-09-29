---
title: Regla — Playwright en la migración Hospital
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-04
phase_id: sdd.hospital.regla-playwright-migracion
---

# Playwright — viaje de usuario (no universo de CU)

Capa 4 de [`gobierno-migracion.md`](gobierno-migracion.md).  
Enganche: [`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md) § Clarify (8) y § verify.

**Intención:** el mismo **viaje de usuario** (pasos) en legacy y migrado.  
**No es:** descubrir el spec recorriendo Oracle, ni cubrir todos los CU con E2E.

El universo de casos sigue en la **matriz SDD** (`done` / `diferido(slug)` / `WAIVE`).  
Playwright ejecuta **viajes críticos** de esa matriz.

## Decisión obligatoria (sin silencio)

Todo SDD de implementación declara en `verify-report.md`:

```markdown
## Viaje Playwright
| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí / no |
| Decisión | N/A (motivo) / e2e-migrado / diferido(fixture) / **cerrado-pre-regla** |
| Viaje (pasos) | path a tabla o `spec.md` |
| Fixture | mocks / centro-servicio-fecha PG / — |
| Legacy e2e | no / opt-in (fixture Oracle vigente) |
```

**PASS** del slice con pantallas exige esa tabla. Vacío = silencio = no PASS.

Un SDD con **varios viajes** (ej. T4 generar + eliminar + consultar): **una fila por viaje**, no un silencio conjunto.

## Ya gate-done sin e2e (no reabrir)

Slices cerrados **antes** de esta regla (smoke host / IT / ops, sin Playwright) **siguen válidos**.

- En verify: Decisión = **`cerrado-pre-regla`** + fecha de smoke/gate.
- **No** reescribir T2/T3/HAB ni inventar e2e retroactivo.
- Si **se vuelve a tocar la UI** de ese slice → re-decidir (`e2e-migrado` o `diferido(fixture)`). El gate anterior no exonera el corte nuevo.

`cerrado-pre-regla` ≠ “no hubo tiempo”. Solo aplica a gate ya PASS documentado.

## Cuándo SÍ (aplicar)

Hay xhtml/page con acto de usuario (consultar, grabar, drill-down, imprimir, llamar).

Entonces:

1. Tabla de **pasos del viaje** desde xhtml/BB (no desde un combo hallado en runtime).
2. E2E en `Hospital-Web/e2e/` del **migrado** (mocks o Api+PG con fixture **nombrado**).
3. CI: ese spec corre con `npm run e2e`.
4. G6 / PDF BIRT / datos reales **no** se sustituyen por download mockeado.

Skill: `Hospital-Migration/.cursor/skills/playwright-viaje-slice/`.

## Cuándo N/A (no aplicar, con motivo)

- Slice solo Api / Flyway / JDBC / reporte sidecar sin pantalla de este corte.
- Sin acto de usuario (job, seed, cutover DDL).
- Motivo **escrito** en la tabla. “No hubo tiempo” ≠ N/A.

## Cuándo diferido(fixture)

El viaje existe pero **no hay datos estables** en PG (o Oracle, si se quiere dual).  
Slug hijo o nota: qué fixture falta. No escanear ambientes vivos para inventarlo.

## Legacy E2E (opt-in)

**Opt-in** = no corre en CI ni en `npm run e2e`. Solo `e2e:legacy` + `LEGACY_E2E_*`.

| Condición | Acción |
|-----------|--------|
| Fixture Oracle **vigente** (mismo hecho de negocio que PG, o documentado) | Permitido, skip si el dato desaparece |
| No hay fixture / hay que “buscar un mes con S” | **Prohibido** |
| Paridad 1:1 automática legacy↔migrado | Solo con **el mismo** dataset en ambos lados |

Dos runners, un viaje (PrimeFaces ≠ Angular). No un spec compartido.

## Prohibido

- Cerrar gate / verify **solo** porque Playwright con mocks pasó.
- Completar el spec **escaneando** Oracle (centros × servicios × años).
- Versionar probes/scan como entrega del slice.
- Tratar E2E legacy como paridad del universo de CU.
- Sustituir smoke G6 (Api + Reports + PG) por PDF mockeado.

## Relación con smoke y IT

| Prueba | Qué cierra |
|--------|------------|
| IT Api | Contrato HTTP / CQRS |
| Playwright migrado | Orquestación UI del viaje |
| G6 smoke | Stack real (datos PG, BIRT, Identity) |
| Playwright legacy opt-in | El mismo viaje en HIS, si hay fixture |

Ninguna sustituye a las otras.
