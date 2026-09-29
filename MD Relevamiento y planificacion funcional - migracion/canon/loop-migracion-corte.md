---
title: Loop de migración de un corte (runbook de ejecución)
version: 1.1.0
status: canonical
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.loop-migracion-corte
---

# Loop de migración de un corte

Transversal a las capas 3–5 de [`gobierno-migracion.md`](gobierno-migracion.md).
Cifras del programa: [`datos-canonicos.md`](datos-canonicos.md). Palabras: [`glosario-migracion.md`](glosario-migracion.md).
Las capas dicen **qué** exigir; este runbook dice **en qué orden ejecutarlo**.

**Qué es:** el orden operativo de un corte, para que el resultado no dependa de recordar
catorce reglas ni de la memoria de una conversación.

**Herramientas del loop** (el canon dejó de ser solo prosa):

| Comando | Paso | Para qué |
|---------|------|----------|
| `./tools/indice-legacy.sh` | 3 | Genera el índice del legacy (~10 s) |
| `./tools/indice-legacy.sh --semilla X` | 3 | Candidatos + techo evaluado, sin leer xhtml a mano |
| `./tools/indice-legacy.sh --jobs X` | 3 | Procesos programados del dominio: lo que corre **sin pantalla** |
| `./tools/verificar-sdd.sh <slug>` | 0 · 7 | Audita el corte contra el canon; `FAIL` = no se cierra |
| `./tools/verificar-sdd.sh --todos` | — | Estado de los slugs (cortes + spikes + relevamientos) en ~3 s |
| `tools/hook-verificar-sdd.sh` | 7 | Hook `stop` de Cursor: si el turno tocó un slug y el linter falla, lo devuelve como pendiente |

El linter lo corre el agente en los pasos 0 y 7; el hook existe para que la compuerta
no dependa de que se acuerde.

**Distribución del hook al equipo.** Cursor carga hooks de cuatro niveles
(enterprise vía MDM · team cloud · proyecto · usuario) y **los de proyecto se
versionan**: [`.cursor/hooks.json`](../../.cursor/hooks.json) de este repo ya trae la
compuerta, así que quien abra **Hospital-Migration como raíz** del proyecto la tiene
sin instalar nada (requiere workspace confiable). Quien abra una **carpeta padre** con
varios repos corre una vez `./tools/instalar-hook.sh` (idempotente; `--user` lo aplica
a todas las carpetas, `--estado` informa qué hay activo). La distribución cloud a nivel
team es de plan enterprise; no dependemos de ella. Verificación en Cursor:
**Customize → Hooks**.

Skill que orquesta los ocho pasos: [`.cursor/skills/migrar-corte/`](../../.cursor/skills/migrar-corte/SKILL.md).

**Qué no es:** un proceso paralelo. No reemplaza
[`relevamiento-modular-funcional.md`](relevamiento-modular-funcional.md) (capa 3),
[`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md) (capa 4) ni el
backlog (capa 5).

## Los ocho pasos

| # | Paso | Salida | Stop si… |
|---|------|--------|----------|
| 1 | **Elegir** corte | Fila del backlog + stream del tablero | El stream no tiene A–C → solo docs (paso 1b) · el proyecto está fuera de alcance ([`alcance-proyectos-migracion.md`](../planificacion/alcance-proyectos-migracion.md)) |
| 1b | **Relevar** (si falta A–C) | `relevamiento-<modulo>/` Fases A–E + `## Profundidad de análisis` | — (no se pasa a código en el mismo corte) |
| 2 | **Reservar** recursos | Bloque *Reservas* en el `README.md` del slug | Un recurso ya está tomado |
| 3 | **Firmar** universo | Inventarios del slug (copy, validaciones, geometría, capacidades) | El universo supera el techo → partir el corte |
| 4 | **Contratar** fixture | Bloque *Fixture* en el `README.md` del slug | Falta un padre y nadie lo va a crear → `diferido(fixture)` declarado ahora, no al final |
| 5 | **Implementar** | Código en el repo de producto | Pantalla sin Gate UI de arranque |
| 6 | **Evidenciar** | Ledger en `verify-report.md` | Afirmación sin artefacto |
| 7 | **Cerrar** gate | Verify anti-gap PASS/FAIL | Silencio, `done` sin artefacto, presupuesto de diferidos excedido |
| 8 | **Publicar** | Matriz + backlog + estado + tablero | — |

Orden fijo. Saltar 2–4 es lo que produce cortes que se convierten en módulos, dos personas
sobre el mismo package, y descubrir a mitad del click-through que faltan padres en PG.

## Paso 2 — Reservar antes de tocar código

Un corte **toma** recursos. Se anotan en el `README.md` del slug y se reflejan en el
tablero de streams ([`relevamiento-his-inventario-global/arbol-dependencias.md`](../relevamiento/relevamiento-his-inventario-global/arbol-dependencias.md) § Tablero).

```markdown
## Reservas
| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | … | — |
| BODY / package | … | libre → reservado por este corte |
| Rango Flyway | `n/a` o Vnn (solo delta vs dump) | — |
| Tablas `ts` que escribe | … | — |
| Rama | … | — |
```

Reglas:

1. **BODY reservado = un solo escritor.** Dos cortes portando firmas del mismo package
   duplican SQL y generan drift.
2. **Un rango Flyway por corte, solo si hay delta DDL** (objeto que el dump no tiene:
   vista/función/package PG, gap vs Oracle). Identity va al Flyway de
   **Hospital-Identity**, no al del Api. El schema HIS de tablas **ya está**: no se
   reserva `Vnn` para `CREATE TABLE ts.*` existente ni para seed. Si no hay delta → `n/a`.
3. Si un recurso está tomado, el corte **no arranca**: se elige otro del tablero o se espera.
4. Al cerrar (paso 8) las reservas se liberan en el tablero.

Dos cortes son paralelizables cuando **no** comparten BODY, ni rango Flyway, ni writer de
las mismas tablas. Los pares permitidos y prohibidos están en el mismo tablero
(§ Qué sí / qué no en paralelo).

## Paso 3 — Universo firmado (y por qué no es el grep)

El descubrimiento produce **candidatos**; el corte se ejecuta contra el **universo firmado**.
Son dos conjuntos distintos y solo el segundo obliga.

| Conjunto | Qué es | ¿Obliga en el gate? |
|----------|--------|---------------------|
| Candidatos | Lo que apareció al recorrer el legacy | No |
| **Universo firmado** | Lo que Clarify aceptó para *este* corte | Sí: `done` / `diferido(slug)` / `WAIVE` |

```bash
./tools/indice-legacy.sh --semilla <ACCION o basename>
```

El índice ([`indice-legacy/`](../relevamiento/indice-legacy/)) recorre el legacy una vez y responde
la cadena de la semilla, beans, tronco, reportes y firmas PL/SQL con el techo ya
evaluado. Emite candidatos: **no** firma nada.

Cómo se acota (mismo criterio que el gate UI de arranque):

- **Semilla:** el `ACCION` del menú o el xhtml del `cortes.md`. **Nunca** la carpeta
  (`pages/laboratorio` son 169 xhtml, no un CU).
- **Un hop:** `ui:include`, `p:dialog` y buscadores de la cadena de esa semilla. El segundo
  hop es candidato, no obligación.
- **Métodos, no clases:** de un bean se toman los métodos del acto de usuario de este corte,
  no las 12 k líneas de la clase.
- **Tronco afuera** (ver tablero § Tronco compartido): `PERSONAS`, `GENERAL`,
  `BBSessionData`, `BBDatosPaciente` y afines, `pages/configuracion`, `pages/buscadores`.
  Entran solo por firma que **este** corte invoca.
- **BIRT:** un `.rptdesign` entra si el botón de esta pantalla lo llama. `HTMLDocument` es
  wrapper, no un layout a portar.

### Techo (aborta el corte, no lo agranda)

Orientativo; el lead ajusta. Si al firmar el universo se supera, **partir el corte**:

| Dimensión | Techo |
|-----------|------:|
| xhtml de la cadena | 8 |
| Beans de dominio (sin tronco) | 15 |
| Firmas de package | 20 |
| `.rptdesign` | 5 |

Un corte que roza el techo es un módulo mal cortado. Es más barato verlo acá que tres
semanas después.

**Precisión antes que recall:** ante duda, la capacidad queda como candidato o
`diferido(slug)`, no dentro del corte. Lo que falte se cobra en el corte siguiente del
mismo stream; lo que sobre paraliza este.

### El corte no hereda la profundidad del stream

El A–C del módulo declara hasta dónde llegó cada capa del grafo
([`relevamiento-modular-funcional.md`](relevamiento-modular-funcional.md)
§ Profundidad de análisis). Ese estado **no se transfiere** al corte: «el stream ya está
relevado» no firma ningún universo.

| En el A–C | Qué obliga acá |
|-----------|----------------|
| `cerrado` | Se cita y se usa; no se rehace |
| `muestra` | Las capacidades de esa capa que **este** universo toca se cierran ahora, o salen como `diferido(slug)` |
| `diferido(slug)` | Si el corte las toca, el slug es este; si no, se deja donde está |
| `N/A` | Se verifica que siga siendo N/A para este universo |

Correr el índice tampoco cierra una capa por sí solo. El índice atribuye la firma a la clase
donde está el literal, así que un bean que delega en `HOSPITAL-BUSINESS` deja la firma fuera
de su fila; y cuando el SoT de packages no está disponible, las firmas son **candidatas
derivadas de literales Java**, no el catálogo del BODY
([`indice-legacy/`](../relevamiento/indice-legacy/) § Límites conocidos). Un stream de 77
funciones del que el índice ve tres no está cerrado: está en `muestra`.

### Qué corre solo en este circuito

El universo firmado se arma desde la pantalla, y hay capacidades que **ninguna pantalla
muestra**: procesos programados e interfaces con terceros. El relevamiento por menú no puede
verlas y la matriz anti-gap tampoco, porque no hay pantalla desde donde preguntar.

```bash
./tools/indice-legacy.sh --jobs <dominio|tabla>
```

Cada job del circuito termina en **se porta**, **`diferido(slug)`** o **N/A con motivo**.
Silencio = gap. Dos cosas que ya costaron caro (ver
[`relevamiento-procesos-programados/`](../relevamiento/relevamiento-procesos-programados/README.md)):

1. **El nombre del job miente.** `MigrarTurnoVencidoJob` también purga la cola de espera del
   mostrador y apaga los llamados del anunciador. Hay que leer qué hace, no qué se llama.
2. **La frecuencia no está en el código:** vive en `TS.TAREA_PROGRAMADA`, con un flag
   `ACTIVA` que decide si corre. Sin ese dato no se sabe si el job está vivo, así que no se
   puede firmar su alcance.

### Contra qué legacy, y para quién

**Instalación de referencia.** El HIS tiene 16 clientes y **313 ramas `esClienteX()` en el
código**; el repositorio trae `cliente="TS"`, que no coincide con ninguna, así que leyendo el
legacy local **las ramas están apagadas**. El corte declara su instalación y decide cada rama
del universo: [`regla-instalacion-referencia.md`](regla-instalacion-referencia.md).

**Acceso.** Qué perfiles veían la entrada de menú y qué rol funcional exige el PL/SQL (lo
valida en la base y aborta la operación). Se declara acá y se prueba con un actor sin derecho
en el paso 6: [`regla-paridad-acceso-auditoria.md`](regla-paridad-acceso-auditoria.md).

### Dos cosas más que se firman acá (no al cerrar)

**Decisión por firma PL/SQL.** Cada firma del universo queda con una de tres:
**portar** (golden master), **puente** (registrado en `pendientes-solo-oracle.md`) o
**rediseñar** (firma de producto). La cuarta —traducirla y confiar— no existe:
[`regla-paridad-logica-plsql.md`](regla-paridad-logica-plsql.md).

**Presupuesto no funcional.** Tiempo objetivo de la operación, volumen contra el que se va
a medir y qué recurso se disputa (turno, cama, numerador, stock). Declararlo al cerrar es
declararlo cuando ya no se puede cambiar el diseño:
[`regla-no-funcionales-migracion.md`](regla-no-funcionales-migracion.md).

## Paso 4 — Contrato de fixture

Antes de implementar se declara con qué datos se va a probar. Sin esto, el corte llega al
click-through y descubre que PG no tiene padres — y aparece la tentación de un `Vnn` seed.

```markdown
## Fixture
| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| … | sí (es el ABM) / no | CU previo · bootstrap padres · — | disponible / falta |
```

Reglas ([`criterio-avance-e2e-datos.md`](criterio-avance-e2e-datos.md)):

1. Si el corte **es** el ABM de esa tabla, el dato **no** se bootstrapea: crearlo es el trabajo.
2. Si no lo es, se permite bootstrap de padres (fuente **#2**), declarado como tal.
3. Seed = `Hospital-Api/scripts/sql/seeds/` (DML a mano; `padres/<tabla>/` ·
   `sec-id/` · `oraculo/`). **No** Flyway. **No** `db/dev-seed/` para seeds
   nuevos. No seedear la tabla que este corte ABMea. Sirve
   para CI/fixture, **no** como evidencia de paridad (fuente **#3**).
   Origen de padres: dump PG → copia Oracle (lectura) → mock **solo** si no
   hay conexión ([`regla-fixture-oracle-padres.md`](regla-fixture-oracle-padres.md)).
4. Si un padre falta y nadie lo va a crear en este corte → `diferido(fixture)` **desde el
   arranque**, con nombre, no como sorpresa del verify.

## Paso 5 — Implementar

Sin novedad respecto del canon: Gate UI de arranque antes del template
([`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md)), scaffold del starter de cada
repo, un CU por vez por repo. La API que responde no cierra la pantalla.

## Paso 6 — Evidenciar

Ledger con artefacto por afirmación ([`regla-evidencia-ejecutable.md`](regla-evidencia-ejecutable.md)).
Se llena **mientras** se ejecuta, no al final de memoria: `verificado` /
`no verificado` / `no ejecutado`, y la escritura con `id` vigente en PG.

Además de las clases habituales, el corte cierra las dos que se firmaron en el paso 3:
**golden master** por cada firma portada y **no funcional** con la medición y la prueba de
dos actores. Un cálculo sin comparación contra el oráculo y una grilla sin medir contra
volumen son silencios, no PASS.

## Paso 7 — Cerrar

```bash
./tools/verificar-sdd.sh <slug>     # exit 1 = el gate no cierra
```

El linter chequea lo que antes dependía de recordar: ledger presente, `done` con
respaldo, escritura con `id`, bloques de Reservas y Fixture, verdictos válidos,
links vivos. Un verify anterior al **2026-09-15** se audita como WARN
(`cerrado-pre-regla`); `--estricto` lo exige igual.

Además, verify anti-gap ([`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md) § verify)
y dos guardas del programa:

**Presupuesto de diferidos.** El criterio de cementerio de
[`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md) (padre PASS con **más de 3**
hijos abiertos sin fecha) se aplica también al **stream**: pasado ese número, el stream no
abre corte nuevo hasta cobrar hijos o hasta que producto firme que dejan de ser paridad.

**Regresión, no solo el corte.** Antes del PASS corren también las pruebas del circuito ya
migrado que comparte tablas o troncos con este corte (smokes de `Hospital-Api/tools/smoke-*.sh`
y la batería `npm run e2e` de Hospital-Web). Migrar un stream nuevo no puede romper Turnos ni
el mostrador en silencio. El set exacto lo fija el lead por stream.

## Paso 8 — Publicar y dejar el corte retomable

Actualizar matriz del dominio, `backlog-orden-*`,
[`estado-piloto-vs-general.md`](../estado/estado-piloto-vs-general.md) y el tablero de streams
(liberar reservas).

Y dejar en el `README.md` del slug el **estado del corte**, para que cualquiera (o una
sesión nueva) lo retome sin releer una conversación:

```markdown
## Estado del corte
| Campo | Valor |
|-------|-------|
| Paso del loop | 1–8 |
| Reservas | activas / liberadas |
| Universo firmado | path de los inventarios |
| Fixture | resuelto / diferido(fixture) |
| Evidencia | N filas verificado / M no verificado / K no ejecutado |
| Diferidos abiertos | slugs |
| Próximo paso | una línea |
```

Ese bloque es la unidad de continuidad del programa: vive en git, no en un chat.

## Anti-patrones del loop

- Empezar por el código y «después» documentar reservas, fixture o universo.
- Tomar la carpeta del módulo como semilla.
- Promover candidatos a obligación sin snippet que lo justifique.
- Ampliar el corte al chocar con el techo, en vez de partirlo.
- Descubrir el fixture faltante en el click-through.
- Llenar el ledger de memoria al cerrar.
- Cerrar PASS con el presupuesto de diferidos excedido.
- Dejar el próximo paso solo en la cabeza de quien lo hizo.

## Relación con el canon

| Paso | Canon que aplica |
|------|------------------|
| 1 · 8 | `backlog-orden-*` · `estado-piloto-vs-general.md` · tablero de streams |
| 1b | `relevamiento-modular-funcional.md` |
| 2 | Tablero § Tablero y § paralelo · `trabajo-paralelo-equipo.md` |
| 3 | `proceso-sdd-paridad-completa.md` § inventario · `regla-paridad-ui-legacy.md` · `regla-paridad-logica-plsql.md` (decisión por firma) · `regla-no-funcionales-migracion.md` (presupuesto) |
| 4 | `criterio-avance-e2e-datos.md` |
| 5 | `regla-paridad-ui-legacy.md` · arquitectura de cada repo |
| 6 | `regla-evidencia-ejecutable.md` · `regla-paridad-logica-plsql.md` (golden master) · `regla-no-funcionales-migracion.md` (medición) |
| 7 | `proceso-sdd-paridad-completa.md` § verify · `regla-waiver-paridad-legacy.md` · `regla-playwright-migracion.md` |

Skills del programa: [`.cursor/skills/`](../../.cursor/skills/) —
`migrar-corte` (orquesta 1–8), `relevamiento-modular` (1b), `abrir-sdd-slice` (2–4),
`gate-ui-arranque` (5), `playwright-viaje-slice` (6), `cerrar-gate-antigap` (7).
