---
title: Inventario validaciones — M1a logos
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro-west.validaciones
---

# Inventario validaciones — M1a logos (G0)

| Disparador | Legacy | Destino |
|------------|--------|---------|
| SVG app / term-auto | `allowTypes` svg\|SVG; sin `checkImgSize` | 400 `Archivo No Válido` si no es SVG |
| PNG impresión / comp / prescri / HC | PNG + `checkImgSize` 215×120 | 400 `ERROR_TAMANO_IMG` |
| PNG small | PNG + 50×45 | 400 `ERROR_TAMANO_IMG` |
| ImageIO null | NPE HIS | 400 `Archivo No Válido` (contingente API; no silenciar) |
| Insert pack | `ImpBusPackLogos`: no inserta si solo HC/prescripciones | 400 si no hay pack y el slot no crea pack |
| Centro inexistente | session | 404 |
| Aceptar | siempre `ROW_UPDATE_INFO` si no exception | toast |
| Trash | confirm; null en memoria hasta Aceptar | confirm HAB + DELETE slot en Aceptar |

Placeholders HIS `{1}` `{2}` (no `{0}`).
