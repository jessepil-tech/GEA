---
title: Plan — Equipo en Suspender y Quitar suspensión
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.turnos-grilla-suspender-equipo.plan
---

# Plan

1. Universo: el radio Equipo de las dos hojas del padre. Hecho.
2. Rama `dev/tur-historial-equipo`. Hecho. Sin push.
3. Gate UI: habilitar el radio que ya está. Hecho en la rama.
4. Api: `codItemEquipo` en el listado de candidatos. Suspender y quitar no cambian de firma.
5. Web: el radio deja de estar disabled. Sin equipo, el aviso del padre.
6. Evidencia: viaje, turno `17255287`, lote `6`, acceso y p95.
