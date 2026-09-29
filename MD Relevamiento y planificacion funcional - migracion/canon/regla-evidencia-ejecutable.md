---
title: Regla — evidencia ejecutable (verify verificable, no narrado)
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.regla-evidencia-ejecutable
---

# Evidencia ejecutable — el verify se prueba, no se redacta

Capa 4 de [`gobierno-migracion.md`](gobierno-migracion.md).
Engancha [`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md) § verify,
[`criterio-avance-e2e-datos.md`](criterio-avance-e2e-datos.md) (fuentes de datos) y
[`regla-playwright-migracion.md`](regla-playwright-migracion.md).

**Problema que resuelve:** un `verify-report.md` puede quedar impecable **sin que nada
se haya ejecutado**. La matriz anti-gap detecta capacidades en silencio; no detecta
afirmaciones sin respaldo. Con agentes en el loop, el modo de falla dominante deja de
ser el gap y pasa a ser el **PASS narrado**.

## Principio

> Una afirmación de estado **sin artefacto reproducible** es un **silencio**, no un PASS.

Silencio ya bloquea el gate ([`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md) § verify).
Esta regla extiende ese criterio de las **capacidades** a las **pruebas**.

## Artefacto por clase de afirmación

| Clase | Artefacto mínimo | No alcanza |
|-------|------------------|------------|
| Build / compilación | Comando + exit code | «compila», «sin errores» |
| Test (IT / unit / vitest) | Comando + nombre de la clase o spec + conteo PASS/FAIL | Citar el nombre del test sin haberlo corrido en esta tanda |
| Endpoint | Método + ruta + **status** observado | «el API responde» |
| Escritura en `ts` | **`id` de la fila** + query que la devuelve + qué CU la creó | «guardó bien», toast de éxito |
| Click-through / e2e | Paso + selector o texto + valor medido (p. ej. `naturalWidth`) + trace o screenshot | «se ve igual», descripción de la pantalla |
| PDF / BIRT | Path del archivo + tamaño o páginas, motor real | Download mockeado ([`regla-playwright-migracion.md`](regla-playwright-migracion.md)) |
| Smoke ops (G6) | Quién, fecha y **frase textual** del operador | «el usuario lo aprobó» |
| Geometría / copy | Path del inventario + medida del xhtml vs Web | «respeta el layout» |
| **Golden master** (cálculo portado) | Firma `PKG.f_x` + nº de casos + origen (captura u oráculo sintético) + test de replay + PASS/diffs ([`regla-paridad-logica-plsql.md`](regla-paridad-logica-plsql.md)) | «la función quedó traducida», test propio en verde |
| **No funcional** | Operación + medición con percentil + filas de la tabla dominante + prueba de dos actores ([`regla-no-funcionales-migracion.md`](regla-no-funcionales-migracion.md)) | «anda rápido», promedio, cronómetro contra base vacía |
| **Acceso** | Actor **sin** el rol intenta la operación + rechazo observado con el mensaje del legacy ([`regla-paridad-acceso-auditoria.md`](regla-paridad-acceso-auditoria.md)) | PASS con el usuario administrador; «los permisos se configuran» |
| `N/A` | Motivo escrito | Celda vacía, guion |

Toast, log de consola y «debería» **no** son artefactos.

## Verdictos (tres, sin zona gris)

| Verdicto | Cuándo |
|----------|--------|
| **verificado** | Artefacto presente y atado a un `ref` (commit, fecha, tanda) |
| **no verificado** | La afirmación existe pero el artefacto no está registrado → **re-ejecutar** |
| **no ejecutado** | Declarado explícitamente (bloqueo, falta de fixture, fuera del corte) |

**Prohibido inferir.** Si el artefacto no está, el verdicto es `no verificado`, aunque el
código «claramente funcione». `no ejecutado` es honesto; `verificado` sin artefacto no.

## Caducidad por cambio de código

La evidencia se ata al `ref` en que se produjo. Si después se toca el código del corte,
las filas afectadas **vuelven a `no verificado`**. No se hereda un PASS a través de un fix.

Es la misma disciplina que `cerrado-pre-regla` en
[`regla-playwright-migracion.md`](regla-playwright-migracion.md): un gate anterior no
exonera el corte nuevo.

## Ledger de evidencia (sección fija del verify)

Además de **Capacidades legacy** y **Viaje Playwright**:

```markdown
## Ledger de evidencia
| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|-----------|-------|-----------|-----|----------|
| 1 | … | test / endpoint / escritura / e2e / … | comando · id · status · path | fecha o commit | verificado / no verificado / no ejecutado |
```

Reglas del ledger:

1. Toda afirmación de la sección **Capacidades legacy** con estado `done` tiene **al menos
   una** fila aquí.
2. `done` con todas sus filas en `no verificado` → **FAIL** del gate (no `done`).
3. Si el corte **escribe**, hay al menos una fila de clase **escritura** con `id` vigente
   en PG. Un `id` borrado después de la prueba **no** cierra la escritura
   ([`criterio-avance-e2e-datos.md`](criterio-avance-e2e-datos.md) fuente **#1**).
4. Seed, bootstrap Oracle y mocks se etiquetan como tales en el artefacto; no se presentan
   como escritura del CU.

## Anti-patrones (rechazo de gate)

- «IT verde» sin comando ni conteo, o citando una tanda anterior al último cambio.
- Marcar `done` porque el API devolvió 201, sin `id` ni query de la fila.
- Click-through descrito en prosa, sin selector, valor medido ni trace.
- Reusar un PASS previo después de un fix en el mismo corte.
- Borrar la fila de prueba y dejar `done` en la capacidad de escritura.
- Convertir un bloqueo en `verificado` («faltaban padres, pero el flujo es correcto»).

## Relación con las reglas vigentes

| Regla | Qué agrega esta |
|-------|-----------------|
| [`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md) | Capacidad sin traza = silencio → **también** afirmación sin artefacto |
| [`criterio-avance-e2e-datos.md`](criterio-avance-e2e-datos.md) | Fuente **#1** exige `id` + query, no toast |
| [`regla-playwright-migracion.md`](regla-playwright-migracion.md) | El viaje declarado se respalda con trace; mocks ≠ G6 |
| [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md) | Geometría con medida, no con adjetivos |
| [`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md) | WAIVE necesita evidencia de que el legacy **no** tiene la capacidad |

## Por qué habilita autonomía

Un agente puede ejecutar el corte completo (gate UI → API/`ts` → e2e → verify) sin que
nadie relea el chat: el ledger es **auditable a posteriori** y falla solo. Sin esta regla,
más autonomía = más deuda invisible.

Rule (agents, `alwaysApply`): [`.cursor/rules/hospital-evidencia-ejecutable.mdc`](../../.cursor/rules/hospital-evidencia-ejecutable.mdc).
Skill de cierre: [`.cursor/skills/cerrar-gate-antigap/`](../../.cursor/skills/cerrar-gate-antigap/SKILL.md).
Team rule **9**: [`cursor-team-rules.md`](cursor-team-rules.md).
