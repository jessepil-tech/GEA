---
title: Cortes de implementación
status: active
last_updated: 2026-09-22
---

# Cortes

Se migra **por stream**. El stream se parte en **cortes**. Índice de palabras: [`glosario-migracion.md`](../../canon/glosario-migracion.md).

| Stream | Qué cubre | Cortes |
|--------|-----------|--------|
| [`turnos/`](turnos/) | Oferta, habilitación, agenda, consultas | 40 |
| [`anunciador/`](anunciador/) | Tótem, TV, ciclo LLAMAR, apagado Node | 8 |
| [`recepcion/`](recepcion/) | Cola AGI, llamar, clínico post-recepción | 15 |
| [`plataforma/`](plataforma/) | Identity, shell, orientación, BIRT, Paciente-Web | 6 |
| [`maestros/`](maestros/) | ABM Administración General (tronco territorial) | 6 |

**75 cortes.** El contenido está en `docs/cortes/<stream>/<slug>/`. Las rutas viejas `docs/sdd/<slug>/` siguen siendo punteros (opción B). `docs/cortes/` no deja carpetas planas, para que el Finder agrupe por stream.

## Qué es cada tipo

| Tipo | Qué significa | Qué no es |
|------|---------------|-----------|
| **SDD** | Hay `spec.md` y `verify-report.md`: es el cuerpo del corte | El estado del gate (eso está en [`estado/`](../estado/)) |
| **SDD (sin verify)** | Spec abierto; el gate no se cierra con esta carpeta | Un “en progreso” del tablero diario |
| **parcial** | Hay material de trabajo, no el set SDD completo | Un corte cobrado |
| **pagaré** | Solo `README.md`: compromiso, diferido, hijo o checklist de cierre | Seguimiento diario ni snapshot de piloto |

El seguimiento diario y el gate-done siguen en [`docs/estado/`](../estado/). No se agrupa por “cerrado / abierto”.

Nuevo corte: `docs/cortes/<stream>/<slug>/` (skill `abrir-sdd-slice`). Auditar: `./tools/verificar-sdd.sh <slug>`.
