---
title: Inventario validaciones — T6.4 suspender grilla
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-grilla-suspender.validaciones
---

# Inventario validaciones — Suspender / quitar suspensión

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| Lista vacía al confirmar | ORA-20001 «algun turno a cancelar» | toast | 400 mismo texto |
| Motivo null | ORA-20001 / BB `DEBE_SELECCIONAR_UN_MOTIVO_DE_SUSPENSION` | toast | 400 |
| Parcial + paciente | ORA-20000 «No se puede suspender parcialmente un turno otorgado.» | toast | 400 |
| Equipo sin elegir | `EQUIPO_REQUIRED_ERROR` | N/A D-TUR-17 | — |
| Call center | sesión T1/T5 | sesión | gate T5 |
| Actor sin menú | no entra | no ruta | 401/403 sin `/500` |

No hay `Raise_application_error` de rol funcional en estos beans.
