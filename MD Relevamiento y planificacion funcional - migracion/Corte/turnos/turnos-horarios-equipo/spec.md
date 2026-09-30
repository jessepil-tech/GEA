---
title: Spec — Turnos por equipo (D-TUR-13)
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-horarios-equipo.spec
---

# Spec — Turnos por equipo

Padre: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/).  
Espejo: T3 [`turnos-horarios-grupos/`](../turnos-horarios-grupos/) (pers + serv, gate-done).  
Hab previa: [`turnos-hab-equipo/`](../turnos-hab-equipo/) gate-done.  
Menú legacy: `turnos_por_equipo` id **10816** / **15416**, acción `turnosEquipo`.

## Problema

Sin grupo, prestaciones y horario del equipo no hay oferta en modo equipo. T3 dejó esas tablas en el dump y la pantalla afuera (D-TUR-13).

## Clarify — **FIRME** (ealbo, 2026-09-23, «ok firme»)

| # | Pregunta | Respuesta propuesta | Evidencia |
|---|----------|---------------------|-----------|
| 1 | ¿Pipeline? | T3 gate-done. Hab equipo gate-done. Este corte escribe la cadena equipo. | spec T3 · D-TUR-13 |
| 2 | ¿Qué hojas? | Cascarón, datos (solo lectura), grupo, prestaciones, horario con días. | menú de `turnosEquipo.xhtml` |
| 3 | ¿Qué queda afuera? | Horario especial, inhibición, ocupación convenio/plan, generar grilla. La reserva plan-conv del día entra con el lápiz. | ocupación sigue afuera; el lápiz se cobró en este corte |
| 4 | ¿Paquete TURNOS? | No se porta. Los `Turnos.insert*` del bean son CRUD. JDBC, como T3. | índice 0 firmas |
| 5 | ¿Copiar vigencia? | Entra con el horario nuevo: el botón nueva vigencia puede copiar la anterior. | flag `copiar` del bean de horario |
| 6 | ¿UI? | Menú TURNOS → Dominios → Configuración, hoja «Turnos por Equipo», ruta `/configuracion/horarios-turnos-equipo`. | igual que serv/pers |
| 7 | ¿Paridad UI? | Gate de arranque antes del template. Inventarios de este slug, lectura inicial hecha; se completan al firmar. | regla-paridad-ui |
| 8 | ¿Viaje? | e2e-migrado cuando haya pantalla. Fixture nombrado `EQDEMO1001`. | regla-playwright |

**Firma.** ealbo, 2026-09-23, «ok firme».

## Universo propuesto

| Capacidad | Evidencia | Este corte |
|-----------|-----------|------------|
| Cascarón + buscador equipo | `turnosEquipo.xhtml` · `buscadorEquipoServCentro.xhtml` | se porta. El buscador ya está en la hab |
| Datos del equipo | `datosTurnosEquipo.xhtml` | se porta, solo lectura |
| Grupo | `grupoPrestacionesTurnoEquipo.xhtml` · `grp_prest_tur_equipo` | se porta |
| Prestaciones del grupo | `prestacionGrupoPrestacionesTurnoEquipo.xhtml` · `prest_grp_prest_tur_equipo` | se porta |
| Horario + días | `horarioTurnoEquipo.xhtml` · `horario_tur_grp_equipo` · `dia_horario_tur_grp_equipo` | se porta, incluida copiar vigencia |
| Horario especial | `horarioEspecialTurnoEquipo.xhtml` | **diferido** [`turnos-horarios-especiales/`](../turnos-horarios-especiales/) |
| Inhibición | `inhibicionTurnoEquipo.xhtml` | **diferido** [`turnos-horarios-inhibiciones/`](../turnos-horarios-inhibiciones/) |
| Ocupación conv/plan | `ocupacionTurEquipo*.xhtml` | **diferido** (T3 ya lo dejó afuera; sin slug propio de equipo) |
| Reserva plan-conv del día | lápiz de `horarioTurnoEquipo.xhtml` · `reserva_tur_equipo_plan_conv` | **se porta** en este corte |
| Generar grilla modo equipo | `f_gen_grilla_turnos` | **diferido** D-TUR-17 |
| Jobs laboratorio / ANMAT | `--jobs equipo` | **N/A** |
| Auditoría `aud_*` | dump, cuatro tablas `aud_` de esta cadena | **diferido(auditoria)** |
| Volumen | sin dataset del orden del legacy | **diferido(perf-volumen)** |

Techo del universo propuesto: 6 xhtml (cascarón, buscador, datos, grupo, prestaciones, horario) / 8. Beans: `BBTurnosEquipo`, `BBBuscadorEquipoServCentro`, `BBGrupoPrestacionesTurnoEquipo`, `BBPrestacionGrupoPrestacionesTurnoEquipo`, `BBHorarioTurnoEquipo` → 5 / 15.

## Acceso y no funcional

Perfil de menú: quien ve TURNOS → configuración (mismo padre que hab y que turnos por servicio). Hoja **15416** / **10816**. Los beans no consultan `rol_funcional_pers`.  
La prueba negativa es un actor sin `ATENCION_TURNOS`.

Recurso disputado: la vigencia del horario (y el id del grupo). El bean no usa `FOR UPDATE`. El control es la PK del dump. Dos altas de la misma vigencia se miden en el paso 6. Tiempo: p95 de listar el grupo y aceptar un horario. Volumen diferido.

## No objetivos

| Ítem | Destino |
|------|---------|
| Vínculo `equipo_serv_centro` | [`turnos-equipo-serv-centro/`](../turnos-equipo-serv-centro/) |
| Hab de equipo | ya gate-done |
| Combo Equipo de Agenda y generar grilla | D-TUR-17 |
