# Maestros / seguridad — orientación

| Maestro / permiso | ¿Bloquea el mapa si falta? | ¿Existe CU/SDD? | Estado |
|-------------------|----------------------------|-----------------|--------|
| `MENU_APLICACION` (árbol HOSPITAL) | Sí — sin esto el Web inventa padres | Identity `GET /menus` previsto; dump O1 en [dump-menu.md](dump-menu.md) | **Hecho** (catálogo 1240; sync Identity pendiente) |
| `MENU_PERFIL_ACCESO` / personal | Sí — home origin ≠ admin | Identity oleadas B+ | Pendiente prod (`menuShowAll` DEV) |
| Iconos PNG módulo | No bloquea operar; sí reconocimiento | Portados a `Hospital-Web/public/images/menu-modulos/` | Parcial (M1) |
| Call center / puesto recepción | Sí para operar esos módulos | T1 turnos hecho; recepción gate **no** en Web | Parcial |
| Catálogo hardcode `hospital-menu.catalog.ts` | No para piloto; sí para 1.200 hojas | Sustituir por API | Deuda M2 |

Identity: [`contrato-api-identidad.md`](../../arquitectura/contrato-api-identidad.md) §6.
