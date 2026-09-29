# Control de Gestión — Inventario de pantallas del legacy

Carpeta de **seguimiento** (control de gestión), no de especificación. Acá vive el
relevamiento pantalla‑por‑pantalla del HIS legacy (ThinkSoft, JSF/PrimeFaces,
`http://10.0.0.28:29300/HOSPITAL`), módulo por módulo, en Excel.

> **Qué NO es:** esto no reemplaza ni compite con los cortes SDD de
> `docs/cortes/**`. Las reglas de negocio y su evidencia ejecutable siguen viviendo
> en cada slice (`spec.md`, `inventario-validaciones.md`, `verify-report.md`).
> Esta carpeta es el mapa de superficie (qué pantallas existen y a qué URL abren),
> útil para dimensionar alcance y no saltear pantallas al migrar.

## Contenido

| Archivo | Qué es | Estado |
| --- | --- | --- |
| `inventario-pantallas-modulo.md` | Instructivo del barrido: formato del Excel, cómo entrar al legacy, patrón de menú lateral por registro, variantes de buscador, casos borde (ítem deshabilitado, tabla vacía, redirección a logout por permisos). | Vigente |
| `ModuloAdministracionGeneral_Pantallas.xlsx` | Inventario del módulo **Administración General**. | ✅ Completo — 658 filas, 8 ramas, 0 celdas vacías. Mejor referencia del patrón. |
| `ModuloTurnos_Pantallas.xlsx` | Inventario del módulo **Turnos** (lista de pantallas). | Relevamiento de partida (no auditado hoja‑por‑hoja como Admin. General). |

## Formato del Excel (resumen)

Hoja `Hoja1`, una fila por pantalla. Columnas en orden:

```
Modulo | menu nivel 1 | menu nivel 2 | menu nivel 3 | menu nivel 4 | [menu nivel 5] | url
```

- La fila del ítem superior lleva los niveles profundos vacíos y su URL de apertura.
- Lo que aparece **al elegir un registro** (servicio, profesional, paciente) va un
  nivel más abajo, sin borrar la fila padre.
- `menu nivel 5` se agrega **solo** si el módulo anida tan hondo (pasó en Admin.
  General → Módulos); si no, no se agrega.
- Ítem visible pero deshabilitado → se lista con `url` vacía (no se inventa URL).
- Ítem que redirige a logout por falta de permiso del usuario de prueba → texto
  `Redirige a logout al hacer click` en **fuente roja** (necesita un usuario con
  más permisos para capturarla).

El detalle completo (cómo entrar, buscadores, caídas de sesión, método de extracción
desde el código fuente cuando la pantalla no abre) está en
[`inventario-pantallas-modulo.md`](inventario-pantallas-modulo.md).

## Reglas de uso

- **Credenciales y paciente de prueba** los da quien pide el inventario; no se versionan.
- **No volcar PII** en el Excel ni en el chat: teléfono, mail, número de afiliado ni
  documentos de otras personas.
- **Un solo barrido a la vez** contra el legacy: el HIS es de sesión única por usuario.
- Los scripts de llenado (Excel COM en PowerShell) que apuntaban a la raíz vieja
  (`C:\Proyectos\GEA Cursor\Modulo*.xlsx`) deben repuntarse a esta carpeta.
