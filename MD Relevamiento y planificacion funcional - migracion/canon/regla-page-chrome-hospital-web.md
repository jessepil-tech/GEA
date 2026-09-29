---
title: Regla — Page chrome Hospital-Web (breadcrumb, título, dark mode)
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.regla-page-chrome-web
---

# Page chrome — breadcrumb, título y dark mode

Complementa [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md) (L&F / DS).
Aplica a **toda page** con `app-page-shell`.

## 1. Breadcrumb = camino del menú, no de la URL

| Correcto | Incorrecto |
|----------|------------|
| `Inicio` → `Turnos` → `Consulta de Agendas Generadas` | `Inicio` → `Configuración` → … |
| Ancestros **clicables** (`moduleEntryRoute` / padre menú) | Segmentos intermedios en negrita |
| Último segmento = título i18n (`msg.*` / `*-labels.ts`) | Copiar carpeta legacy de la ruta Angular |

**Regla:** el breadcrumb sigue **`MENU_APLICACION`** / `hospital-menu.catalog.ts`, no el
prefijo técnico de la ruta (`/configuracion/…` puede ser legado mientras el tile/menú
sea otro módulo, p. ej. **TURNOS**).

**Implementación TURNOS:** `Hospital-Web/src/app/turnos/turnos-breadcrumb.ts` →
`turnosMenuBreadcrumb(labelPantalla)`.

**Otros módulos:** crear helper análogo (`recepcionMenuBreadcrumb`, …) con
`Inicio` + label del módulo + label de la hoja.

**Componente compartido:** `app-breadcrumb` — solo el **último** ítem va en negrita;
los anteriores link con estilo secundario.

## 2. Título de página — no duplicar

Si el breadcrumb ya termina con el título de la pantalla (`msg.*`):

- **No** pasar `[title]` a `app-page-shell` (no `<h1>` duplicado).
- El west / menú lateral tampoco repite ese título (ya en regla-paridad-ui).

```html
<!-- bien -->
<app-page-shell [breadcrumb]="breadcrumb"> … </app-page-shell>

<!-- evitar -->
<app-page-shell [breadcrumb]="breadcrumb" [title]="L.titleConsulta"> … </app-page-shell>
```

## 3. Dark mode — obligatorio y con trampa en clases dinámicas

Hospital-Web usa `.dark` en `<html>` (`ThemeService`). Smoke **light + dark** antes de UI done.

### 3.1 Tokens estáticos (`*-labels.ts`)

Preferir pares `dark:` en `Sz` / `UI.*` del slice. Revisar bordes, inputs, tablas, popups.

### 3.2 Clases resueltas por mapa / concatenación

Si el estado se aplica así:

```ts
cssDia(claseCss) // lookup en Record
`${base} ${selectedClass}` // concat
```

los utilitarios `dark:bg-*` / `dark:text-*` **pueden no entrar en el bundle** de Tailwind
v4 aunque estén escritos en el string del objeto.

**Patrón acordado (consulta agendas, 2026-09-03):**

1. Clases **hook** estables en el DOM: `gt-cal-day`, `gt-cal-day--disponible-calendar`,
   `gt-matrix-s`, `gt-matrix-s--selected`, …
2. Overrides explícitos en CSS: `Hospital-Web/src/styles/grilla-turnos-surfaces.css`
   (selectores `.dark .gt-…` con contraste garantizado).
3. En dark, estados de calendario tipo legacy: **color de texto** saturado, sin
   `bg-green-100` / fondos light que brillan sobre `gray-800`.
4. Botones de día: `bg-transparent border-0` en la base (evitar blanco del user-agent).
5. **Selección + foco (v1.14):** `design-system.css` tiene `*:focus { box-shadow: none
   !important }`. Un anillo de selección que **solo** usa `box-shadow` desaparece
   mientras el día tiene foco (parece que “no marca” hasta clic afuera). Override
   `.gt-*-selected:focus` con `box-shadow: … !important`, o marcar con borde/`outline`.
   Cabecera `p:calendar`: locale del `common.js` **de esa app** (no reusar Lu-first).

**Nueva pantalla con estados dinámicos:** extender `grilla-turnos-surfaces.css` o crear
`{slice}-surfaces.css` importado desde `styles.css`; no confiar solo en `dark:` dinámico.

### 3.3 Checklist dark (por pantalla)

- [ ] Matrices / celdas clicables legibles (S/N, selección).
- [ ] Calendarios / leyendas con swatches sólidos en dark.
- [ ] Sin bloques blancos o `bg-*-50` de light colándose en `.dark`.
- [ ] Selección visible (borde inset o ring con offset sobre fondo oscuro).
- [ ] Selección visible **con el control enfocado** (`*:focus` del DS no debe borrar el ring).
- [ ] `p:calendar` inline: cabecera de días del locale HIS (no solo números).

## Referencias

| Artefacto | Uso |
|-----------|-----|
| `turnos-breadcrumb.ts` | Migas módulo TURNOS |
| `breadcrumb.component.html` | Solo último ítem bold |
| `grilla-turnos-surfaces.css` | Overrides dark matriz + calendario consulta |
| `grilla-turnos-labels.ts` | Tokens UI + hooks `gt-*` |

## Verify SDD (fila sugerida)

| Ítem | Estado | Nota |
|------|--------|------|
| Breadcrumb = menú real | | no carpeta URL |
| Sin `<h1>` duplicado | | PageShell sin `[title]` si migas lo cubren |
| Dark mode smoke | | light + dark; estados dinámicos con CSS hook si aplica |
