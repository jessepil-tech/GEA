---
title: Criterio de avance — E2E permanente y datos
status: canonical
owner: grupogea
last_updated: 2026-09-07
phase_id: sdd.hospital.criterio-avance-e2e-datos
indice_blurb: E2E permanente; escritura cobrada en ts; copia Oracle ≠ ABM
---
# Criterio de avance — E2E permanente y datos (no seed)

`phase_id:` **`sdd.hospital.criterio-avance-e2e-datos`**  
Fecha: **2026-09-07** (corregido: PG `ts` = SoT destino; copia Oracle ≠ prueba de ABM)  
Padre: [`gobierno-migracion.md`](gobierno-migracion.md) (capa 5) · Orden: [`backlog-orden-2026-08-14.md`](../planificacion/backlog-orden-2026-08-14.md)

**Estado:** **CANÓNICO** — cómo se avanza a partir de esta fecha. No es un SDD de implementación.

Producto (2026-09-07): no depender de seeds; armar lo necesario para **disponer de data**; cerrar flujos **E2E** con módulos **ya migrados**; no tratar el avance como piloto/MVP desechable.

**SoT del destino:** schema PostgreSQL **`ts`**. Oracle se usó para golden master y para generar DDL. No es la fuente de verdad operativa del HIS nuevo. Extraer filas Oracle→PG es **bootstrap opcional**, no dual-write ni paridad de escritura.

---

## Qué cuenta como avance

Un corte está **hecho** cuando:

1. El CU vive en **`ts`** (paridad de schema) y en el menú/contexto HIS (paridad de orientación).
2. Si el CU **inserta o edita**, la evidencia es una fila **nacida de ese CU** en PG (API/UI), no un INSERT de fixture ni una copia Oracle de la misma tabla. Se registra con **`id` + query que la devuelve** en el ledger del verify ([`regla-evidencia-ejecutable.md`](regla-evidencia-ejecutable.md)); toast de éxito o `201` no cierran la escritura, y una fila borrada tras la prueba tampoco.
3. El flujo se recorre de punta a punta. Lo entregado **no se tira**.

Un corte **no** está hecho si solo corre con DNI demo, turno `OTORGADO` de seed, tablas `*_agi`, o maestros **copiados** que el slice debía poder dar de alta.

`gate-done` de un slice **sigue valiendo como código**. Lo que deja de valer es usar ese slice + seed (o + dump) como prueba de paridad de negocio.

---

## Tres fuentes de datos (no son equivalentes)

| # | Fuente | Qué es | Sirve para | No sirve para |
|---|--------|--------|------------|---------------|
| **1** | **Filas nacidas de CUs migrados** | Hab T2, horario T3, gen T4, otorgar T5, confirmar recepción, llamar, ABM que esté in-scope | Evidencia de que el flujo de **escritura** está migrado; E2E permanente | — |
| **2** | **Bootstrap Oracle→PG** | Carga puntual de **padres** que esta oleada **no** va a ABMear (paciente, centro, prestación de catálogo, …) | Desbloquear FKs / volumen; PG vacío | Declarar ABM migrado; probar T2/T3/T4/T5; cerrar un slug `diferido` de escritura |
| **3** | **Seed Flyway / IT** | Filas desechables (`30111222`, grupos `91001`, turno AGI de test) | Tests locales, smoke disposable | UAT, DoD de verify de producto, “migrado” |

**Armar lo necesario para disponer de la data** = **#1** (CUs que escriben) + **#2** solo donde el corte no es el escritor. No = dump Oracle “porque el happy path queda lindo”. No = más seed.

Copia Oracle **no** es seed (no se inventa en el repo) y **tampoco** es prueba de ABM. Copiar `hab_turnos` o un anunciador demuestra **lectura**. No demuestra el alta.

---

## ABM diferido vs bootstrap

PostgreSQL es la única SoT del destino. No hay un Oracle vivo que siga creando esas filas “hasta el cutover”.

Pregunta por tabla:

> ¿Este corte tiene que **crear o editar** esto en el HIS nuevo?

| Respuesta | Acción |
|-----------|--------|
| **Sí** | El ABM o el comando (T4 generar) **es** el trabajo. Bootstrap de esa tabla **no** cierra el pagaré. |
| **No** | Bootstrap opcional de padres. No abras el ABM por completitud. El slug puede quedar diferido **sin** fingir que la escritura está migrada. |

`diferido(slug)` sigue siendo pagaré ([`regla-waiver-paridad-legacy.md`](regla-waiver-paridad-legacy.md)). La copia no lo cobra.

---

## E2E a cerrar (solo lo ya migrado)

Circuito **ambulatorio mostrador**. No abrir Internación, Nutrición, Lab ni Caja para “completar un piloto”.

Código **ya en `ts`** (no se reescribe como MVP):

| Tramo | Slices | Estado código | Cómo se cobra escritura |
|-------|--------|---------------|-------------------------|
| Login | Identity | Hecho | Login real |
| Shell HIS | `paridad-orientacion-web` | gate-done | N/A (navegación) |
| Tótem / espera | AGI recepción + espera + ticket BIRT | gate-done | Confirmar / recepcionar **en PG** |
| Cola | `paridad-recepcion-cola` M1–M4 | gate-done | Filas de cola nacidas del CU, no seed |
| Llamar → TV | CU-B.1 + ciclo vida + WS | gate-done | `LLAMAR` desde UI |
| Oferta (config) | Turnos T2 hab + T3 horarios | gate-done código | **Alta** hab/horario desde UI, no seed V42 |
| Catálogo puntual | CU-A convenios | gate-done | Alta/edición convenio si el tramo lo exige |

**Hoy el circuito se corta en los datos:** AGI lista `OTORGADO` de seed; T2/T3 se fuman con grupos seed; la cola y el TV se demuestran con DNI demo. Eso no es E2E de producto ni prueba de ABM.

### Flujo 1 — Mostrador

```text
Identity → (AGI | Recepción HIS)
  → turno OTORGADO
  → confirmar / recepcionar
  → cola → Llamar → TV
  → ticket BIRT
```

Cadena de escritura a cobrar (no sustituible por dump):

1. Padres FK: bootstrap **#2** si esta oleada no ABMea paciente/centro/servicio.
2. Hab + horario: **T2/T3 UI** contra PG.
3. Oferta `LIBRE`: **T4**.
4. `OTORGADO`: **T5**. Hasta T5 el mostrador **no** puede cerrarse como escritura de agenda; un `OTORGADO` copiado de Oracle solo permite probar **lectura/cola/llamar**, y hay que etiquetarlo así.
5. Cola / llamado / ticket: CUs ya migrados, disparados sobre filas de 3–4 (o, en transitorio, sobre bootstrap **declarado** — no DoD de T5).

Huecos de **producto** en este flujo (paridad, no MVP):

- Acciones menú Cola B (P1).
- Gate centro/puesto/box [`paridad-recepcion-gate/`](../cortes/recepcion/paridad-recepcion-gate/).
- Impresora térmica (P2): hardware; no bloquea el E2E funcional.
- ABM anunciador/terminal: si hay que **dar de alta** sala/tótem en destino, el slug `anunciador-agi-config-abm` se cobra con INSERT. Copiar la sala no alcanza.

### Flujo 2 — Oferta de turnos (escritura permanente)

```text
hab T2 + horarios T3 (alta en PG)  →  T4 genera LIBRE  →  T5 otorga
  →  Flujo 1 consume OTORGADO nacido en PG
```

- **T4** [`turnos-generacion-grilla/`](../cortes/turnos/turnos-generacion-grilla/): Clarify FIRME. Crea oferta en `ts.turno`.
- **T5** `turnos-agenda-otorgar`: primer CU visible de agenda. Sin T5 no hay prueba de otorgar.
- Prohibido: ensanchar AGI para “crear turnos demo”; copiar `LIBRE`/`OTORGADO` y tratarlo como T4/T5.

---

## Qué construir

Orden, sin abrir módulos nuevos:

1. Bootstrap **#2** de padres que no se ABMean en esta oleada (inventario de tablas + orden FK; una carga, no un sync eterno “porque Oracle es SoT”).
2. Cobrar escritura de lo **ya** gate-done: T2 hab, T3 horario, CU-A si aplica, confirmar recepción, llamar.
3. **T4** generación de grilla (`LIBRE` reales).
4. **T5** otorgar.
5. E2E Flujo 1 sobre filas de 2–4.
6. ABM anunciador/terminal **si** hay que provisionar sala en PG; Cola B / gate recepción como paridad del mismo circuito.

Seed IT **puede quedar** en Flyway para CI. No se usa como dataset de demostración de paridad.

---

## Piloto: archivo histórico, no modo de trabajo

Las carpetas `piloto-agi-*` son el **nombre de slices ya cobrados** sobre `ts`. No se descartan ni se rehacen “en serio después”.

| Decir | Significa |
|-------|-----------|
| “Cerramos el piloto / el MVP” | **Prohibido** como objetivo de un corte nuevo |
| “Medimos el stack con seed” | Solo CI / DEV disposable |
| “Trajimos Oracle y el TV prende” | Bootstrap + lectura; **no** ABM ni T4/T5 |
| “Avance permanente” | Código + filas **#1** + E2E; #2 solo padres fuera de alcance |

[`estado-piloto-vs-general.md`](../estado/estado-piloto-vs-general.md) sigue siendo el snapshot de lo cobrado. El orden diario lo manda este criterio + el backlog.

---

## Qué no hacer

- Abrir Internación / Nutrición / Lab / HC “para tener otro vertical”.
- Tratar T4 como spike o PoC: es el port de `f_gen_grilla_turnos`.
- Sustituir T2/T3/T4/T5 o un ABM in-scope por copia Oracle o seed.
- Declarar E2E cerrado porque el TV se mueve con `30111222`.
- Asumir Oracle como SoT del destino (golden master / DDL ≠ operación).
- WAIVE de maestros “porque el piloto era chico” ([`gobierno-migracion.md`](gobierno-migracion.md)).

---

## Relación con capas

| Capa | Este criterio |
|------|----------------|
| 1 Paridad | E2E sin seed; escritura cobrada con INSERT del CU |
| 2 DDL | Destino = `ts`; Oracle aportó estructura, no operación |
| 3 Relevamiento | Sigue A–C **antes** de un módulo nuevo; bootstrap no cierra Fase C |
| 4 SDD | T4/T5 y, si hace falta, carga bootstrap tienen spec; no se improvisa el copy como verify de ABM |
| 5 Backlog | Semana = cobrar escrituras del circuito + T4; no un tile nuevo |
