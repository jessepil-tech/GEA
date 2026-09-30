---
title: Verify — T6.2 hijo · Excel cola Reasignación
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar-export.verify
---

# Verify — Excel cola Reasignación

**Gate:** **PASS / gate-done** 2026-09-17 · Clarify **FIRME** Camino 1 D-TUR-69.  
G6 visual **PASS** (Francisco: «se ve bien el excel y sale la info»; estilo «se ve bien ahora»).  
**No** cierra PDF (ya gate-done), D-TUR-17 ni T6 padre.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Cable GET + botón south | **done** | este hijo |
| `.xls` valores = grilla | **done** | G6 2026-09-17 |
| Cabecera columnas gris 25% (`XLSParser` `csTableHead`) | **done** | G6 2026-09-17 |
| Equipo usable | **diferido** | D-TUR-17 |
| PDF cola | **fuera** | print gate-done |

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| Stub blob `.xls` | sí | **e2e-migrado** | CI ≠ POI real |
| Valores reales | sí | **N/A** | G6 abrir archivo |
| Legacy HIS | — | **no** | — |

## Paridad UI (xhtml)

**N/A** xhtml nuevo.

| Eje | Estado | Nota |
|-----|--------|------|
| geometría | **N/A** | south 150px padre |
| copy | **N/A** | Exportar Excel padre |
| validaciones | **N/A** | mismas que Consultar |
| interacción | **N/A** | disparador south |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Ref | Verdicto |
|---|------------|-------|-----------|-----|----------|
| 1 | Handler 17 cols título Reasignación de Turnos | test | `mvn -pl application -am test -Dtest=GetColaReasignarExcelQueryHandlerTest` · exit 0 · lista vacía igual xls · header `<TODOS>` | Hospital-Api 2026-09-17 | **verificado** |
| 2 | GET `cola-reasignar/exportar.xls` | endpoint | sin token **401** | localhost:8081 2026-09-17 | **verificado** |
| 3 | e2e botón enabled + stub download | e2e | `npx playwright test e2e/turnos-agenda.spec.ts -g "Cola reasignar T6.2"` · 6 passed (Exportar Excel stub xls) | Hospital-Web 2026-09-17 | **verificado** |
| 4 | G6 Excel valores = grilla | test | Francisco abrió `.xls` | 2026-09-17 «se ve bien el excel y sale la info» | **verificado** |
| 5 | Acceso: mismo perfil menú Agenda que T6.2 | e2e | actor G6 con perfil; prueba negativa «sin el rol» = padre T5 | T6.2 verify | **verificado** |
| 6 | Volumen filas xls = cola dump | test | G6 1 fila = grilla dump | 2026-09-17 | **verificado** |
| 7 | NFR GET; concurrencia N/A (sin FOR UPDATE) | test | no escribe; G6 Excel salió en el click | G6 2026-09-17 | **verificado** |
| 8 | Cabecera columnas `GREY_25_PERCENT` | test | `mvn -pl infrastructure -am test -Dtest=PoiHssfExcelAdapterTest` · exit 0 · fill SOLID + gris 25% | Hospital-Api 2026-09-17 | **verificado** |
| 9 | G6 estilo cabecera = HIS | test | Francisco re-exportó `.xls` | 2026-09-17 «se ve bien ahora» | **verificado** |

## Resultado

**PASS / gate-done** 2026-09-17. Equipo usable = D-TUR-17. T6 padre sigue diferido.

Capacidad done con filas **no verificado** = **FAIL** si se declara PASS. Al tocar código del corte, filas 1–4 y 8–9 vuelven a no verificado.
