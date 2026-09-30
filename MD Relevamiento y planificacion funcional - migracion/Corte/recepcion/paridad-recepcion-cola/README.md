# Paridad recepción / cola (HOSPITAL_2)

`phase_id:` **`sdd.hospital.paridad-recepcion-cola`**  
Estado: **gate-done M1–M4** (acciones Cola B / sync Oracle diferidos)  
Fecha: 2026-08-18

## Lectura

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Dos colas, RF |
| [plan.md](plan.md) | Cortes |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Gate + **cuándo se usa cada tabla** |

## Entregado

| Slice | UI / API |
|-------|----------|
| M1–M3 Cola A | `/recepcion/cola` · `…/colas/{id}/…` |
| M4 Cola B | `/recepcion/espera-amb` · `GET …/espera-amb` |

## `cola_espera_recep` — no está diferida

Se usa **desde M1** en cada listado/llamar del puesto.  
Lo diferido a **UAT/pre-corte** es el **sync Oracle→PG** de datos reales (hoy hay seed).  
Lo diferido sin fecha fija de producto es el mirror 1:1 de `LLAMADO_ANUNCIADOR` (display = `ts.llamado_anunciador`).  

> **Avance 2026-08-25 (cutover ts):** el display y el side-effect de llamar operan sobre
> `ts.llamado_anunciador` (V27); el mirror 1:1 de `LLAMADO_ANUNCIADOR` quedó **superseded**
> para schema (la tabla ya está en `ts`). `llamado_paciente` / `public.*` retirado en **V33**.
> Schema canónico según [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

**Ciclo `LLAMAR` (deuda M2):** **cerrada** en núcleo TV —  
[`ciclo-vida-llamado-anunciador/`](../../anunciador/ciclo-vida-llamado-anunciador/) **gate-done** (2026-08-26).  
Criterios y matriz Node→Api: [`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/).  
Sigue diferido: acciones menú Cola B (M5) · sync Oracle→PG UAT.
