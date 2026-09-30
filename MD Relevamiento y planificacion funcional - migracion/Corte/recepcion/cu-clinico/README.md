# Programa CU clínico (3 fases)

Fecha: 2026-08-14  
`phase_id:` **`sdd.hospital.cu-clinico`**

Circuito ambulatorio del dossier, cortado en **tres slices medibles** (no “migrar
ATENCION entero”).

| Fase | Slug SDD | Qué mide | Owner sugerido |
|------|----------|----------|----------------|
| **1** | [`cu-clinico-a-catalogo-abm/`](../cu-clinico-a-catalogo-abm/) | CRUD CQRS + Flyway + UI lista (convenio) | Dev 1 |
| **2** | [`cu-clinico-b-post-recepcion/`](../cu-clinico-b-post-recepcion/) | Post-ticket → espera/llamado (enlace Anunciador) | Dev 2 |
| **3** | [`cu-clinico-c-demanda-espontanea/`](../cu-clinico-c-demanda-espontanea/) | Alta sin turno (camino feliz) | Dev 3 / tras A |

## Dependencias

```text
Fase 1 (catálogo) ──┬──► Fase 2 (post-recepción) ──► opcional enlace anunciador
                    └──► Fase 3 (demanda espontánea) usa catálogo + IDs
```

Fase 2 y 3 pueden **preparar SDD en paralelo**; código de Fase 3 conviene tras
Fase 1 (convenios/pacientes). Fase 2 puede avanzar sobre G1 ya gate-done.

## Índice de lectura

1. [`backlog-orden-2026-08-14.md`](../../../planificacion/backlog-orden-2026-08-14.md)
2. [`trabajo-paralelo-equipo.md`](../../../planificacion/trabajo-paralelo-equipo.md)
3. Spec de la fase que te toca

## Estado programa

| Fase | Estado |
|------|--------|
| 1 Catálogo ABM | **gate-done** (piloto) |
| 2 Post-recepción (lista) | **gate-done** (piloto) |
| 2.1 Llamar recepción | **gate-done** (piloto; acto con evidencia legacy) |
| 2.x Cola rica HOSPITAL_2 | **M1–M4 gate-done** — [`paridad-recepcion-cola/`](../paridad-recepcion-cola/); sync Oracle / menú Cola B diferidos |
| 3 Demanda espontánea | **gate-done** (piloto C1; **no** = módulo ambulatorio Oracle) |

**2026-08-17:** programa piloto **cerrado como medidor**. No ensanchar `*_agi`.  
Corte activo: **paridad recepción/cola**. R3.1 UAT impresora **pospuesto** (sin UAT/hardware).
