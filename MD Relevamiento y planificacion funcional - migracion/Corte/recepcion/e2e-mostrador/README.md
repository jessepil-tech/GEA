---
title: SDD — E2E mostrador (turno nacido en PG)
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.e2e-mostrador
---
# SDD — E2E mostrador (`e2e-mostrador`)

`phase_id:` **`sdd.hospital.e2e-mostrador`**  
Canon: [`criterio-avance-e2e-datos.md`](../../../canon/criterio-avance-e2e-datos.md) Flujo 1.  
Backlog **#5**. No abre tile nuevo.

Capa 3: Turnos [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) (T0) ·
Anunciador [`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/) ·
Recepción **slices** cola (no A–C de módulo; este corte no lo abre).

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Circuito, Clarify, RF de escritura |
| [plan.md](plan.md) | Orden del viaje + fixture |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger + Viaje Playwright |

**No es:** gate centro/puesto (`paridad-recepcion-gate`) · Cola B menú · C5 ABM anunciador ·
T6 ciclo · jobs nocturnos · Lab/Internación.

## Estado del corte

**active — abierto** 2026-09-17. Viaje Playwright 2026-09-16 **1 passed** (turno 5 /
cola 5009 / llamado 5). 2026-09-17: Identity `:8080` y Api `:8081` UP; **401**
sin Bearer en T5/AGI/Llamar. NFR Llamar: 20× POST cola 5010 → **200**, p95 **6,0 ms**
(presupuesto ≤ 1,5 s). Dos POST `/reservar` sobre `id_turno=7` → 200 `RESERVADO` vs
400 ocupado; dos `/otorgar` → 200 `OTORGADO` vs 404. Volumen fixture →
`diferido(perf-volumen)`. JWT sin rol de menú = `diferido(acceso)`. Writer:
[`cola-b-llamar/`](../cola-b-llamar/).

Instalación de referencia: **genérica (`cliente="TS"`)**. `diferido(multi-instalacion)`.

Jobs (mismo día no los necesita; overnight sí): `MigrarTurnoVencidoJob` ·
`CheckHabTurnosJob` · `LlamadorAnunciadorJob` →
`diferido(relevamiento-procesos-programados)`. Premisa: HOSPROD los tiene `ACTIVA=N`
porque no es prod; **prod se asume encendido** hasta confirmación.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Recepción + satélite AGI/Anunciador (circuito ya migrado) | este workspace |
| BODY / package | **no se porta** TURNOS ni RECEPCIONES. Se **consumen** CUs gate-done | no segundo owner de package |
| Rango Flyway | ninguno nuevo | n/a |
| Tablas `ts` que escribe | las de T5 (otorgar) + cola + llamado — vía API/UI ya cobradas | no DDL |
| Rama | `dev/dev` (Web e2e + Api ya en origin) | — |

El BODY TURNOS sigue `reservado` para **portar** package (owner remoto / T6). Este
corte no abre firmas nuevas de `TURNOS_BODY`.

## Fixture

Medido en `hospital_api` (postgresql_01) **2026-09-16**. T2/T3 Flyway no
insertó (el centro 1001 no existía al correr V37/V42). Este corte **arma**
hab/horario/grilla/`LIBRE` y terminal AG **vía CUs ya cobrados** en el spec
Playwright (`ensurePadresViaCus`), no con SQL.

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| Paciente `id=20001` DNI `30111222` | no | seed / V57 (antes V48 local) | **vigente** |
| Centro `1001` · servicio `10` · personal `90001` (`admin`) · call center `92001` | no | Flyway maestros | **vigente** |
| Hab T2 + horario T3 + grilla T4 `LIBRE` | **sí** (consume CUs T2/T3/T4) | API en el viaje | **se arma en F0 del spec** si faltan |
| `OTORGADO` | **sí** (T5 UI) | CU T5 | evidencia = `id` de `ts.turno` |
| Cola B (`cola_espera_serv_amb`) | **sí** (confirmación AGI / G1) | sobre el `OTORGADO` | **id=5008** (viaje previo) y **id=5009** (viaje 2026-09-16 Llamar) |
| Llamar / TV del mismo ticket | **sí** (consume `cola-b-llamar` API) | POST espera-amb/{id}/llamar | **llamado id=5** (cola 5009, ANU-DEMO 1001) |
| Recepción `2001` · puesto `3001` | no | dev-seed | **vigente** |
| Sala TV `id_anunciador=1001` (`ANU-DEMO`) | no | default Api | **vigente** (P3 `id=3` no está vinculado al TV de este viaje) |
| Terminal AG | **sí** si no hay fila (CU C2) | API | **se arma en F0 del spec** |
| Seed IT `ANU-DEMO` / DNI 30111222 **sin** T5 | no | fuente #3 | **no** cierra este corte |

Si un padre falta y este corte no lo crea → el viaje de esa pata queda
`diferido(fixture)` **ahora**, no en el click.

## Universo firmado

No es un port de xhtml. Semilla = **viaje** de [`criterio-avance-e2e-datos.md`](../../../canon/criterio-avance-e2e-datos.md):

```text
Identity → (AGI | Recepción) → OTORGADO (T5 en PG) → confirmar/recepcionar
  → cola → Llamar → TV → ticket (si el tramo ya está cobrado)
```

Pantallas: las ya migradas (`/turnos/agenda`, `/agi/recepcion`, `/recepcion/cola`,
display). Techo de índice **no aplica** como EXCEDE de módulo: no se firma carpeta
`pages/`. Jobs del circuito: decisión arriba (diferido hasta prod).
