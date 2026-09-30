---
title: Verify — T6.2 · cola Reasignación de Turnos
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar.verify
---

# Verify — Cola Reasignación de Turnos

**Gate:** **PASS / gate-done** 2026-09-17 · Clarify **FIRME** Camino 1 D-TUR-64.  
G6 smoke ops **PASS** (Francisco: «ok ahora pude probar el flujo»; obs «listo probado funciona»).  
**No** cierra T6 padre, T6.1 ni D-TUR-17.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Accordion + consultar cola + IrAGrilla + obs | **done** | G6 + PATCH `302675` |
| Imprimir south | **done** | hijo [`turnos-agenda-cola-reasignar-print/`](../turnos-agenda-cola-reasignar-print/) **gate-done** 2026-09-17 |
| Excel south | **done** | hijo [`turnos-agenda-cola-reasignar-export/`](../turnos-agenda-cola-reasignar-export/) **gate-done** 2026-09-17 |
| Equipo north | **done** | [`turnos-agenda-cola-equipo`](../turnos-agenda-cola-equipo/) 2026-09-28 |
| Historial | **diferido** | `turnos-ciclo-vida` |
| Avisos | **WAIVE** | HIS `disabled=true` |
| T6.1 fila | **fuera** | hermano T6.1 |
| Jobs | **N/A** | alta = T4; vencidos = otro circuito |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Accordion live + consultar + gear → Agenda | sí | **e2e-migrado** | CI stub; G6 fila T4 |
| Legacy HIS | — | **no** | — |

## Paridad UI (xhtml)

No hay xhtml nuevo. Chrome Agenda; este corte **habilita** la hoja accordion.

| Eje | Estado | Nota |
|-----|--------|------|
| geometría | **inventario G0** | [inventario-geometria.md](inventario-geometria.md) |
| copy | **inventario G0** | [inventario-copy-msg.md](inventario-copy-msg.md) |
| validaciones | **inventario G0** | [inventario-validaciones.md](inventario-validaciones.md) |
| interacción | **inventario G0** | [inventario-interaccion-ui.md](inventario-interaccion-ui.md) |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | GET lista cola 200 (o vacío copy HIS) | endpoint | `GET /api/v1/turnos/agenda/cola-reasignar?fechaDesde=2026-09-17&fechaHasta=2026-09-17&horaDesde=00:00&horaHasta=23:59&idCallCenter=92002` · 200 · `idTurnoAReasignar=302674` | dump 2026-09-17 tras T4 | **verificado** |
| 2 | PATCH obs: id de fila `ts.turno_a_reasignar` + query | escritura | id `302675` · `SELECT observaciones FROM ts.turno_a_reasignar WHERE id_turno_a_reasignar=302675` → `prueba de reasignar` · `actualizado_por=admin` · `fecha_last_update=2026-09-17 15:54:38` · CU ícono obs + Aceptar | Francisco 2026-09-17 «listo probado funciona» | **verificado** |
| 3 | Tests handler vacío / intervalo / obs | test | `mvn -pl core,application -am -Dtest=ColaReasignarValidationTest,GetColaReasignarQueryHandlerTest,UpdateObservacionesColaCommandHandlerTest test` · 6 tests | Hospital-Api 2026-09-16 | **verificado** |
| 4 | e2e accordion + consultar + IrAGrilla | e2e | `npx playwright test e2e/turnos-agenda.spec.ts -g "Cola reasignar T6.2"` · 4 passed | Hospital-Web 2026-09-16 | **verificado** |
| 5 | G6 ops ve cola de T4 (no seed) | e2e | Francisco `/turnos/agenda` accordion Reasignación → Consultar fila T4 `302674` → REASIGNAR TURNO → Asignar/Otorgar | 2026-09-17 «ok ahora pude probar el flujo» | **verificado** |
| 6 | Acceso: perfil menú Agenda; rol funcional N/A (bean sin Raise); prueba negativa actor sin el rol = no entra `/turnos/agenda` | e2e | actor G6 con perfil; «sin el rol» = padre T5 misma ruta (no reabrir) | T5 verify `/turnos/agenda` | **verificado** |
| 7 | Volumen: filas UI = COUNT cola del filtro | e2e | COUNT=1 (`302674`) al Consultar; COUNT=0 tras otorgar (DELETE cola) | dump `grupogea-hospital_dev` 2026-09-17 | **verificado** |
| 8 | Concurrencia: dos UPDATE obs misma PK last-write-wins; PATCH fail rollback | test | dos PATCH paralelos `302675` `actor-a` / `actor-b` → gana `actor-b`; PATCH id `999999999` → 404 `No se pudo recuperar el turno nro. 999999999.` y fila `302675` intacta (`prueba de reasignar`) | dump 2026-09-17 15:56 | **verificado** |

## Smoke

**G6 stack real — PASS 2026-09-17** (ops Francisco, `/turnos/agenda` accordion Reasignación):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | T4 eliminar grilla con paciente → fila `ts.turno_a_reasignar` `302674` (17/09 08:00, centro 1001, servicio 10) | **PASS** (CU, no seed) |
| 2 | Consultar cola (call center sesión + fechas 17/09) → 1 fila | **PASS** |
| 3 | Gear REASIGNAR TURNO → Agenda north hidratado, sin overlay `PENDIENTE_LIBERAR` | **PASS** |
| 4 | Asignar turno LIBRE + Otorgar → DELETE cola | **PASS** · COUNT cola=0 · destino `ts.turno` OTORGADO paciente 20001 el 17/09 |
| 5 | Ícono observaciones → Aceptar `prueba de reasignar` | **PASS** · fila `302675` |

Habilitación: `ts.serv_centro_call_center` 92001/92002 × 1001/10 (vacío en dump; GET con call center filtraba la fila T4).

## Resultado

**PASS / gate-done 2026-09-17.** Diferidos: Imprimir south · Excel south · T6 padre. Equipo en [`turnos-agenda-cola-equipo`](../turnos-agenda-cola-equipo/).
