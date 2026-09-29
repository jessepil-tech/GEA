---
title: SDD — Llamar fila Cola B (serv_amb → TV)
status: gate-done
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cola-b-llamar
---
# SDD — Llamar Cola B (`cola-b-llamar`)

`phase_id:` **`sdd.hospital.cola-b-llamar`**  
Canon: [`loop-migracion-corte.md`](../../../canon/loop-migracion-corte.md).  
**gate-done** 2026-09-16 (paso 8). Clarify B; GM **PASS**; NFR medido; acceso
`diferido(acceso)`. Backlog **#6b** (no el menú M5 ni el gate centro/box).
Enganche: [`e2e-mostrador/`](../e2e-mostrador/) (TV del `OTORGADO` autorizado).

Capa 3: Recepción slices cola
[`paridad-recepcion-cola/`](../paridad-recepcion-cola/) (M4 read **done**, M5 menú
**fuera**) · Anunciador
[`ciclo-vida-llamado-anunciador/`](../../anunciador/ciclo-vida-llamado-anunciador/) ·
hermano clínico [`cu-llamar-atencion-medica/`](../cu-llamar-atencion-medica/).

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Problema, universo firmado, Clarify |
| [plan.md](plan.md) | Cortes C0–C4 |
| [tasks.md](tasks.md) | Checklist |
| [verify-report.md](verify-report.md) | Ledger (C1–C4 + GM + acceso + NFR) |

**No es:** menú M5 de `colaEspera.xhtml` (CI, reemplazar, derivar, anular, print,
etiqueta) · Cola A `btnLlamar` (CU-B.1 gate-done) · gate centro/puesto · P3 C5 ·
T6 · Lab · clones GYE/`colaEsperaImagen`.

## Estado del corte

| Campo | Valor |
|-------|-------|
| Paso del loop | **8** (cerrado) |
| Reservas | liberadas |
| Universo firmado | [spec.md](spec.md) · Clarify **B** |
| Fixture | resuelto (id=5 vigente; id=6 → 26) |
| Evidencia | 11 filas **verificado** ([verify-report.md](verify-report.md)) |
| Diferidos abiertos | `diferido(acceso)` Identity · M5 menú Cola B · jobs (`relevamiento-procesos-programados`) · `diferido(multi-instalacion)` |
| Próximo paso | ninguno en este corte |

**gate-done** 2026-09-16. Semilla HIS del **acto** = `listaEsperaAtencionMedica.xhtml`
→ `actionBtnLlamarPacienteColaEsperaPersonal` (la grilla Cola B **no** tiene
Llamar). Índice de `colaEspera.xhtml` recepción: xhtml 4/8 ok · beans 12/15 ok ·
reportes 7/5 **EXCEDE** (todos `diferido` salvo el acto Llamar).

API `POST /api/v1/recepcion/espera-amb/{id}/llamar`. E2E live: `turno=5 colaB=5009`
llamado id=**5**. NFR p95 Cola B **7,3 ms** ≤ B.1 **59,9 ms** (n=3). Acceso: IT JWT
`user_role` **404**. GM **PASS**. Re-llamar 5010 reemplazó llamado id=6. Sin botón
en espera-amb.

Instalación de referencia: **genérica (`cliente="TS"`)**. `diferido(multi-instalacion)`.

Jobs: `LlamadorAnunciadorJob` · `MigrarTurnoVencidoJob` (purga cola) →
`diferido(relevamiento-procesos-programados)`. `AtencionAutomaticaColaEsperaAmbJob`
→ **N/A** este corte (genera atención, no el llamado). Premisa: prod encendido
hasta confirmación.

## Reservas

| Recurso | Valor | Estado |
|---------|-------|--------|
| Stream | Recepción (circuito ya migrado) + satélite TV | **liberado** |
| BODY / package | **no se porta** RECEPCIONES ni ATENCION. Lógica en app (mismo patrón que G1/B.1) | n/a — no segundo owner de package |
| Rango Flyway | ninguno nuevo (columnas `llamado_anunciador.id_cola_espera_serv_amb` ya en V28) | n/a |
| Tablas `ts` que escribe | `llamado_anunciador` (INSERT path serv_amb) · no `ts.turno` | **liberado** (path Cola B cobrado; Cola A intacta) |
| Rama | `dev/dev` | — |

No pisa BODY TURNOS (reservado remoto). No abre M5. El hermano
`cu-llamar-atencion-medica` **consume** este writer para su UI clínica (E3);
E1–E2 del puerto viven **aquí**.

## Fixture

| Padre / dato | ¿Lo crea este corte? | Origen | Estado |
|--------------|----------------------|--------|--------|
| `ts.cola_espera_serv_amb` id=**5008** (turno 4) y **5009** (turno 5, recep_amb 9) | no (5008) / origen e2e (5009) | viaje `e2e-mostrador` | **vigente** — no borrar |
| `ts.llamado_anunciador` id=**5** (`ATENCION_MEDICA`, cola 5009, ANU-DEMO 1001) | **sí** | POST Llamar este corte | **vigente** — no borrar |
| `ts.llamado_anunciador` id=**6** (cola 5010) | sí (e2e E3) | NFR re-llamar 5010 | **reemplazado** 2026-09-16 por id=**26** (delete previos mismo serv_amb) |
| Paciente `20001` · centro `1001` · servicio `10` | no | seed / T5+AGI | **vigente** |
| `ts.anunciador` `1001` `ANU-DEMO` | no | default Api | **vigente** |
| Vínculo `anunciador_ambiente_amb` del ambiente del llamado | no | P3 / seed | si falta → `diferido(fixture)` de la pata TV (HIS también oculta Llamar) |
| `OTORGADO`/`RECEPCIONADO` | no | T5 + G1 | **id=4 y id=5** vigentes |
| Filas menú M5 / reportes de cola | — | — | **fuera** |

## Universo firmado

Semilla del **acto** (no la carpeta recepción):

```text
listaEsperaAtencionMedica.xhtml
  → BBListaEsperaAtencionMedica.actionBtnLlamarPacienteColaEsperaPersonal
  → Anunciadores.insertAnunciarPacienteConCola
  → ATENCION.f_set_paciente_anun_cola
  → ANUNCIADORES.p_insert_llamado_anunciador (id_cola_espera_serv_amb)
```

Cola B `colaEspera.xhtml` / `BBConsultaColaEsperaRecepcion`: **candidatos del
menú M5**, todos `diferido` (otro corte). El poll a `:frmCabecera:btnLlamar` es
Cola A, no este CU.

Techo: no se firma la cadena EXCEDE de reportes ni GYE.
