---
title: Inventario interacción — prep / req infoTurno
version: 0.1.0
status: proposed
owner: grupogea
last_updated: 2026-09-10
phase_id: sdd.hospital.turnos-agenda-info-turno-prest.interaccion
---

# Inventario interacción

Fuente: `infoTurno.xhtml` L150–232 · `BBAsignacionTurnos` L697–710.

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| Abrir Asignar / Información | menú fila / post-reserva / post-sobreturno | HIS carga prep + `listReqRealizaPrest` | GET al abrir `turnos-info-turno-dialog` |
| Panel prep | `preparacionPrest.preparacionStr` | HTML `escape=false` | `data-testid="turnos-info-turno-preparacion"` + innerHTML |
| Tabla req | `listReqRealizaPrest` | cols Documentación / Observaciones | mismas cols; `@for` como doc-req |
| Empty tabla | lista vacía | `no_se_encontraron_registros` | ya `@empty` |
| Print | solo recepción | `actionBtnImprimirPreparacionPrevia` | **no render** call center |
| Repetidos concat | `otorgaTurnoRepetido/Multiple` | `<br/>` + union req | **diferido** |
