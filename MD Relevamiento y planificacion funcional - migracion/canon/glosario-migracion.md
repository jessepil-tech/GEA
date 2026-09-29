---
title: Glosario de la migración
status: canonical
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.glosario-migracion
indice_blurb: Corte / stream / carril / gate / compuerta; dónde se escribe cada cosa
---

# Glosario de la migración

Estos términos **no** son sinónimos. Un documento nuevo que los mezcle está mal,
aunque el resto del SDD esté bien.

Narrativa en **español**. El inglés queda para código, identificadores y los
archivos del método (`spec.md`, `plan.md`, `tasks.md`, `verify-report.md`) y
para los slugs.

## Pares

| Término | Es | No es |
|---------|----|--------|
| **stream** | Dominio técnico (Turnos, Recepción, Anunciador…). Tablero de 16. Reserva de BODY. | Una persona. Una carpeta extra en `docs/`. |
| **carril** | Una persona o equipo trabajando en un stream. | El dominio. |
| **módulo** | Unidad de relevamiento (capa 3): `relevamiento-<modulo>/`. A veces coincide con el stream. | Un corte. El HIS entero. |
| **corte** | Fragmento de implementación de un stream (un slug, un gate). Capa 4. | El stream. El relevamiento. El tablero diario. |
| **slice** | Residuo inglés (`abrir-sdd-slice`, algún filename). | Palabra de prosa nueva. En narrativa: **corte**. |
| **slug** | Nombre de carpeta del corte (`turnos-agenda-otorgar`). | El stream. |
| **SDD** | El set `README` + `spec` + `plan` + `tasks` + `verify-report` de un corte. | Un pagaré. Un snapshot de estado. |
| **pagaré** | Corte con solo `README.md`: compromiso, hijo diferido o checklist de cierre. | El seguimiento diario. |
| **gate** | Estado del corte (`gate-done`, parcial, diferido). Vive en `docs/estado/`. | El linter. |
| **compuerta** | Control automático (`verificar-sdd.sh`, hook `stop`). | El estado de negocio del corte. |
| **profundidad** | Declaración A–C de hasta dónde llegó el grafo (`cerrado` / `muestra` / `diferido(slug)` / `N/A`). | El A–C entero. Un call graph. |
| **muestra** | Capa recorrida en parte; el denominador sigue abierto. El corte no la hereda. | Silencio. Cerrado. |
| **dump HIS** | Artefacto Oracle→PG, 2279 tablas vacías. Instalador del catálogo legado. | Un `Vnn` por corte. |
| **Flyway fino** | Identity (otra base) + delta vs dump. | Recrear las 2279. Seed de turnos/pacientes. |
| **seed script** | DML en `Hospital-Api/scripts/sql/seeds/` (`padres/<tabla>/` · `sec-id/` · `oraculo/`), a mano. | Migración Flyway. Evidencia de ABM. `db/dev-seed/` (legado). |

Se migra **por stream**. El stream se parte en **cortes**. El Finder agrupa
`docs/cortes/<stream>/<slug>/` para no perder los 31 de Turnos; no hace falta
un árbol `docs/streams/`.

## Dónde se escribe

| Qué | Dónde | No |
|-----|-------|----|
| Regla o proceso | `docs/canon/` | Otro `gobierno-*.md` suelto |
| Relevamiento A–C | `docs/relevamiento/relevamiento-<modulo>/` | Dentro del corte |
| Corte nuevo | `docs/cortes/<stream>/<slug>/` | `docs/sdd/`, `docs/cortes/<slug>/` plano, `docs/streams/` |
| Snapshot / deuda / gate diario | `docs/estado/` | Carpeta del corte “cerrado” vs “abierto” |
| Spike / golden master | `docs/spikes/` | Un corte de implementación |
| Ruta vieja | `docs/sdd/<…>` (puntero) | Editar el puntero |

Índice de carpetas: [`../README.md`](../README.md). Espina: [`gobierno-migracion.md`](gobierno-migracion.md).
Cifras: [`datos-canonicos.md`](datos-canonicos.md) (quien no es dueño, cita).
Nuevo corte: skill `abrir-sdd-slice`. Auditar: `./tools/verificar-sdd.sh <slug>`.
