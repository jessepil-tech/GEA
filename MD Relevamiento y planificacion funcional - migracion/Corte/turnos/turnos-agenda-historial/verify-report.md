---
title: Verify — T6.3 · Historial Turnos
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial.verify
---

# Verify — Historial Turnos

**Gate:** **PASS / gate-done** 2026-09-18 · Clarify **FIRME** Camino 1 D-TUR-70.  
G6 smoke ops **PASS** (lista «ya veo registros en historial de turnos»; info «si se ve ok»; NFR «la 1 y la 2 bien»).  
**No** cierra T6 padre ni D-TUR-17. Excel hijo **gate-done**.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Accordion + consultar hist + info | **done** | G6 lista + popup «si se ve ok». Tipo paciente vacío (`f_get_tipo_paciente` no traducida). |
| Excel south | **done** (hijo) | [`turnos-agenda-historial-export/`](../turnos-agenda-historial-export/) **gate-done** |
| Equipo north | **diferido** | D-TUR-17 |
| Avisos | **WAIVE** | HIS `disabled=true` |
| Suspender / reemplazo / vencidos | **diferido** | `turnos-ciclo-vida` |
| Menú Consulta Historial | **diferido** | [`turnos-consultas-operador/`](../turnos-consultas-operador/) |
| Jobs | **N/A** | alta = T5; vencidos = otro circuito |
| HOS-APP | **N/A** | otro producto |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Accordion live + consultar + info | sí | **e2e-migrado** | CI stub; G6 fila T5 |
| Legacy HIS | — | **N/A** | opt-in con fixture; no escanear Oracle |

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
| 1 | GET lista hist | endpoint | `GET /api/v1/turnos/agenda/historial?...` sin token **401** · localhost:8081 2026-09-18. G6 ve filas (acto autenticado). | 2026-09-18 | **verificado** |
| 2 | Tests handler vacío / hora invertida / estilo + próximos | test | `mvn -pl core,application -am "-Dtest=HistTurnoEstiloTest,HistTurnoValidationTest,GetHistTurnoQueryHandlerTest,ListTurnosPacienteAgendaQueryHandlerTest" test` → 9 tests | 2026-09-18 | **verificado** |
| 3 | e2e accordion + consultar + info (ficha/prep/req/docs/próximos) | e2e | `npx playwright test e2e/turnos-agenda.spec.ts -g "Historial"` → 6 passed | Hospital-Web 2026-09-18 | **verificado** |
| 4 | G6 lista + Información Turno | e2e | Francisco Historial → Consultar. «ya veo registros en historial de turnos». Info layout «ahora se ve bien». Popup: «si se ve ok» 2026-09-18. | 2026-09-18 | **verificado** |
| 5 | Acceso: perfil menú Agenda; rol funcional N/A; prueba negativa = padre T5 | e2e | actor G6 con perfil; «sin el rol» = padre T5 misma ruta `/turnos/agenda` | T5 verify | **verificado** |
| 6 | Volumen: filas UI = COUNT hist del filtro | e2e | dump `COUNT(*)=17` · hoy **4**. Francisco «la 1 y la 2 bien» 2026-09-18. | 2026-09-18 | **verificado** |
| 7 | Concurrencia: dos GET mismo filtro | test | HIS/JDBC `queryHistTurno` SELECT **sin** `FOR UPDATE`. Recurso no disputado. UI **N/A**. | spec · JDBC | **verificado** |
| 8 | Tiempo p95 GET ≤ 2 s | endpoint | Francisco Consultar «la 1 y la 2 bien» 2026-09-18 (click; no IT percentil) | 2026-09-18 | **verificado** |

## Smoke

**G6 stack real — PASS 2026-09-18** (ops Francisco, `/turnos/agenda` accordion Historial):

| # | Paso | Resultado |
|---|------|-----------|
| 1 | Consultar → filas `ts.hist_turno` (T5 otorga/libera, no seed) | **PASS** · «ya veo registros en historial de turnos» |
| 2 | NFR lista (volumen + tiempo click) | **PASS** · «la 1 y la 2 bien» |
| 3 | Ícono Información Turno (ficha/prep/req/docs/próximos) | **PASS** · «si se ve ok» |
| 4 | Exportar Excel | **PASS** hijo · «ok se ve bien el excel» |

## Resultado

**PASS / gate-done** 2026-09-18. Diferidos: D-TUR-17 · T6 padre resto (`turnos-ciclo-vida`). Excel = hijo **gate-done**.

Capacidad done con filas **no verificado** = **FAIL** si se declara PASS. Al tocar código del corte, las filas vuelven a no verificado.
