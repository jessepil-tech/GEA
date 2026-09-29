---
title: Gobierno documental de la migración
status: canonical
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.gobierno-migracion
indice_blurb: Espina: capas de gobierno (paridad → relevamiento → SDD → backlog)
---
# Gobierno documental de la migración

`phase_id:` **`sdd.hospital.gobierno-migracion`**  
Fecha: **2026-08-26**  
**Estado:** **CANÓNICO** — mapa de capas (no es un índice más)

Este documento es la **espina** de cómo se decide y se documenta la migración.
Los demás SDD de proceso **implementan** una capa; no compiten entre sí.

---

## Capas (orden de lectura y de trabajo)

```text
1. Paridad de producto     →  regla-waiver-paridad-legacy.md
   1b. Orientación / visual →  regla-paridad-orientacion-visual.md  (mapa mental)
       + chrome de pantalla  →  regla-paridad-ui-legacy.md
2. Paridad de schema       →  regla-ddl-postgres-migrado.md
3. Relevamiento de módulo  →  relevamiento-modular-funcional.md   ← antes de “¿migrar X?”
   3b. Mapa HIS (menú)     →  relevamiento-his-orientacion/       ← no sustituye 3
       Inventario global     →  relevamiento-his-inventario-global/  ← tamaño vs avance (walk)
                                 + tablero 16 streams (arbol-dependencias.md § Tablero)
4. SDD de implementación   →  proceso-sdd-paridad-completa.md     ← antes de código
       + viaje Playwright     →  regla-playwright-migracion.md         (Clarify #8 / verify)
       + evidencia ejecutable →  regla-evidencia-ejecutable.md         (ledger en verify)
5. Orden / estado diario   →  backlog-orden-* · estado-piloto-vs-general.md
       + criterio E2E/datos  →  criterio-avance-e2e-datos.md  (no seed; no MVP)

Ejecución (transversal 3–5) →  loop-migracion-corte.md
   ocho pasos: elegir → reservar → firmar universo → fixture → implementar
               → evidenciar → cerrar → publicar
```

| Capa | Pregunta que responde | Cuándo |
|------|----------------------|--------|
| 1 | ¿Qué significa “hecho” en negocio? | Siempre |
| 1b | ¿El usuario encuentra el módulo/hoja donde lo tenía? | Siempre (menú + contexto) |
| 2 | ¿Qué tablas/columnas son la verdad? | Siempre (DDL = `ts`) |
| 3 | ¿Qué hay que migrar del módulo y en qué orden de dependencia? | **Antes** de viabilidad o priorizar |
| 3b | ¿Dónde cuelga en el menú del HIS? | Antes de colgar rutas en Web; dump O1 |
| 3b′ | ¿Cuánto pesa el legado y qué stream/BODY está libre? | Planificar capacidad — tablero 16 streams |
| 4 | ¿Cómo se corta el slice sin gaps (capacidades + viaje UI)? | **Antes** de implementar |
| 4b | ¿Con qué artefacto se prueba cada afirmación del verify? | Al cerrar gate — sin artefacto = silencio |
| 4c | ¿El cálculo portado devuelve **lo mismo** que el PL/SQL? | Al portar lógica — golden master o diferido |
| 4d | ¿Aguanta **datos reales y dos actores** a la vez? | Al cerrar gate — presupuesto declarado en el paso 3 |
| 4e | Paridad **¿con cuál** de las 16 instalaciones? | Al firmar universo — sin esto «igual al legacy» no tiene sujeto |
| 4f | ¿**Quién** puede ejecutarlo y qué queda registrado? | Al cerrar gate — prueba con un actor sin derecho |
| 5 | ¿Qué se hace esta semana? | Coordinación — E2E permanente + data real, no piloto |
| Ejecución | ¿En qué orden, qué reservo y cómo queda retomable? | Todo el corte — [`loop-migracion-corte.md`](loop-migracion-corte.md) |

**Regla de flujo:** no se salta de “idea de módulo” a código.  
`3 → 4 → implementación`. La capa 5 solo elige *cuál* relevamiento/SDD sigue.

---

## Definition of Ready (iniciativa de migración)

Una iniciativa (módulo / CU / WI) está **Ready** para implementación solo si:

1. Existe carpeta `docs/sdd/relevamiento-<modulo>/` (o relevamiento vigente ampliado).
2. `pipeline.md` + `maestros.md` con Fases A–C sin silencios (o N/A con evidencia).
3. `README.md` con veredicto Fase E (viable / prerrequisitos / primer CU / diferidos).
4. Link desde el SDD de implementación (capa 4) al relevamiento.
5. Si el alcance es parcial: **fecha + quién acepta** el parcial en el README.

Sin (1)–(3) → no Ready. El chat o un informe suelto **no** sustituyen el entregable.

**Skeleton al abrir iniciativa:** crear los seis archivos del entregable capa 3
(aunque vacíos) en el mismo PR/cambio de docs que declara el módulo candidato.

**Ejemplo dorado (retrofit):** [`relevamiento-node-anunciador/`](../relevamiento/relevamiento-node-anunciador/).  
**Greenfield aplicado:** [`relevamiento-turnos/`](../relevamiento/relevamiento-turnos/) (T0, 2026-08-27) ·
[`relevamiento-nutricion/`](../relevamiento/relevamiento-nutricion/) (T0, 2026-09-07).

---

## Qué no es cada capa

| Confusión frecuente | Corrección |
|---------------------|------------|
| “Con el happy path operativo alcanza el análisis” | Capa 3 exige pipeline config → maestros → operación |
| “Seed / demo = paridad de configuración” | Seed no cierra maestros ni permisos; diferir con slug |
| “E2E con DNI demo / OTORGADO de seed = flujo cerrado” | Cerrado = CUs migrados + filas **nacidas de esos CUs** en `ts`; copia Oracle = bootstrap de padres, no ABM — [`criterio-avance-e2e-datos.md`](criterio-avance-e2e-datos.md) |
| “Piloto / MVP y después la versión de verdad” | Prohibido. Lo que entra a `ts` es permanente; el nombre `piloto-agi-*` es archivo histórico |
| “Inventario en el chat = relevamiento” | Entregable en `docs/sdd/relevamiento-<modulo>/` |
| “Clarify del SDD reemplaza el relevamiento” | Clarify (capa 4) **consume** el relevamiento (capa 3) |
| “WAIVE porque el piloto es chico” | Prohibido si legacy tiene la capacidad (capa 1) |
| “ABM API listo = pantalla paritaria” | Falta paridad UI (labels, iconos, paginator, disabled, **validaciones**, **geometría**) — [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md) |
| “Estructura de bloques OK = disposición done” | Faltan anchos/filas del xhtml (cols, `InputWid100`, misma `tr`); geometría = DoD v1.9 — no polish |
| “Form guarda = validaciones done” | Falta inventario BB/MessageBundle → UI + API (create y update); gap → `diferido(slug)` |
| “Playwright mocks PASS = paridad de flujo / G6” | E2E UI ≠ stack real; decisión en verify (`regla-playwright-migracion.md`); universo = matriz SDD |
| “La función PL/SQL quedó traducida y compila” | Sin golden master no está portada: el cálculo se compara con el oráculo — [`regla-paridad-logica-plsql.md`](regla-paridad-logica-plsql.md) |
| “Ese plural raro del legacy lo arreglé” | La rareza **es** la paridad; corregirla es decisión de producto firmada, no del que migra |
| “PASS en local con base vacía = listo” | Un actor y cero filas esconden carreras y colapsos de volumen — [`regla-no-funcionales-migracion.md`](regla-no-funcionales-migracion.md) |
| “Leí el legacy, así se comporta” | El repo trae `cliente="TS"`: las **313 ramas por instalación están apagadas**. Se leyó el camino genérico — [`regla-instalacion-referencia.md`](regla-instalacion-referencia.md) |
| “Los permisos se configuran después” | El legacy los valida **en el PL/SQL** y aborta la operación; el rol funcional es regla de negocio — [`regla-paridad-acceso-auditoria.md`](regla-paridad-acceso-auditoria.md) |
| “El proyecto está fuera de alcance, la capacidad también” | Falso: la lista del cliente está en unidades de **código**. `SCHEDULER` afuera deja sin disparador capacidades de circuitos que sí se migran — [`alcance-proyectos-migracion.md`](../planificacion/alcance-proyectos-migracion.md) |
| “Gate-done pre-T2 = validaciones OK” | Falso — deuda explícita [`deuda-validaciones-pre-hab-turnos.md`](../estado/deuda-validaciones-pre-hab-turnos.md) |
| “El xhtml está en configuracion/ → el menú va en Configuración” | Padre = `MENU_APLICACION`, no el path del WAR — [`regla-paridad-orientacion-visual.md`](regla-paridad-orientacion-visual.md) |
| “Relevar todo el HIS = 37 A–C de una vez” | Primero mapa de orientación ([`relevamiento-his-orientacion/`](../relevamiento/relevamiento-his-orientacion/)); A–C por módulo cuando se migre negocio |
| “% migrado = slices del piloto” | El denominador HIS es el árbol de módulos ([`cobertura.md`](../relevamiento/relevamiento-his-orientacion/cobertura.md)); el piloto no es el 100%. Tamaño medido vs avance: [`relevamiento-his-inventario-global/`](../relevamiento/relevamiento-his-inventario-global/) |
| “`search_path` / `currentSchema=ts` = tablas calificadas” | En packages PG las tablas van `ts.<tabla>`; el schema del callable no es el de las tablas — [`regla-ddl-postgres-migrado.md`](regla-ddl-postgres-migrado.md) |
| “`urlLogo` solo si el bean hace `put`” | `ReportManager` siempre inyecta; JSON según 3 casos del [playbook BIRT](../arquitectura/playbook-migracion-reporte-birt.md) §2 |
| “El árbol de módulos es todo el HIS” | Falso: hay capacidades que **ninguna pantalla muestra** — interfaces con terceros ([`relevamiento-integraciones-externas/`](../relevamiento/relevamiento-integraciones-externas/)) y procesos programados ([`relevamiento-procesos-programados/`](../relevamiento/relevamiento-procesos-programados/)). Relevar por menú no las alcanza |
| “Pantallas migradas = circuito cerrado” | Falso: hay que decidir **qué corre solo** (`indice-legacy.sh --jobs`). Por esto el mostrador, el anunciador y Turnos quedaron incompletos con gate cerrado |
| “Hay A–C = el stream está analizado en profundidad” | Falso: A–C declara **hasta dónde** llegó el grafo (`## Profundidad de análisis`). `muestra` no se hereda al corte — [`relevamiento-modular-funcional.md`](relevamiento-modular-funcional.md) · loop paso 3 |

---

## Gate mínimo para agentes y equipo

Antes de responder “conviene migrar / es viable / abramos el SDD de X”:

1. ¿Existe relevamiento vigente (capa 3) con Fases A–C?  
   → Si no: **hacerlo o declarar parcial fechado** (no improvisar veredicto).
2. ¿El README declara `## Profundidad de análisis` (nada en silencio)?  
   → `muestra` es válido; tratarlo como cerrado no lo es.
3. ¿El veredicto (Fase E) lista prerrequisitos y primer CU?  
4. ¿Recién entonces capa 4 (spec/plan) y capa 5 (backlog)?

Análisis que omita maestros / perfiles / habilitación sin marcar N/A → **incompleto**.

Review de docs: un segundo revisor puede rechazar el PR de Migration si faltan
`pipeline.md` / `maestros.md` o si el veredicto trata seed como paridad.

---

## Dónde vive cada cosa

| Artefacto | Path |
|-----------|------|
| Este mapa | [`gobierno-migracion.md`](gobierno-migracion.md) |
| Glosario (corte / stream / gate) | [`glosario-migracion.md`](glosario-migracion.md) |
| Datos canónicos (un dueño por cifra) | [`datos-canonicos.md`](datos-canonicos.md) |
| Relevamiento (proceso) | [`relevamiento-modular-funcional.md`](relevamiento-modular-funcional.md) |
| Relevamientos por módulo | `docs/relevamiento/relevamiento-<modulo>/` |
| Ejemplo dorado | [`relevamiento-node-anunciador/`](../relevamiento/relevamiento-node-anunciador/) |
| SDD anti-gap | [`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md) |
| Runbook de ejecución (8 pasos) | [`loop-migracion-corte.md`](loop-migracion-corte.md) (reservas · universo firmado · fixture · estado del corte) |
| Compuertas ejecutables | [`../tools/verificar-sdd.sh`](../../tools/verificar-sdd.sh) (audita el corte) · [`../tools/indice-legacy.sh`](../../tools/indice-legacy.sh) (candidatos + techo) |
| WAIVE / paridad | [`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md) |
| Paridad UI (xhtml) | [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md) (geometría DoD + **gate de arranque** agentes) |
| Viaje Playwright | [`regla-playwright-migracion.md`](regla-playwright-migracion.md) (decidir en cada SDD; e2e ≠ universo CU) |
| Evidencia ejecutable | [`regla-evidencia-ejecutable.md`](regla-evidencia-ejecutable.md) (ledger; afirmación sin artefacto = silencio) + rule `hospital-evidencia-ejecutable` |
| Paridad orientación / visual | [`regla-paridad-orientacion-visual.md`](regla-paridad-orientacion-visual.md) |
| Relevamiento mapa HIS | [`relevamiento-his-orientacion/`](../relevamiento/relevamiento-his-orientacion/) |
| Integraciones con terceros | [`relevamiento-integraciones-externas/`](../relevamiento/relevamiento-integraciones-externas/) (no cuelgan del menú; esfuerzo real ≠ conteo de clases) |
| Procesos programados | [`relevamiento-procesos-programados/`](../relevamiento/relevamiento-procesos-programados/) (82 jobs; tres circuitos `gate-done` incompletos por esto) |
| **Alcance de proyectos** | [`alcance-proyectos-migracion.md`](../planificacion/alcance-proyectos-migracion.md) (recorte del cliente; qué deja abierto) |
| Consultas al oráculo | [`tools/consultas-relevamiento.sql`](../../tools/consultas-relevamiento.sql) (jobs activos · validadores · roles · volumen) |
| SDD shell orientación (único) | [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) |
| DDL | [`regla-ddl-postgres-migrado.md`](regla-ddl-postgres-migrado.md) |
| Reportes BIRT (sidecar, un `reportId`) | [`regla-migracion-reportes-birt.md`](regla-migracion-reportes-birt.md) · [`playbook-migracion-reporte-birt.md`](../arquitectura/playbook-migracion-reporte-birt.md) |
| Paridad de lógica (PL/SQL) | [`regla-paridad-logica-plsql.md`](regla-paridad-logica-plsql.md) (golden master o diferido; rarezas se preservan) |
| No funcionales | [`regla-no-funcionales-migracion.md`](regla-no-funcionales-migracion.md) (tiempo · volumen · concurrencia) |
| Instalación de referencia | [`regla-instalacion-referencia.md`](regla-instalacion-referencia.md) (16 clientes · 313 ramas en el código) |
| Acceso y trazabilidad | [`regla-paridad-acceso-auditoria.md`](regla-paridad-acceso-auditoria.md) (perfil→menú · rol funcional en PL/SQL · 264 tablas auditadas) |
| Destino de Seguridad | [`destino-seguridad-identity.md`](../planificacion/destino-seguridad-identity.md) (se absorbe en Identity + `Identity-Web`; qué **no** se absorbe) |
| Criterio E2E + datos (no seed) | [`criterio-avance-e2e-datos.md`](criterio-avance-e2e-datos.md) |
| Índice programa | [`../README.md`](../README.md) |
| Cursor Team / rules | [`cursor-team-rules.md`](cursor-team-rules.md) · [`.cursor/`](../../.cursor/) |

---

## Mantenimiento

- Cambios de **proceso** se reflejan aquí primero (una frase en la capa afectada).
- No agregar “también leer X” dispersos en cinco archivos sin actualizar esta espina.
- Dueño: lead de migración.
