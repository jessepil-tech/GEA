---
title: Verify — Hab. turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-hab-equipo.verify
---

# Verify — Hab. turnos por equipo

Clarify **FIRME** 2026-09-22. Padre del buscador: `EQDEMO1001` en centro 1001 / servicio 10 (seed mock). La hab no se seedea.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Lista + popup de vigencia | **done** | ledger 3 · 4 · 9 · 10 |
| Buscador equipo-servicio-centro | **done** | ledger 8 · 9 |
| Padre vínculo | **diferido(fixture)** | [`turnos-equipo-serv-centro`](../turnos-equipo-serv-centro/) |
| Horarios equipo | **diferido** | D-TUR-13 |
| Generar / filtrar por equipo | **diferido** | D-TUR-17 |
| Check `vigente` | **N/A** | T2 |
| Sync `atiende_turnos` | **puente** | `pendientes-solo-oracle.md` |
| Jobs interface lab | **N/A** | otro dominio |
| Auditoría campo a campo | **diferido(auditoria)** | `fecha_last_update` es el último cambio |
| Volumen de listar | **diferido(perf-volumen)** | medido en vacío |

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** |
| Viaje (pasos) | Buscar → diálogo → elegir EQUIPO DEMO → Agregar → Aceptar → la fila `2026-09-23` en la grilla |
| Fixture | mock `EQDEMO1001` en `e2e/hab-turnos-equipo.spec.ts`; padre PG real para el smoke |
| Legacy e2e | no |

## Ledger de evidencia

| # | Afirmación | Clase | Artefacto | Verdicto |
|---|------------|-------|-----------|----------|
| 1 | Índice semilla dentro del techo | build | `indice-legacy.sh --semilla habTurnosEquipoServ` → 3/8 xhtml, 2/15 beans, 0 firmas, 0 reportes | **verificado** |
| 2 | Jobs equipo fuera de este circuito | build | `--jobs equipo` → 5 jobs laboratorio/ANMAT | **verificado** |
| 2b | Mensajes de vigencia, entero y porcentaje | test | `mvn -pl application -am test -Dtest=HabEquipoRulesTest` → 6 tests, 0 failures (2026-09-23) | **verificado** |
| 3 | Fila de hab nacida del CU | escritura | id=1001 / servicio 10 / EQDEMO1001 / 2026-09-23 · vigente S · actualizado_por admin. `SELECT` en `ts.hab_turnos_equipo_serv` sigue en 1 fila | **verificado** |
| 4 | Listar responde | endpoint | `GET /api/v1/turnos/hab/equipo?idCentroAte=1001&idServicio=10&codItemEquipo=EQDEMO1001` → **200** (618 bytes, trae EQDEMO1001 y 2026-09-23). Sin Authorization → **401** | **verificado** |
| 5 | Actor sin la entrada de menú | acceso | perfil menú TURNOS `ATENCION_TURNOS` hoja 15413; rol funcional ninguno. `RECEPCION` + `showAll=false` oculta TURNOS y `nav-hab-turnos-equipo`. Con `ATENCION_TURNOS` la hoja está. Filtro ejecutado exit 0. `ng test` del repo sigue bloqueado por jasmine ajeno | **verificado** |
| 6 | p95 de listar | no funcional | n=20 · p95=313,7 ms · min 53,9 · max 441,7. Un POST de vigencia nueva 1120 ms. Volumen **diferido(perf-volumen)** | **verificado** |
| 7 | Dos vigencias iguales | no funcional | POST de la misma PK → **409**. Dos POST paralelos de 2099-01-15 → **201** y **500** (`pk_hab_turnos_equipo_serv`). Quedó una fila; el probe se borró con DELETE **204**. La fila id=1001 sigue | **verificado** |
| 8 | Padre del buscador | consulta | `EQDEMO1001` · HOSPITAL-DEMO · CLINICA MEDICA · `atiende_turnos=N` | **verificado** |
| 9 | Viaje Buscar → Agregar | e2e | `npx playwright test e2e/hab-turnos-equipo.spec.ts` contra `:4200` → 1 passed (2,4 s) | **verificado** |
| 10 | Smoke del operador | smoke | Francisco, 2026-09-23: «ok ahi estaria probado la alta edicion y elimacion» | **verificado** |

Geometría, copy, validaciones e interacción: inventarios de este slug.
