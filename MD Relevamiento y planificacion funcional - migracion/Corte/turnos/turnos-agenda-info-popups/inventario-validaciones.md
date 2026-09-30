---
title: Inventario validaciones — T5.1 hijo info popups
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-info-popups.validaciones
---

# Inventario validaciones — Info popups (G0)

Lectura. No MessageBundle de error en estos dialogs.

| Regla | Legacy | UI | API |
|-------|--------|----|-----|
| Info búsqueda siempre disponible | `type=button` sin convenio | Abrir aunque north vacío | N/A |
| Info convenio sin convenio/plan | HIS igual abre dialog (tabla doc req + paneles `rendered` si hay texto) | Abrir; chrome tabla vacía; obs solo si hay texto; X cierra | DTO ya cargado o null |
| Auto-popup solo si hay texto | `descripcionConvenio` / `observacionesPlanConvenio` | **WAIVE Agenda** — HIS no abre el dialog | campos se usan al click info |
| HTML en obs | `escape="false"` | Render HTML sanitizado (sin script) | string PG |
| Doc req obligatorio naranja | `obligatorioRecepBoolean` | Clase `gt-his-orange` cuando haya filas; **datos** diferido | — |

Toast: ninguno. Error API: N/A (sin GET nuevo v1).
