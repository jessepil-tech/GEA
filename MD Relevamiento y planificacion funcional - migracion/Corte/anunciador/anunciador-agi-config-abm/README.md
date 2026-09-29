---
title: SDD — ABM Anunciador + Terminal AG
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.anunciador-agi-config-abm
---
# SDD — ABM Anunciador + Terminal AG (`anunciador-agi-config-abm`)

`phase_id:` **`sdd.hospital.anunciador-agi-config-abm`**  
Padre capa 3: [`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/) (corte 7 / P3)  
Cierre: [`cierre-paridad-agi-anunciador/`](../cierre-paridad-agi-anunciador/)

Producto (2026-09-07): **cerrar el satélite** AGI+Anunciador. Operación TV/tótem ya
gate-done; falta escritura de config en `ts` (seed ≠ ABM). Turnos T4/T5 = otro owner.

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Problema, Clarify FIRME, RF, hijos |
| [plan.md](plan.md) | Cortes C0–C5 (C0–C4 UI; C5 `diferido(anunciador-agi-config-abm-c5)`) |
| [tasks.md](tasks.md) | Checklist |
| [inventario-copy-msg.md](inventario-copy-msg.md) | G0 copy |
| [inventario-validaciones.md](inventario-validaciones.md) | G0 validaciones |
| [inventario-geometria.md](inventario-geometria.md) | G0 geometría + menú |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | G0 disparadores |
| [verify-report.md](verify-report.md) | Gate (C1–C3 API IT; C4-1 click PASS; C4-2 diccionario PASS / terminal shell; C5 diferido) |

**No es:** display TV, WS, Llamar, ticket, Cola B, gate recepción, T4/T5.

## Estado del corte

**active — gate no cerrado.** Escritura vigente en dump `grupogea-hospital_dev`:
`ts.anunciador` **id=1** (`P3-COBRO`) + diccionario `P3COBRO`. Padres #2:
`ambiente_amb` 2 + `recepcion` 2001 (`Hospital-Api/scripts/sql/seeds/padres/`).
Web `:4210` UP. C5 **`diferido(anunciador-agi-config-abm-c5)`**.
No se toca M1a (`centro` 1002–1004).

Instalación de referencia: **genérica (`cliente="TS"`)**. No se portan ramas `esClienteX()`
en este corte (`diferido(multi-instalacion)`).

Jobs del circuito: `LlamadorAnunciadorJob` (AGI) → `diferido(relevamiento-procesos-programados)`.
Premisa: HOSPROD no lo tiene (y el resto está `ACTIVA=N` porque no es prod); **prod se
asume encendido** hasta confirmación.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Satélite AGI + Anunciador (no tile origin) | este workspace |
| BODY / package | n/a — escritura JDBC sobre `ts.*`; no se porta package Oracle en este corte | libre |
| Rango Flyway | **ninguno nuevo**: DDL ya en V27 / V29 / V31. Este corte no abre Vnn | n/a |
| Tablas `ts` que escribe | `anunciador` · `anunciador_ambiente` · `diccionario_anunciador` · `terminal_ag` · opciones de terminal | writer de este corte |
| Rama | `dev/dev` (Api + Web) | — |

No pisa `ts.turno` ni el BODY TURNOS (otro owner).

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.anunciador` / diccionario / terminal | **sí** (es el ABM) | CU | cobrado 2026-09-18 dump `grupogea-hospital_dev`: **id=1** `P3-COBRO` · diccionario `P3COBRO`. NextId: `scripts/sql/seeds/sec-id/anunciador.sql`. Seed `ANU-DEMO` 1001 **no** está en este dump y ≠ cobro. El id=3 de `hospital_api` bootstrap **no** rige |
| `ts.ambiente_amb` (vínculo + lupa terminal) | no | seed #2 `Hospital-Api/scripts/sql/seeds/padres/ambiente_amb/` | **2 filas** (Consultorio 1/2, centro 1001). No es ABM de este corte |
| `ts.recepcion` (destino de opción terminal) | no | seed #2 `Hospital-Api/scripts/sql/seeds/padres/recepcion/` | **id=2001**. Gate recepción = otro corte |
| `ts.puesto_recepcion` | no | seed #2 `Hospital-Api/scripts/sql/seeds/padres/puesto_recepcion/` | puestos 3001/3002 |
| Seed IT `ANU-DEMO` / `TERMINAL 1` / `DR.` | no | Flyway/IT | **no** es evidencia de paridad (fuente #3); **ausente** en este dump |
