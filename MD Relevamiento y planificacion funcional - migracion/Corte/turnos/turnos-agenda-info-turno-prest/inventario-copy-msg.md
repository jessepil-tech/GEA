---
title: Inventario copy — prep / req infoTurno
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-prest.copy
---

# Inventario copy

Chrome labels: **reusa T5.1e** (`turnos-agenda-labels.ts`). Este corte no agrega toasts MessageBundle.

| msg.key | Valor | Constante | Este slice |
|---------|-------|-----------|------------|
| `preparacion_previa` | Preparación Previa | `preparacionPrevia` | ya — contenido HTML |
| `requisitos_realizacion` | Requisitos Realización | `requisitosRealizacion` | ya — filas |
| `documentacion` | Documentación | `documentacion` | ya — col tabla |
| `observaciones` | Observaciones | `observaciones` | ya — col tabla |
| `no_se_encontraron_registros` | No se encontraron registros | `noSeEncontraronRegistros` | ya emptyMessage |
| `imprimir` | Imprimir | — | **N/A** agenda |
