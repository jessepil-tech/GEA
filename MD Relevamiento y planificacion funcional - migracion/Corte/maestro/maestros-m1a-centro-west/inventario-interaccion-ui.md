---
title: Inventario interacción — M1a logos
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.maestros-m1a-centro-west.interaccion
---

# Inventario interacción — M1a logos (G0)

| Acto | HIS | HAB |
|------|-----|-----|
| Entrar | west `logoCentroAte` (centro en session; ítem **Logo** disabled sin id) | Editar/Agregar → **pantalla** `app-centro-atencion-ficha` (`host=page`, ruta `/centros-atencion/:id` o `/nuevo`). Mismo componente admite `host=dialog`. Accordion 170px · Logo disabled sin `editingId` |
| Upload | `auto=true` → bytes en bean | archivo pendiente en la hoja |
| Trash | `confirm(desea_eliminar_el_icono)` | confirm HAB |
| Aceptar | `updatePackLogos` + `ROW_UPDATE_INFO` | PUT/DELETE + toast; permanece en la hoja |
| Cancelar | salir west / otra hoja | ítem Centro Atención o Cancelar/Volver → listado |

Alta de centro **no** abre logos (HIS exige centro en session).
