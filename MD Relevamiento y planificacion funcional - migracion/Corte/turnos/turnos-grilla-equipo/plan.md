---
title: Plan — Grilla en modo equipo
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.turnos-grilla-equipo.plan
---

# Plan — Grilla en modo equipo

Sin código hasta la firma del spec.

| Gate | Qué |
|------|-----|
| G0 | Inventarios de este slug (radio Equipo sobre la UI ya migrada) |
| G1 | Api: el comando de generar/eliminar/consultar acepta el equipo y lee el horario de D-TUR-13 |
| G2 | Web: encender el radio y el buscador en las tres hojas. El de serv/pers no se toca de más |
| G3 | Viaje Playwright de generar y de consultar por equipo |
| G4 | Ledger: fila nacida del Generar, con id |
| G5 | p95, volumen en vacío, dos Generar solapados |
| G6 | Smoke del operador |

Rama `dev/tur-grilla-equipo` al firmar. BODY no se reserva. Writer de `ts.turno` sí.
