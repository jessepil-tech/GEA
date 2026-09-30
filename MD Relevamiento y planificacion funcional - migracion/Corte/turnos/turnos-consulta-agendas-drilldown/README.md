---
title: SDD — T4 hijo · drill-down consulta agendas
description: >-
  Panel sur calendario + grilla de turnos al click en mes S de Consulta Agendas
  Generadas. Imprimir BIRT en hijo [`turnos-consulta-agendas-imprimir`](../turnos-consulta-agendas-imprimir/).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-04
phase_id: sdd.hospital.turnos-consulta-agendas-drilldown
---

# T4 hijo — Drill-down consulta agendas (`turnos-consulta-agendas-drilldown`)

**Padre:** [`turnos-generacion-grilla/`](../turnos-generacion-grilla/) (matriz S/N G5 done).  
**Hermano:** [`turnos-consulta-agendas-imprimir/`](../turnos-consulta-agendas-imprimir/) (PDF BIRT).  
**Motivo:** sin el south de legacy la página queda incompleta (opción A acordada 2026-09-02).

## Alcance

| In | Out |
|----|-----|
| Click mes = `S` → calendario del mes + estados día | Imprimir BIRT (hijo [`turnos-consulta-agendas-imprimir`](../turnos-consulta-agendas-imprimir/)) |
| Click día → tabla turnos (cols xhtml) | Filtro equipo (D-TUR-17) |
| Leyendas calendario + footer estados turno | Navegación mes anterior/siguiente (nice-to-have si cabe) |
| API días-mes + turnos-día (JDBC, sin SP Oracle) | |

## Clarify (FIRME)

1. Geometría: south ~300px debajo de matriz; calendario ~202px + dataTable scroll.
2. Solo celdas `S` clickeables; `N` no dispara.
3. Estados día (paridad tokens SP): `DISPONIBLE`, `COMPLETO`, `SUSPENDIDO`, `FERIADO`, `SIN_TURNO`, `CON_TURNO` si aplica.
4. Estilo fila turno según estado/sobreturno (reservado, sobreturno, cancelado, reemplazado, inhibido).
5. Imprimir: ver hijo [`turnos-consulta-agendas-imprimir`](../turnos-consulta-agendas-imprimir/) (no es alcance de este slug).

## API

```
GET /api/v1/turnos/grilla/consulta-agendas/dias
  ?anio&mes&idGrpPrestTurServ|idGrpPrestTurPers
→ [{ fecha, estado, claseCss }]

GET /api/v1/turnos/grilla/consulta-agendas/turnos
  ?fecha&idGrpPrestTurServ|idGrpPrestTurPers
→ [{ fechaHoraTurIni, fechaHoraTurFin, duracionTurnoMtos, paciente, tipoDocPaciente,
     nroDocPaciente, centroAtencion, servicio, personalEquipo, convenio, planConvenio,
     estadoTurno, sobreturno, estilo }]
```

## Gate UI arranque

xhtml: `consultaAgendasGeneradas.xhtml` south size=300 + formMenuCalendar + tablaTurnos.  
Copy: labels ya en `grilla-turnos-labels.ts` (disponible, completo, feriado, turnos, …).

## Verify

- [x] Click S carga calendario del mes/grupo (API+UI cableados)
- [x] Click día carga turnos (API+UI cableados)
- [x] Leyendas visibles
- [x] Smoke manual en stack local (G6 padre) — **2026-09-04**
- [x] Padre verify: drill-down → done; imprimir → hijo gate-done

## Viaje Playwright

| Campo | Valor |
|-------|--------|
| ¿Pantalla / acto de usuario en Web? | sí |
| Decisión | **e2e-migrado** (cubierto por padre/consulta) |
| Viaje (pasos) | `Hospital-Web/e2e/grilla-turnos-consulta.spec.ts` |
| Fixture | mocks CI |
| Legacy e2e | no |
