---
title: Regla — Paridad UI con legacy (contrato + criterio)
version: 1.20.0
status: active
owner: grupogea
last_updated: 2026-09-23
phase_id: sdd.hospital.regla-paridad-ui-legacy
---

# Regla — Paridad UI con legacy

**Decisión:** el xhtml + `Resources.properties` son el **contrato** (qué se muestra,
cómo se comporta, **disposición** de inputs/combos/grillas y menús — **incluye
tamaños y posiciones**). La migración actualiza el **Look & Feel** (DS Origin
Backoffice) **sin** rediseñar la arquitectura de información ni la lógica de
interacción, salvo mandato explícito.

**Mandato ABM de catálogo (2026-09-17b):** chrome Web = listado agenda/HAB (barra
búsqueda + tabla nativa + Agregar + pager). Copy, validaciones, toasts y anchos
de input HIS se conservan en el dialog. **No** portar `pe:layoutPane` north/west
ni clonar Identity Empresas. Agenda operativa / recepción / HAB / horarios
siguen esta regla completa. Cursor: `Hospital-Web/.cursor/rules/hospital-web-abm-listado.mdc`.

**Ampliado 2026-09-02 (T3 HAB/horarios):** feedback CRUD = toast; confirmación de
borrado = **modal** (no `window.confirm`/`alert`); errores HTTP manejados en pantalla
**no** deben expulsar a `/500`|/`404` globales.

**Ampliado 2026-09-02 (v1.8 — gate pre-smoke):** antes de **solicitar o ejecutar smoke
UI/ops**, la pantalla debe tener **paridad con legacy** (disposición + interacción +
copy + validaciones). Toda excepción = `diferido(slug)` o nota explícita en
tasks/verify del SDD — **nunca** silencio ni “lo vemos en el smoke”.
Cablear API + labels + toast **no** alcanza para pedir smoke ni marcar UI done.

**Ampliado 2026-09-02 (v1.9 — geometría = DoD):** “misma estructura de bloques” **no**
alcanza. Anchos de columna, altura/ancho de inputs, filas del `panelGrid`/`h:panelGrid`
y espaciados del xhtml son **contrato desde el primer corte**, no polish post-smoke.
Origen del gap: Generar Agenda Turnos (T4) — se reusó CSS de buscadores HAB/horarios
(apilado + `md:`) y Centro|Servicio dejaron de ir en la misma línea.

**Ampliado 2026-09-02 (v1.10 — gate de arranque agentes):** la paridad UI no es
recordatorio al cerrar. Es **gate de arranque** antes de template/API-first:
Cursor rules `alwaysApply` `hospital-gate-ui-arranque` (Migration) /
`hospital-web-gate-ui-arranque` (Web) + skill `gate-ui-arranque`. Orden:
xhtml → inventarios/geometría → UI del slice → wiring API → verify → smoke.
Team rule: [`cursor-team-rules.md`](cursor-team-rules.md) §7.

**Ampliado 2026-09-07 (v1.12 — acciones de grilla / menú contextual):** copy igual
(`Asignar Turno`, `Liberar`) **no** autoriza cambiar el **disparador** (botón visible vs
`ui-icon-gear` + `p:tieredMenu` / `p:menuitem`). Antes de template: inventario de
**interacción** (quién dispara qué) además de copy y geometría. E2E y smoke deben
seguir el disparador legacy, no un atajo “más fácil de testear”. Lección: T5 agenda —
botones inline en grilla.

**Ampliado 2026-09-10 (v1.13 — dialogs PrimeFaces / tablas):** `p:dialog height` es el
**cuerpo** (titlebar aparte). `InputWid100` es el **100% de la celda** (Verona
`calc(100% - 10px)`), **no** 111px. Filas de un mismo `<table>` HIS = **una** tabla
Web (columnas compartidas); N grids con `auto` en labels **desalinea**. `rowspan` de
paneles laterales es geometría, no polish. Disabled Verona = `#dadada`. Chrome de
dialog: inventariar de dónde sale cada campo (grilla / GET / vacío diferido) — no hay
API “info*” por defecto. Lección: T5.1e `infoTurno.xhtml`.

**Ampliado 2026-09-11 (v1.14 — calendarios `p:calendar` + foco DS):** `mode="inline"`
incluye **cabecera de días** del locale HIS (no solo números). Selección de día
**no** puede ser solo `box-shadow`: el DS (`*:focus { box-shadow: none !important }`)
lo apaga mientras el botón tiene foco. Checks HIS = texto + checkbox **hermanos**,
no `<label>` wrapping ni `[(ngModel)]="form[key]"` en `@for`. Lección: T5 agenda
west + popup Turnos repetidos.

**Ampliado 2026-09-16 (v1.15 — chrome de campo `panelGrid`):** cada `p:commandButton`
icon-only **hermano** del input/combo (lupa, X, info) es contrato. Inventario
**copy** del `title` (`limpiar_profesional`, etc.) **no** implica que el botón
exista en Web. Limpiar el formulario ≠ X de un campo. Lección: T5 Agenda north
prestación/profesional.

**Ampliado 2026-09-16 (v1.16 — abrir dialog ≠ flag BB):** `visible="#{display*}"`,
`displayPopup* = true` o `update=":popup…"` **no** son apertura. Apertura =
clic del icono/`commandButton` o `PF('…').show()` explícito. Si no hay `show()`
y el operador HIS no ve el modal al cambiar el combo, Web **no** auto-abre.
Lección: info observaciones profesional (Agenda).

**Ampliado 2026-09-16 (v1.17 — Volver del buscador):** `actionBtnVolver` del
`BBBuscador*` **no** es “cerrar y dejar el valor”. Convenio / prestación /
profesional de Agenda llaman `set*Buscado(null)` (vacían el campo y la grilla).
Paciente **no**: `BBBuscadorPaciente.actionBtnVolver` solo oculta el popup.
Leer el listener, no asumir “cancel = noop”. Lección: T5 Agenda north.

**Ampliado 2026-09-16 (v1.18 — overlay del dialog):** HIS buscadores Agenda
`closable="false"` (sin X, sin dismiss por máscara). En Web: `closeOnBackdrop=false`
en esos buscadores. En `app-ds-dialog`, overlay close **solo** si el pointerdown
empezó en el overlay — arrastrar para seleccionar texto en un input (mouseup en
la máscara) **no** cierra. Lección: T5 buscadores.

**Ampliado 2026-09-21 (v1.19 — footer leyenda del `dataTable`):** HIS
`scrollable="true"` + `f:facet name="footer"` (estados Sobreturno/Cancelado/…)
es **footer fijo** del widget, no un renglón debajo de las filas. Web: pane con
scroll interno + `gt-his-consulta-leyenda` **fuera** del scroll. Prohibido copiar
`obsTableWrap` de T4 (generar/eliminar) si el xhtml tiene ese facet: la leyenda
viaja con el volumen. Reusar Consulta/Historial/Cola, no la familia T4. Lección:
T6.4 suspender/quitar — ya cobrado antes en T5.5/T6.2/T6.3.

**Ampliado 2026-09-23 (v1.20 — ícono apagado y POST incompleto):** un `disabled`
en lápiz o tacho sin cambio de opacidad se lee como activo. El botón que envía
el pedido queda apagado hasta que cada id que el handler exige está elegido;
el texto tipeado en un buscador no es un id. El toast de pantalla no es la lista
`idA/idB/idC requeridos` del handler. Lección: lápiz de convenio en
`horarioTurnoEquipo.xhtml`.

**Dónde vive la pantalla en el HIS** (padre de menú, tile, gate de puesto) no es este
archivo: [`regla-paridad-orientacion-visual.md`](regla-paridad-orientacion-visual.md).

Origen: gaps en habilitación turnos (labels inventados, paginator/sort omitidos,
Agregar oculto vs disabled, iconos/tamaños, filtros mal maquetados, botones
`authPrimary` a ancho 100% estirados al input). Ampliado 2026-08-28 con lecciones
de buscadores hab, T3 horarios (copy / validaciones). Ampliado 2026-08-31: no fusionar
secciones del menú lateral en tabs (Turnos por Servicio). Ampliado 2026-09-01: cadena
popup padre + buscador multi-select (prestaciones T3) — tamaño/filtro/sort/page en
**ambos** dialogs; no sustituir buscador por alta manual cod/id.

Canon proceso: [`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md).

## Contrato duro (no negociable sin diferido/WAIVE)

| Ítem | Regla |
|------|--------|
| Copy | Mismo texto `msg.*` / `headerText` / confirms — no acortar ni renombrar |
| Inventario copy **antes** de UI done | Tabla `msg.key` → valor properties → constante en `*-labels.ts` (o equivalente). Sin esa tabla en spec/tasks/verify → **UI no está done** |
| Inventario validaciones **antes** de UI/API done | Tabla regla → mensaje legacy → UI → API. Fuentes: BB (`FacesMessage` / checks) + `MessageBundle` BUSINESS. Sin tabla → **no** G4 done ni PASS verify |
| Icono vs texto | Si legacy es icono+`title`, migrado es icono+`title` (no “Editar” escrito) |
| Columnas grilla | Mismo set y orden; flags = checkbox disabled si el xhtml lo hace |
| Comportamiento | Mismo `disabled` / `rendered` (ej. Agregar visible pero disabled hasta contexto) |
| Paginator / sort | Si el `dataTable` los tiene → implementar o `diferido(slug)` |
| Menú / título | Misma ubicación de módulo y título i18n |
| **Buscador / dialog** | Inventariar **todos** los filtros y columnas del xhtml del buscador; gap → `diferido(slug)`, nunca silenciar (ej. amb/int, combos, apellido≠nombre) |
| **Geometría (tamaño + posición)** | Del xhtml: `width` de `td`/`style`, `%` de columnas, `styleClass` tipo `InputWid100`, `style="width: …"` de timePicker/calendar/botones, `cellspacing`/`spacer`. Replicar en Web (grid/flex) **antes** de UI done. Gap → `diferido(slug)`. |
| **Interacción / disparador** | Del xhtml + BB: menú contextual, icon-only, drag-drop, doble paso (reserva→modal→otorga). Tabla en SDD (`inventario-interaccion-ui.md` o fila en verify). **No** sustituir por botones inline “equivalentes” sin `diferido(slug)`. |
| **Chrome de campo (`panelGrid`)** | Cada icon-only hermano del input (lupa / X / info) se porta o `diferido(slug)`. Copy del `title` en inventario **≠** botón cableado. X de un campo ≠ `Limpiar Datos` del form. |
| **Footer `dataTable` / leyenda (v1.19)** | Si hay `scrollable` + `f:facet footer` (leyenda de estilos): scroll **solo** en el body; leyenda anclada (`gt-his-consulta-leyenda`). “Hay leyenda debajo de `</table>`” ≠ footer HIS. |
| **Ícono apagado / POST incompleto (v1.20)** | El `disabled` de un ícono se ve (opacidad). El botón que envía queda apagado hasta cada id que el handler exige. El toast de pantalla no es `idA/idB/idC requeridos`. |

**Prohibido:** inventar labels; “copy pragmático” o subtítulos aclaratorios no presentes en
legacy (salvo comentario de código / doc SDD, no UI); ocultar chrome que en legacy está
visible (aunque disabled); omitir paginator “porque hay pocas filas”; dar por hecha la UI
solo porque el API ABM funciona; cerrar un buscador solo con texto libre si legacy tiene
combos/filtros/columnas extra; marcar G4/Web **done** sin fila-a-fila `msg.*` ↔ labels;
**omitir validaciones** (required, intervalos, condicionales) porque “el form ya tiene
Validators.required genérico” o “lo valida solo el API”; **cerrar disposición** solo con
“mismos campos en otro orden/tamaño” o “responsive moderno” que apila filas que en legacy
van juntas.

## Geometría de formulario (anti-regresión — v1.9)

**Decisión:** tamaño y posición de controles son **DoD de paridad**, no refinamiento.

| Extraer del xhtml | Migrado |
|-------------------|---------|
| `td width="200"` / cols `40%` / `colspan` | Misma proporción (ej. `grid-cols-[200px_1fr_200px_1fr]`) |
| `InputWid100` / input a ancho de celda | `w-full min-w-0` **de la celda**, no del viewport |
| `style="width: 60px"` (timePicker), `width: 130px` (south) | Misma magnitud compacta |
| `cellspacing` / `p:spacer width="10\|40px"` | Gaps equivalentes entre controles de la misma fila |
| Dos campos en la **misma** `tr` | Misma fila en Web — **sin** apilar por breakpoint |

| Permitido (L&F) | Prohibido sin `diferido` / mandato |
|-----------------|-------------------------------------|
| Bordes, tipografía, `dark:`, radios DS | Reusar CSS de **otra** pantalla (HAB buscador apilado, `h-10`/`min-w-[16rem]`) si el xhtml es tabla/`panelGrid` fijo |
| Redondeo / tokens `UI.*` del DS | `grid-cols-1` + `md:`/`sm:` que **rompe** filas legacy en desktop |
| | Tratar geometría como “va después del smoke” / “estructura OK = done” |
| | Labels de form stacked (`mb-2 block`) donde legacy es celda de fila alineada al medio |

**Antes de codear (checklist geometría):** abrir el xhtml → anotar anchos/cols/spacers
de north (y dialogs) → definir clases del slice (`*-labels.ts` / `*UI`) **para esa
pantalla** → implementar → verify con fila **Geometría vs xhtml**.

Lección T4 (`generacionGrillaTurnos.xhtml`): Centro + Servicio en una `tr` con
`200|40%|200|40%`; timePicker `60px`. No copiar el patrón de filtros HAB.

## Acciones de grilla y menús (anti-regresión — v1.12)

**Decisión:** el **mecanismo** de interacción es contrato tanto como el copy del menú.

| Extraer del xhtml | Migrado |
|-------------------|---------|
| `p:column` gear + `p:tieredMenu` / `p:menuitem` | Menú contextual por fila (icono + overlay), **no** botones de texto en la celda |
| Icon-only + `title` | Mismo patrón (`app-icon` + `aria-label` / `title`) |
| Drag-drop (`p:droppable`) | Implementar o `diferido(slug)` — no omitir en silencio |
| Acción en **dos pasos** (reserva → modal → otorga) | Respetar secuencia y feedback (toast solo al paso final) |

| Prohibido sin `diferido` | Ejemplo |
|--------------------------|---------|
| “Misma acción, botón más visible” | `Asignar Turno` inline en grilla T5 |
| E2E que usa disparador distinto al legacy | `getByRole('button', { name: 'Asignar Turno' })` en celda |
| Declarar UI done solo con north/west OK | Grilla con acciones sin inventario de interacción |

**Checklist (bloquear G5 done):** inventario interacción en SDD del slice → verify fila
**Disparador vs xhtml** → e2e/smoke usan el mismo camino.

Lección T5 (`agenda.xhtml`): col `width="24"` · `ui-icon-gear` · menú
`ASIGNAR_TURNO` / `LIBERAR` / `INFORMACION`. Ver
[`turnos-agenda-otorgar/inventario-interaccion-ui.md`](../cortes/turnos/turnos-agenda-otorgar/inventario-interaccion-ui.md).

## Dialogs PrimeFaces, tablas y chrome (anti-regresión — v1.13)

**Decisión:** un `p:dialog` con tabla + `rowspan` se migra como **ese** layout, no como
stack de cards. Medir el xhtml y Verona; no copiar un `w-[111px]` de otro corte.

| Extraer del xhtml / Verona | Migrado |
|----------------------------|---------|
| `p:dialog width/height` | `width` = dialog. `height` = **`.ui-dialog-content`** (cuerpo). Titlebar **fuera** de esos px. Si el cuerpo Web incluye titlebar en el 520, aparece scroll interno que HIS no tiene. |
| HIS cabe **sin** scroll del modal | Cuerpo Web sin overflow de página. `overflow:auto` solo en el panel que HIS ya lo tiene (ej. Datos Turno `height:166px; overflow:auto`). |
| `InputWid100` | `width: 100%` / `min-w-0` **de la TD**. Verona: `calc(100% - 10px)`. **Prohibido** tratarlo como 111px fijo (111px en otros xhtml es `td` de **label**, no el input). |
| Varias `tr` del mismo `<table>` | **Una** `<table>` (o un grid con columnas compartidas / subgrid). Un CSS grid por fila con labels `auto` **no** alinea Fecha con Centro Atención: cada fila recalcula el ancho del label. |
| `td colspan` (nombre paciente, prestación) | Mismo span. |
| `td rowspan` (Observaciones + Preparación al lado de Datos **y** Req/Doc) | Misma celda/columna que cubre **dos** filas. Prohibido apilar Req/Doc debajo de Obs y forzar scroll. |
| `disabled="true"` input | Fondo Verona **`#dadada`**, texto negro, `opacity: 1`. **No** blanco ni el gris del panel (`#f5f8f9`) — se pierde el “solo lectura”. Editable (fecha prescripción al otorgar, textarea al asignar) = blanco. |
| `msg.*` del header de panel | Valor de **properties**, no el key. `datos_paciente` → **Datos del Paciente**, no “Datos Paciente”. |

| Prohibido sin `diferido` | Qué pasó en T5.1e |
|--------------------------|-------------------|
| `w-[111px]` en inputs `InputWid100` | Centro/servicio recortados (`HOSPITAL-DE`, `CLINICA MEDI`) |
| Dialog `h-[520px]` **con** titlebar adentro | Scroll interno; Volver / Req / Doc fuera de vista |
| Grid suelto por fila | Inputs de Datos Turno no forman columnas |
| Obs+Prep solo al lado de Datos Turno | Req/Doc empujados abajo |
| Disabled = fondo del panel | Campos “editables” a la vista |

**Chrome: de dónde sale cada campo (antes de template)**

No asumir `GET /infoTurno`. Inventariar en SDD:

| Campo en el xhtml | Preguntar |
|-------------------|-----------|
| ¿Bean de la fila / grilla? | Reusar DTO de consulta; no inventar endpoint. |
| ¿Otro bean (ficha, doc-req, prep)? | GET ya existente al **abrir** el dialog (no solo si el usuario abrió otro popup antes). |
| ¿Sin columna / sin API? | Input vacío + `diferido(slug)` o N/A. **No** copiar un campo parecido (obs de paciente ≠ obs de turno; tipo paciente sin columna ≠ inventar). |

Lección T5.1e (`infoTurno.xhtml` + `asignacionTurnos.xhtml` L274 `1200×520`):
[`turnos-agenda-info-turno/`](../cortes/turnos/turnos-agenda-info-turno/). Ficha + doc-req al abrir;
tipo paciente / prep / requisitos filas / persist obs-prescripción = diferido o vacío.

## Calendarios PrimeFaces y estado seleccionado (anti-regresión — v1.14)

**Decisión:** un `p:calendar` es el widget jQuery UI completo (thead + celdas), no
un grid de números. Extraer locale del **`common.js` del módulo** (HOSPITAL_2 ≠
consulta agendas ≠ seguridad).

| Extraer del HIS | Migrado |
|-----------------|---------|
| `p:calendar locale="es" mode="inline"` | Fila cabecera **antes** de los días. HOSPITAL_2 `common.js`: `dayNamesMin: ['D','L','M','M','J','V','S']`, `firstDay: 0` (domingo primero). Pad del mes = `Date.getDay()`. |
| Día seleccionado (`ui-state-active`) | Clase hook `gt-cal-day--selected` **visible con y sin foco**. Override `.gt-cal-day--selected:focus` con `box-shadow: … !important` (el reset global del DS gana si no). Alternativa: borde/`outline` que el reset no pise. |
| `p:selectBooleanCheckbox` + `h:outputText` en `panelGrid` | Texto y check **hermanos** (HIS no envuelve en `<label>`). Binding **por campo** o `[checked]`+(change). **Prohibido** `[(ngModel)]="form[d.key]"` en `@for` (acopla domingo/lunes) y label wrapping (clic en uno prende el de al lado). |

| Prohibido sin `diferido` | Qué pasó en T5 / T5.3 |
|--------------------------|------------------------|
| Calendario solo con números | West agenda sin D L M M J V S |
| Copiar cabecera de **otra** pantalla | Consulta agendas usa Lu-first; agenda HIS es Do-first |
| Selected = `box-shadow` sin `:focus !important` | El anillo aparece recién al clic **fuera** del día |
| Checks en `@for` + `<label>` + ngModel dinámico | Lunes tilda también domingo |

**Checklist Gate UI (si el xhtml tiene `p:calendar` o checks en fila):** cabecera
locale del `common.js` correcto · selected visible con foco · checks como el xhtml.

Lección T5 / T5.3 (`agenda.xhtml` L371 `CalendarioTurnos` · `turnosRepetidos.xhtml`
L268–282): `grilla-turnos-surfaces.css` (`.gt-cal-day--selected:focus`).

## Chrome de campo en panelGrid (anti-regresión — v1.15)

**Decisión:** el grupo `h:panelGrid` / `h:panelGroup` al lado de un input es
**chrome de contrato**, no “lupa alcanza”. Contar los `p:commandButton` del xhtml
(mismo orden) **antes** de template.

| Extraer del xhtml | Migrado |
|-------------------|---------|
| `columns="N"` con input + iconos | N celdas: input (y código si hay) + cada icon-only, **mismo orden** |
| `ui-icon-search` | lupa + `title` `msg.buscar` |
| `ui-icon-close` / `ui-icon-closethick` | X + `title` del msg (`limpiar_*`) + **el** `actionListener` de ese campo |
| `ui-icon-info` / `fa-info` | info + `title`; no omitir porque “el dato ya se ve en el input” |
| `disabled` del icono | mismo `disabled` (visible disabled, no oculto) |

| Prohibido sin `diferido` | Qué pasó en T5 Agenda |
|--------------------------|------------------------|
| Copy `limpiar_profesional` / `limpiar_datos_prestacion` = “sí” y no hay botón | Inventario copy listó titles; north solo tenía X de paciente |
| “El buscador ya abre; no hace falta X” | HIS vacía el combo/prestación sin reabrir el dialog |
| “Limpiar Datos del form cubre el campo” | HIS: X de prestación ≠ `limpiarDatosAgenda` |

**Checklist Gate UI (si el xhtml tiene `panelGrid` de campo):** una fila por
icon-only en `inventario-interaccion-ui.md` (disparador + listener). Copy del
title **no** cierra la fila.

Lección T5 (`agenda.xhtml` L154–199): profesional = combo + X + info;
prestación = input + código + lupa + X + info.

## Abrir dialog (anti-regresión — v1.16)

**Decisión:** el disparador de apertura se lee del **acto de usuario** (clic) o
de `PF('widget').show()` / `onclick` en el xhtml. El bean puede cargar datos
y habilitar el botón **sin** mostrar el modal.

| Extraer del HIS | Migrado |
|-----------------|---------|
| `p:commandButton` + `update=":popup…"` | Abre **al clic** |
| `PF('$popup…').show()` / `onclick="PF(…)"` | Auto o al evento que el xhtml dispara |
| Solo `visible="#{display*}"` + ajax `update` del dialog | Cargar datos / `disabled` del info — **no** auto-abrir |
| `displayX = true` en el BB al cambiar combo | Idem: no es `show()` |

Lección T5 Agenda 2026-09-16: `infoObservaciones()` al elegir profesional setea
`displayInfoObservaciones`; Francisco no ve el popup hasta el clic en info.
Misma familia que T5.1b auto-obs convenio (WAIVE D-TUR-39: sin callers de `show()`).

## Volver del buscador (anti-regresión — v1.17)

**Decisión:** el footer Volver/Cancelar se lee del `actionBtnVolver` del
`BBBuscador*`, no de “cerrar modal = noop”.

| Extraer del HIS | Migrado |
|-----------------|---------|
| `setConvenioBuscado(null)` / `setPrestacionTurnoBuscada(null)` / `setPersonalBuscado(null)` | Vaciar el campo (y la grilla si el setter HIS lo hace) |
| `actionBtnVolver` **sin** callback al padre (paciente Agenda) | Cerrar; **conservar** la selección |
| `closable="false"` en el `p:dialog` | Sin dismiss por overlay; Volver es la salida |

X de campo (`limpiarPersonalCombo`) ≠ Volver del buscador (`setPersonalBuscado(null)`).

## Overlay y selección de texto (anti-regresión — v1.18)

**Decisión:** un click de overlay solo cuenta si el pointerdown **empezó** en la
máscara. Arrastrar texto en un input (mouseup fuera del panel) no es “click afuera”.

HIS buscadores Agenda: `closable="false"` → Web `closeOnBackdrop=false` en esos
dialogs. El chrome DS (`app-ds-dialog`) aplica la regla de pointerdown para todos.

## Footer de dataTable / leyenda (anti-regresión — v1.19)

**Decisión:** el facet `footer` de un `p:dataTable` **scrollable** es chrome del
widget (fijo bajo el body), no contenido que crece con las filas.

| Extraer del HIS | Migrado |
|-----------------|---------|
| `scrollable="true"` + `scrollHeight` | Pane (`gt-his-consulta-table-pane` / `layout="fill"`) + scroll **solo** en el body |
| `f:facet name="footer"` leyenda de estilos | `gt-his-consulta-leyenda` **hermano** del scroll, `flex-shrink: 0` |
| Familia T4 `obsTableWrap` (generar/eliminar) | **No** reusar si este xhtml tiene facet footer: el wrap crece y la leyenda viaja |

Referencia ya cobrada: Consulta Agenda, cola Reasignación, Historial. Lección T6.4
suspender/quitar: inventario decía “footer leyenda” y se portó como `div` post-tabla.

## Buscadores y dialogs (anti-regresión)

Patrón típico: campo(s) de **contexto/display** en la pantalla + dialog de búsqueda.
A menudo hay **cadena de dos dialogs**: popup padre (alta/edición) → `buscador*.xhtml`.

| Regla | Detalle |
|-------|---------|
| Separar display vs criterio | El label post-selección (`Apellido, Nombre`, `Servicio - Centro`) **no** se reinyecta como único campo de filtro al reabrir Buscar. Criterios del dialog = campos del xhtml (apellido, nombre, q, combos). |
| Reabrir limpio tras selección | Si ya hay contexto seleccionado, abrir el dialog con filtros vacíos (o con los criterios tipados **antes** de seleccionar), no con el string de display. |
| Inventario del popup | Antes de “done”: lista explícita filtros + columnas del `buscador*.xhtml` en spec/verify. |
| GET opcionales de combo | Fallo/404 de `/…/centros` o `/…/servicios` lo maneja el componente; **no** debe navegar a página global `/404` (excluir en interceptor o no marcar como app-error). |
| Permisos / adm | Si legacy filtra combos (`personal_adm_*`), implementar o `diferido(slug)` + seed DEV; no dejar búsqueda vacía en silencio. |
| **Cadena padre + buscador** | Inventariar **los dos** xhtml: (1) popup padre — input criterio + Buscar, tabla de seleccionados (`rows`/paginator/sort), flags compartidos; (2) buscador — filtros, columnas, `selectionMode`, paginator, sort, tamaño dialog. Cerrar solo el buscador y dejar el padre como “cod/id tipados” = **silencio**. |
| **No sustituir buscador por alta manual** | Si legacy abre `buscador*` (single o `seleccionMultiple`), migrado abre buscador + API de catálogo. Prohibido “MVP” con input `codPrestacion`/`idPrestacion` a mano salvo `diferido(slug)`. |
| **seleccionMultiple** | Si el BB pasa `seleccionMultiple=true` / dataTable multi: UI multi-check + Aceptar + alta en lote con valores compartidos del padre (duraciones/flags). Edición de fila ya asociada puede seguir single. |
| **Chrome del dialog = contrato** | Del xhtml tomar: `width`/`height` del `p:dialog` (**height = cuerpo**, titlebar aparte — v1.13), alto del contenedor de grilla, `rows` del dataTable, ancho del input de búsqueda, `paginator`/`sortBy`. No entregar modal chico sin filtro/sort/page “porque el API lista”. |
| **Labels tipados** | Keys nuevas van en el objeto que importa el template (`*LABELS` o alias explícitos en `*COMPAT`). El `…spread` + `as const` a veces **no** reexpone keys al language service → TS2339; no “arreglar” borrando copy: exponer la key. |
| API de catálogo | Buscador implica `GET` de búsqueda paginada/ordenable sobre el maestro (`ts.*`); sin endpoint no hay UI done. |
| **Volver (v1.17)** | Leer `actionBtnVolver`. Si llama `set*Buscado(null)`, cancelar **vacía** el campo. Si no notifica al padre, **conserva**. Prohibido “cerrar = noop” por default. |
| **Overlay (v1.18)** | `closable="false"` HIS → `closeOnBackdrop=false`. Overlay close solo si pointerdown empezó en la máscara (no drag-select de texto). |

## Adaptación Look & Feel (sí) vs arquitectura de información (no)

**Decisión (2026-08-31):** actualizar estilos Origin Backoffice (colores, tipografía,
componentes nativos) **sin** rediseñar la disposición ni la lógica de interacción
legacy, salvo indicación explícita. Objetivo: no reentrenar usuarios.

| Permitido (L&F / DS) | Prohibido sin mandato explícito |
|----------------------|----------------------------------|
| Colores, tipografía, bordes, espaciado DS | Fusionar pantallas del menú lateral en tabs |
| Sustituir PrimeFaces por input/select/table nativos | Reordenar bloques (combos arriba → abajo distinto) |
| Anchos de botón compactos (no full-width login) | Ocultar menú lateral / cambiar jerarquía north-west-center |
| Iconos `app-icon` donde legacy era icon-only | Inventar flujos (ej. “seleccionar fila → tabs”) |
| **Dark mode** (`dark:` / tokens DS que ya lo soportan) | Estilos solo light (fondos/textos fijos sin variante oscura) |

Hospital-Web tiene **dark mode**. Al migrar pantallas: revisar en ambos temas (bordes,
inputs, tablas, menú west, popups, toasts). Preferir clases/`UI.*` del design system
con pares `dark:`; no hardcodear solo colores light. Si el estado UI se aplica por mapa
dinámico de clases, usar hooks `gt-*` + CSS `.dark` explícito — ver
[`regla-page-chrome-hospital-web.md`](regla-page-chrome-hospital-web.md) §3.

Patrón shell típico config turnos: **north** búsqueda → **west** menú de secciones →
**center** contenido de la sección activa (como `turnosServicio.xhtml`).

**Breadcrumb y título (`PageShell`):** migas = camino del **menú** (`MENU_APLICACION`),
no la carpeta de la ruta (`/configuracion/…` ≠ tile Turnos). Solo el último segmento
en negrita; **no** duplicar título con `[title]` si la miga ya lo muestra. Detalle:
[`regla-page-chrome-hospital-web.md`](regla-page-chrome-hospital-web.md).

**Menú west — no duplicar título:** si el encabezado del lateral repite el título de
página (`PageShell` / breadcrumb), **quitarlo**. El west solo lista secciones
(Datos Servicio, Prestaciones, …). Aplica a próximas pantallas con menú lateral.

El stack migrado (PageShell) adapta el chrome, **no** la arquitectura de información.

## Antes de codear (spec / Clarify)

Tabla mínima por pantalla:

| Ítem | Evidencia legacy | Valor migrado | ¿Adaptación? |
|------|------------------|---------------|--------------|
| Título / labels / botones | `msg.*` | mismo copy | no |
| Iconos acciones / Buscar | `icon`+`title` | `app-icon`+`title` | componente sí |
| Anchos botones | `style width` | magnitud + no full-width | sí (DS) |
| **Geometría filas** | `td width` / `%` / `InputWid100` / spacers | misma fila + mismas magnitudes | L&F sí; geometría **no** |
| **Dialog PF** | `width`/`height` cuerpo, tabla/`rowspan`, disabled `#dadada` | mismo; InputWid100 = celda | no (o diferido) |
| **Fuente campos chrome** | bean grilla vs GET vs vacío | inventario en SDD | no inventar API ni datos |
| Layout filtros | panelGrid | fila inputs → Buscar **como xhtml** | L&F sí; no apilar si legacy no apila |
| Grilla / paginator / sort | dataTable | … | no (o diferido) |
| Enable/disable | `disabled`/`rendered` | mismo | no |
| Buscador: filtros + cols popup | `buscador*.xhtml` | … o `diferido(slug)` | — |
| Display vs criterio (reabrir) | — | no reinyectar label compuesto | no |
| **Validaciones** (required, intervalos, condicionales) | BB + MessageBundle | UI + API mismos mensajes | no (o diferido) |
| Gaps | — | `diferido(slug)` | — |
| Padre menú / gate contexto | `MENU_APLICACION` / inicio módulo | ver regla orientación | no (o diferido) |

## Inventario copy (anti-regresión — obligatorio)

Antes de marcar tarea Web / G4 **done**:

1. Listar xhtml de la pantalla (y menú lateral si aplica).
2. Extraer cada `#{msg.xxx}` / `headerText` / `emptyMessage` / `confirm`.
3. Resolver valor en `HOSPITAL_2/.../Resources.properties` (Unicode → texto).
4. Volcar a `*-labels.ts` (o i18n del slice) **literal**; comentario `// msg.xxx`.
5. Pegar tabla en **spec** (Clarify) o **tasks** y repetir en **verify → Paridad UI**.

| msg.key | Valor properties | Constante Web | ¿En UI? |
|---------|------------------|---------------|---------|
| `turnos_por_profesional` | Turnos por Profesional | `titlePers` | sí |
| … | … | … | sí / diferido |

Gap de copy → `diferido(slug)` o corregir; **nunca** “lo dejamos más claro en español nuevo”.

## Inventario validaciones (anti-regresión — obligatorio)

Misma severidad que el inventario copy. Origen del gap: pantallas ABM “verdes” sin
bloqueos ni mensajes de `BB*` / `MessageBundle` (ej. T3 horarios → g4-3).

Antes de marcar **G4 Web done** y **handlers API done** del corte:

1. Localizar BB / action / validator del xhtml (`BBHorarioTurno*`, `BBPrestacion*`, etc.).
2. Extraer cada `FacesMessage`, `addError`, check de required / intervalo / condicional
   **y** las reglas de `ImpBus*` del insert/update (solapes, auto-cierres, copia de hijos).
3. Resolver texto en `MessageBundle` (HOSPITAL-BUSINESS) o literal del BB/ImpBus — **sin “mejorar”** el typo legacy si es el mensaje de usuario.
4. Implementar en **Web** (validators + mensajes + bloquear Aceptar / `markAllAsTouched`) **y** en **API** (handlers create **y** update; no solo UI).
5. Pegar tabla en SDD (`inventario-validaciones.md` o sección en tasks/verify) con checklist ImpBus.

Plantilla (ejemplo T3: [`turnos-horarios-grupos/inventario-validaciones.md`](../cortes/turnos/turnos-horarios-grupos/inventario-validaciones.md)):

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Campos (*) vacíos | Debe completar todos los campos requeridos. | done | done |
| Intervalo hora / vigencia | … | done / diferido | done / diferido |
| Condicional (ej. reserva si activa) | … | … | … |

| Capa | Obligación |
|------|------------|
| Web | Mismos mensajes; leyenda `(*)` si legacy la tiene; Aceptar disabled o bloqueo explícito si inválido; **feedback = toast** (MessageManager), no solo banner inline |
| API | Misma regla en create **y** update; `DomainException` / `VALIDATION_FAILED` con el texto legacy |
| Gap | `diferido(slug)` con evidencia; **nunca** silenciar |

**Prohibido:** solo validar en UI; solo validar en create; mensajes inventados (“Campo obligatorio”); dar G4 done porque el happy path guarda.

## Feedback UX (toast + confirm — anti-regresión)

Paridad de **MessageManager** / confirms JSF: el canal de feedback migrado es el
design system Web, no el browser nativo ni la página de error global.

| Caso | Legacy | Migrado (obligatorio) |
|------|--------|------------------------|
| Validación / negocio (WARN/INFO) | FacesMessage / MessageManager | **Toast** (`NotificationsService` / helpers del módulo) |
| Alta / edición / baja OK | MessageManager success | **Toast success** — no banner verde de página como único canal |
| Error API en ABM (4xx/5xx) | MessageManager error | **Toast** con mensaje API o fallback; usuario **permanece** en la pantalla |
| Confirmación de eliminar | `confirm` / `alert` / dialog JSF | **Modal** (`app-confirm-dialog` o equivalente DS) con **mismo copy** `msg.*` |
| Botones del confirm | Aceptar / Cancelar (o textos msg) | Mismos labels del inventario — no inventar “Sí, eliminar” |

**Prohibido en pantallas migradas:**

- `window.confirm` / `window.alert` / `prompt` para confirms de negocio.
- Éxito/error de guardar-eliminar **solo** con `error.set` / banner inline (banner de
  carga de lista OK; feedback de acción = toast).
- Toast correcto **y además** navegación a `/500` o `/404` por el mismo request
  (el interceptor HTTP no debe tratar como “app error page” APIs cuyo error ya
  maneja la pantalla — patrón: excluir `/v1/turnos` o header/context equivalente).

**Verify UI — filas extra:**

| Control | Estado | Nota |
|---------|--------|------|
| Feedback CRUD = toast (no solo banner) | done / diferido | MessageManager |
| Confirm eliminar = modal (no `window.confirm`) | done / diferido | copy msg.* |
| Error API no navega a /500\|/404 global | done | interceptor / opt-out |

## Ícono apagado y pedido incompleto (anti-regresión — v1.20)

**Decisión:** lo que el xhtml deja `disabled` se tiene que ver apagado, y el
botón que envía el pedido no se habilita con un id a medias. El mensaje interno
del handler no es el texto que ve el usuario.

Origen: horario de equipo, lápiz de convenio (`horarioTurnoEquipo.xhtml`,
2026-09-23). El lápiz apagado era igual al activo. **Agregar** salía con el plan
en «—» y el toast nombraba todos los ids de la clave.

| Qué se vio | Contrato |
|------------|----------|
| Lápiz o tacho `disabled` igual al activo | Misma opacidad baja que el tacho de peligro (0.4). Verificar el valor computado: un `button:disabled` sin capa puede pisar el `cursor`. |
| Botón habilitado con un combo en «—» | Apagado hasta que **cada** id que el handler exige está elegido. El `disabled` del xhtml (plus con `idConvenio` nulo) es el piso; si el handler también exige el plan, el botón espera el plan. |
| Texto tipeado en el buscador | No es un id. Al modificar el input se limpia el id elegido y el combo que depende de él vuelve a gris. |
| Toast `idHorario/nroDiaSemana/horaDesde/idConvenio/idPlanConvenio requeridos` | Texto del handler. No se publica. El botón no dispara. El handler puede seguir rechazando el pedido incompleto. |

El pie de columna de una `p:dataTable` (criterio + lupa + combo + plus) no se
clona como formulario metido en la grilla. Misma información, en este orden:
línea de contexto, fila de búsqueda, lista debajo, Aceptar centrado. El combo
hijo queda gris hasta el id padre. Es la disposición ya usada en prestaciones
de este módulo; no autoriza a reordenar el norte ni el menú lateral.

Lección: [`turnos-horarios-equipo/`](../cortes/turnos/turnos-horarios-equipo/).

## Verify UI (gate)

```markdown
## Paridad UI (xhtml)
| Control | Estado | Nota |
|---------|--------|------|
| Inventario msg.* ↔ labels.ts | done / diferido / WAIVE | tabla adjunta o link |
| Inventario validaciones BB/MessageBundle | done / diferido / WAIVE | UI + API; link inventario |
| Labels msg.* (sin inventar/acortar) | … | |
| Iconos vs texto | … | |
| Botones compactos (no full-width) | … | |
| Paginator + sort | … | |
| Enable/disable | … | |
| Layout filtros (Buscar al final) | … | |
| Buscador: filtros/cols del popup | … | o diferido(slug) |
| Buscador: reabrir no mezcla display→criterio | … | |
| **Buscador: Volver según `actionBtnVolver` (v1.17)** | … | null al padre ≠ noop |
| **Dialog overlay: pointerdown en máscara (v1.18)** | … | no cerrar por drag-select |
| **Footer `dataTable` / leyenda anclada (v1.19)** | … | no viaja con filas; no `obsTableWrap` T4 |
| Cadena padre+buscador (ambos xhtml) | … | input+Buscar+tabla seleccionados ≠ alta cod/id |
| Multi-select + batch si legacy | … | o diferido(slug) |
| Chrome dialog (size/rows/sort/filtro) | … | = xhtml, no polish |
| Dark mode (light + dark legibles) | … | `dark:` / tokens DS |
| Feedback CRUD = toast | … | no solo banner |
| Confirm eliminar = modal | … | no `window.confirm` |
| Error API sin /500\|/404 global | … | interceptor opt-out |
| Disposición vs xhtml (bloques/filtros/grilla) | … | o `diferido(slug)` documentado |
| **Geometría vs xhtml (cols/anchos/altura/misma fila)** | … | DoD v1.9; no “estructura OK” |
| **Dialog PF (height=cuerpo, tabla+rowspan, InputWid100=celda, disabled `#dadada`)** | … | DoD v1.13; no 111px ni scroll interno |
| **Chrome dialog: fuente de cada campo** | … | grilla / GET al abrir / vacío diferido |
| **Calendario `p:calendar` (cabecera locale + selected con foco)** | … | DoD v1.14; no solo números; no copiar Lu-first de otra pantalla |
| **Checks HIS (hermanos, no ngModel `form[key]` en `@for`)** | … | T5.3; no label wrapping |
| Excepciones pre-smoke listadas en SDD | … | sin silencios |
```

## Gate de arranque (agentes) + Gate pre-smoke

**Arranque (antes de codear UI):** skill `gate-ui-arranque` + rules alwaysApply.
Orden fijo: xhtml → inventarios/geometría → template del slice → API → verify → smoke.

**Pre-smoke (antes de probar):**

1. Inventarios copy + validaciones.
2. Implementación UI **con paridad de disposición/interacción/geometría** respecto
   del xhtml (L&F = DS; no reusar otro patrón Web —ej. buscador HAB apilado— si el
   xhtml usa tabla/`panelGrid` o combos en cascada, salvo `diferido(slug)`).
3. Documentar excepciones en tasks/verify (`diferido` / WAIVE con evidencia).
4. **Recién entonces** solicitar o correr smoke UI/ops.

**Prohibido:** pedir smoke, marcar G5/Web done o “listo para probar” con layout/
interacción/geometría distinta al legacy sin excepción remarcada en el SDD.
**Prohibido:** empezar por Resource/UseCase y dejar la UI “para después”.

**PASS UI** exige inventario copy **y** inventario validaciones **done** (o diferidos
explícitos) **y** disposición **+ geometría** alineadas o excepciones explícitas.
API verde ≠ UI done. Happy path que guarda ≠ validaciones done. Smoke no sustituye
el gate de paridad.

Si el corte toca menú/home: sección **Paridad orientación** en
[`regla-paridad-orientacion-visual.md`](regla-paridad-orientacion-visual.md).

## Deuda retroactiva (pantallas pre-gate)

Pantallas migradas **antes de T2 habilitación turnos** (y T2/buscadores cerrados
antes de v1.4) **no** tienen inventario validaciones. Queda registrado como deuda
explícita — no silencio ni WAIVE:

→ [`deuda-validaciones-pre-hab-turnos.md`](../estado/deuda-validaciones-pre-hab-turnos.md)

Al retocar cualquiera de esas pantallas: aplicar este gate o `diferido(slug)`.

## Reglas agente

- Hospital-Web: `.cursor/rules/hospital-web-paridad-ui-legacy.mdc` · `hospital-web-buscadores.mdc`
- Hospital-Api: `.cursor/rules/hospital-api-paridad-validaciones.mdc`
- Programa: este archivo + Clarify #7 del proceso SDD
- Migration docs: `.cursor/rules/hospital-paridad-ui.mdc`
- Deuda pre-T2: [`deuda-validaciones-pre-hab-turnos.md`](../estado/deuda-validaciones-pre-hab-turnos.md)
