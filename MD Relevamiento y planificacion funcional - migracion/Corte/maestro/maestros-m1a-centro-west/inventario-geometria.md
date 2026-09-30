---
title: Inventario geometría — M1a logos
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro-west.geometria
---

# Inventario geometría — M1a logos (G0)

HIS: tabla 5 columnas en west (`logoCentroAte.xhtml`): label · `graphicImage` **height=32** · fileUpload · trash · extensión (+ `maxSize` en PNG).

HAB (producto): listado sin west. Alta/edición en **pantalla** (`app-centro-atencion-ficha`, `host=page`). El mismo componente entra en `ds-dialog` con `host=dialog`. Accordion 170px. Logo habilitado con `editingId`.

| Pieza HIS | HAB |
|-----------|-----|
| `pe:layoutPane west` 170 + accordion tab Centro Atención | dentro del dialog de edición · `UI.hisWest` · resto `disabled` + tooltip diferido |
| `logoCentroAte` center | main del dialog (7 filas, preview h=32, Aceptar) |
| fileUpload auto | input file |
| trash | `ui.list.iconBtnDanger` |
