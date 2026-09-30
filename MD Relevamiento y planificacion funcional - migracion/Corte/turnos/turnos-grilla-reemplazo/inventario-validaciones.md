---
title: Inventario validaciones — T6.5 reemplazo profesional
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-21
phase_id: sdd.hospital.turnos-grilla-reemplazo.validaciones
---

# Inventario validaciones — Reemplazo profesional

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| Profesional origen vacío (consultar) | `REQUIRED_PERSONAL` | toast WARN | 400 |
| Fecha hasta < desde | `WRONG_INTERVAL_DATE_3` | toast INFO | 400 |
| Mismo día y hora hasta < desde | `WRONG_INTERVAL_HOUR` | toast INFO | 400 |
| Reemplazante vacío (reemplazar) | `DEBE_INGRESAR_PERSONAL_REEMPLAZANTE` | toast WARN | 400 |
| Motivo vacío (reemplazar) | `DEBE_INGRESAR_MOTIVO_REEMPLAZO` | toast WARN | 400 |
| Ninguna fila check | `DEBE_SELECCIONAR_AL_MENOS_UN_TURNO` | toast WARN | 400 |
| Solape reemplazante | ORA-20001 «Existen turnos solapados para este personal.» | toast | 400 mismo texto |
| Parcial + paciente | ORA-20000 «No se puede reemplazar parcialmente el personal de un turno otorgado.» | toast | 400 |
| Call center | sesión T1/T5 | sesión | gate T5 |
| Actor sin menú | no entra | no ruta | 401/403 sin `/500` |
| South sin lista | botones `disabled` si lista vacía/null | igual | — |

No hay `Raise_application_error` de rol funcional en este bean.
