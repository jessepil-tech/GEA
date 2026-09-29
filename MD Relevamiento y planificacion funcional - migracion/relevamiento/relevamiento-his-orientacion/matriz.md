# Matriz — orientación HIS

Leyenda: **Migrado** · **Parcial** · **Diferido(slug)** · **WAIVE** · **N/A**

| Capacidad | Legacy | Web hoy | Estado |
|-----------|--------|---------|--------|
| Grilla de módulos + PNG | `inicio.xhtml` / BBModulos | `/dashboard` tiles 112×112 | **Migrado** (shell; recorte perfil = M2) |
| Tile entra al módulo | Click RECEPCIÓN/TURNOS → inicio módulo | `moduleEntryRoute` | **Migrado** [`paridad-orientacion-web/`](../../cortes/plataforma/paridad-orientacion-web/) gate-done |
| Árbol `MENU_APLICACION` | MenuBuilder / pv:menu | Catálogo TS + pending | **Parcial** (hardcode; padres de este corte OK; 1240 = M2) |
| Hab. turnos serv/prof bajo TURNOS | Dominios (O1 **15411**) | Hijos de TURNOS | **Migrado** (URL `/configuracion/hab-turnos-*` intacta) |
| Gate puesto recepción | `inicioRecepcionCentro` | Índice `/recepcion/inicio`; gate no | **Diferido(`paridad-recepcion-gate`)** |
| Picker call center Turnos | inicioTurnos / chrome CC | `/turnos/inicio` T1 | **Parcial** (OK T1; raíces TURNOS visibles pending) |
| Hojas disabled visibles | No (perfil recorta) | Sí + “Pendiente…” | **Parcial** (útil migración; recortar en prod) |
| Satélites AGI/TV fuera de grilla HIS | Otra URL | Sidebar al final; no en grilla | **Migrado** |
| Hojas sin icono genérico | Texto | Sin icono en hojas hab | **Migrado** |
| Look Verona | Teal / 12px / no zoom | DS Angular | **WAIVE** look (a propósito; no es gap de negocio) |
| Dump completo menú + perfil admin | BD | [dump-menu.md](dump-menu.md) | **Hecho** O1 (origin 37; admin +CRM +SEGURIDAD) |

WAIVE de look: evidencia — contrato D de
[`regla-paridad-orientacion-visual.md`](../../canon/regla-paridad-orientacion-visual.md).
No WAIVE de “el menú puede vivir en otro módulo”.
