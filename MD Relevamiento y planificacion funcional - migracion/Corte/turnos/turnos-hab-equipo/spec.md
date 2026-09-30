---
title: Spec — Hab. turnos por equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-hab-equipo.spec
---

# Spec — Hab. turnos por equipo

Semilla: `habTurnosEquipoServ` (menú `hab_turnos_equipo` id **10813** / **15413**).  
Un hop: `buscadorEquipoServCentro.xhtml`. Tronco `BBSessionData` solo por sesión.

## Clarify — **FIRME** (Francisco, 2026-09-22)

| # | Pregunta | Respuesta propuesta | Evidencia |
|---|----------|---------------------|-----------|
| 1 | ¿Pipeline? | T2 serv+pers **gate-done**. Este corte es el ABM equipo que T2 dejó en DDL+check (D-TUR-12). El vínculo `equipo_serv_centro` no lo crea este corte. | spec T2 · V56 |
| 2 | ¿Happy path? | Buscar equipo → lista de vigencias → Agregar → Aceptar → fila en `hab_turnos_equipo_serv`. Editar y eliminar en la misma hoja. | xhtml + `BBHabTurnosEquipoServ` |
| 3 | ¿Ciclo de vida? | Vigencia desde/hasta. `vigente` lo recalcula el check de T2 (no este popup). Borrado físico de la fila, con confirm. | bean `actBtnEliminar` · `JdbcTurnosHabAdapter` |
| 4 | ¿Errores / acceso? | `Debe seleccionar un servicio centro.` / `Debe ingresar la fecha de vigencia.` / `La fecha de fin de vigencia debe ser posterior a la fecha de vigencia`. Rangos JSF ≥ 0 y porcentaje 0–100. Menú TURNOS configuración (misma rama que hab serv/pers). Rol funcional en el bean: **ninguno**. Actor sin la entrada de menú no entra. | MessageBundle · bean |
| 5 | ¿Side-effects? | Toast alta/edición/baja. Sin mail, sin PDF, sin job nuevo. | bean |
| 6 | ¿Fuera? | Vínculo equipo-centro, horarios equipo, generar/eliminar en modo equipo, combo Equipo de Agenda/historial/suspender/cola, ítem equipo, jobs de laboratorio. | slugs abajo |
| 7 | ¿Paridad UI? | Gate de arranque hecho en los inventarios de este slug. Template Angular **después** de la firma. Ruta prevista `/configuracion/hab-turnos-equipo`, menú bajo TURNOS (como serv/pers). | inventarios |
| 8 | ¿Viaje Playwright? | Padre `EQDEMO1001` cargado. El script automático sigue **diferido(fixture)** hasta correr el viaje. | verify |

### Decisiones de este tramo

| Id | Decisión |
|----|----------|
| D-TUR-12 | Se cobra acá: ABM de `hab_turnos_equipo_serv`. |
| D-TUR-13 | Horarios `turnosEquipo/*` siguen fuera. |
| D-TUR-17 | El modo equipo de generar/filtrar sigue fuera. Este slug es solo la hab. |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| Listar vigencias del equipo elegido | `habTurnosEquipoServ.xhtml` · `initListaHabTurnos` | **in scope** |
| Alta / edición popup | `actionBtnAgregar` / `actBtnModificar` / `actionBtnAceptarPopup` | **in scope** |
| Baja con confirm | `actBtnEliminar` · `desea_eliminar_entidad` | **in scope** |
| Buscador equipo-servicio-centro | `buscadorEquipoServCentro.xhtml` | **in scope** (lee; no inserta) |
| Check `vigente` | `f_check_hab_turnos` | **N/A** — ya en T2, incluye esta tabla |
| Sync `atiende_turnos` en `equipo_serv_centro` | mismo package | **puente** — [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md) |
| Alta del vínculo equipo-centro | `datosEquipoServCentro.xhtml` | **diferido(fixture)** [`turnos-equipo-serv-centro`](../turnos-equipo-serv-centro/) |
| Grupo / horario / días equipo | `turnosEquipo/*` | **diferido** D-TUR-13 |
| Generar / eliminar / filtrar por equipo | radios y combos ya disabled | **diferido** D-TUR-17 |
| Jobs interface lab | `--jobs equipo` | **N/A** (laboratorio) |

## Firmas PL/SQL

El índice no ve firmas en esta semilla. El acto es JDBC sobre `hab_turnos_equipo_serv`.  
`TS.TURNOS.f_check_hab_turnos`: **puente ya anotado** (sync `atiende_turnos`); el UPDATE de `vigente` quedó en T2. No se reabre.

## Presupuesto no funcional

| Eje | Presupuesto |
|-----|-------------|
| Tiempo | p95 del listar + aceptar, a medir en el paso 6 |
| Volumen | **medido en vacío** (`equipo_serv_centro` = 0 filas) + `diferido(perf-volumen)` |
| Concurrencia | Recurso = fila de hab (centro + servicio + equipo + fecha vigencia). El bean no usa `FOR UPDATE`. El control es la PK/UK del dump; dos altas de la misma vigencia se miden en el paso 6 |

## Instalación de referencia

Call Center Demo. Repo `cliente="TS"`: las ramas `esClienteX()` quedan apagadas. Esta hoja no tiene ninguna.

## Acceso

Perfil: quien ve el menú TURNOS → configuración → hab turnos equipo (10813 / 15413), el mismo padre que hab servicio y hab profesional.  
Rol funcional PL/SQL: ninguno en este bean.  
Prueba negativa: actor sin esa entrada de menú.
