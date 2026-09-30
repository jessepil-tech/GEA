---
title: Inventario DDL gaps — T5.1e infoTurno
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno.ddl
---

# Inventario DDL gaps

| Objeto | ¿En Flyway? | ¿Bloquea v1? | Acción |
|--------|-------------|--------------|--------|
| `ts.turno.observaciones` | Sí V31 | No chrome | persist **gate-done** [`turnos-agenda-info-turno-persist/`](../turnos-agenda-info-turno-persist/) |
| `ts.turno.fecha_prescripcion` | Sí V31 | No chrome | mismo slug |
| Ficha tipo paciente | No en `FichaPacienteAgenda` | No | input vacío |
| Preparación / req realización | Sí V52 + GET | Sí chrome | hijo [`turnos-agenda-info-turno-prest/`](../turnos-agenda-info-turno-prest/) **gate-done** |
| `ts.doc_req_*` | Sí T5.1c | No | reusa `GET …/doc-req` |

G1: **no** ALTER.
