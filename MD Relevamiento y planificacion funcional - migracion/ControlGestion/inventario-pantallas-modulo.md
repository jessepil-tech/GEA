# Inventario de pantallas de un módulo legacy

Recorrer el menú de un módulo del HIS (JSF / PrimeFaces) y dejar cada pantalla en un Excel. Referencias hechas: módulo Turnos (`c:\Proyectos\GEA Cursor\ModuloTurnos_Pantallas.xlsx`) y **Administración General** (`c:\Proyectos\GEA Cursor\ModuloAdministracionGeneral_Pantallas.xlsx`, 658 filas, 8 ramas). Administración General es la mejor referencia del patrón de menú lateral por registro: aparece en casi todas las hojas y trae todas las variantes de buscador.

## Entregable

Un `.xlsx` por módulo, junto a los demás inventarios (`c:\Proyectos\GEA Cursor\Modulo<Nombre>_Pantallas.xlsx`).

Hoja `Hoja1`. Una fila por pantalla. Columnas, en este orden:

| Modulo | menu nivel 1 | menu nivel 2 | menu nivel 3 | menu nivel 4 | url |
| --- | --- | --- | --- | --- | --- |

- **Modulo** es siempre el nombre del módulo (Turnos, no el título de la home).
- La fila del ítem de menú superior lleva los niveles más profundos vacíos y la URL a la que abre.
- Lo que aparece recién después de elegir un registro (servicio, profesional, paciente) va un nivel más abajo, debajo de esa fila. No se borra la fila padre.
- Ítem visible pero deshabilitado (`ui-state-disabled`, `onclick` `return false`): se lista igual, con la URL vacía. No se inventa una URL.
- **Columna `menu nivel 5`:** el formato base llega a nivel 4. Pero si un ítem de menú ya está en nivel 4 y al elegir un registro abre su propio menú lateral, esas sub‑pantallas caen en nivel 5. Pasó en Administración General → Módulos (Turnos>Configuración>Turnos por Servicio>…, Depósito>Farmacia/Items/Stock>…). En ese caso agregar la columna `menu nivel 5` **antes** de `url` (queda `Modulo | menu nivel 1..5 | url`); las filas que no llegan a nivel 5 la dejan vacía. Si el módulo no anida tan hondo, no agregar la columna.
- **Ítem que redirige a `login.faces`/logout al hacer clic** (falta de permiso del usuario de prueba, determinístico): dejar en la celda `url` el texto `Redirige a logout al hacer click` en **fuente roja**. No es un drop de sesión: pasa siempre y con cualquier registro. Necesita un usuario con más permisos para capturarla.
- **Tabla vacía** (el buscador no trae filas con ningún término): no hay registro que elegir → el lateral queda deshabilitado y sus hojas van con URL vacía. Anotarlo.
- Acentos reales en el Excel. La consola de PowerShell los muestra mal; verificar exportando celdas a un `.txt` UTF-8, o releyendo el `.xlsx` (unzip → `sharedStrings.xml`).

Ejemplo (Agenda Simplificada, paciente con documento 30971691):

| menu nivel 2 | menu nivel 3 | menu nivel 4 | url |
| --- | --- | --- | --- |
| Agenda Simplificada | | | `.../turnos/asignacionTurnos/agenda.faces` |
| Agenda Simplificada | Paciente | Datos del Paciente | `.../datosPaciente.faces` |
| Agenda Simplificada | Paciente | Grupo Familiar | |
| Agenda Simplificada | Agenda Turnos | Historial Turnos | `.../historialTurno.faces` |

## Cómo entrar

1. Login en `http://10.0.0.28:29300/HOSPITAL/pages/login.faces` (Usuario, Contraseña, Ingresar).
2. Abrir `http://10.0.0.28:29300/HOSPITAL/pages/inicio.faces` y hacer clic en el **tile del módulo**. Una URL directa a `.../<modulo>/inicio.faces` puede dejar `#menuform ul.layout-menu` vacío.
3. Confirmar que `#menuform li` tiene más de unos pocos ítems antes de recorrer.
4. Sin sesión, el HIS manda a `access-denied.faces`. Si aparece «La sesión está por expirar» o vuelve a `login.faces`: entrar de nuevo y repetir el tile.

Credenciales y paciente de prueba los da quien pide el inventario. No volcar en el Excel ni en el chat teléfono, mail, número de afiliado ni documentos de otras personas.

**Estabilidad del server (importante).** El HIS en `10.0.0.28:29300` es inestable: se cae/reinicia solo (`ERR_CONNECTION_REFUSED` aunque el host responda al ping) y tira las sesiones. La sesión además dura poco (~2 selecciones de registro). Consecuencias para el relevo:

- Es **sesión única por usuario**: no correr dos capturas contra el server en paralelo, se pisan.
- En recorridos largos la sesión se agota a mitad. Detectar `login.faces` en la URL y **re‑loguear + rehacer la selección del registro** para continuar. No confundir este drop con el logout por permiso (ese es determinístico, ver arriba).
- Los menús laterales enormes conviene capturarlos **aparte, con sesión fresca** (en Administración General: Convenio con 71 sub‑pantallas y Auditoría Convenio con 57). Los ítems que fallan por drop se reintentan **por índice** (seleccionar una vez y clickear solo los que faltan).
- Login por Playwright: el POST rebota a `login.faces` y recién después redirige a `inicio.faces`. Esperar con un poll de `page.url()` desde Node (sobrevive las navegaciones), no con `waitForNavigation`.
- El id `menu_NNNNN` del menú superior lo regenera JSF: **cambia con cada reinicio del server**. Volver a volcar el menú al reanudar.

## Menú superior

- Contenedor: `#menuform`.
- Hoja: `onclick` con `PrimeFaces.ab({s:"menuform:menu_NNNNN",...})` y `href="#"`. El padre del desplegable no dispara ajax.
- El `li` está oculto hasta el hover. Clic con `a.click()` / `evaluate`, selector `[id="menuform:menu_XXXX"]`, espera `attached`. No usar una espera de visible.
- Registrar la URL final (`location.href`) cuando `document.title` ya no empieza por `Loading`. El título `Loading http://...faces` es el destino de la navegación en curso.
- La home del módulo (en Turnos, `turnos/inicio.faces`, título «Atención Turno») no es un ítem de menú: no lleva fila.
- Pantallas hermanas (Excel, ticket, otro diseño) se distinguen por el botón que las abre. No mezclarlas en la misma fila.

## Menú que solo aparece al elegir un registro

Es el patrón dominante: **casi toda pantalla de ABM tiene un menú lateral "Opciones" (`#formMenuLateral`) que se habilita recién al buscar y seleccionar un registro.** Sin registro, todos sus ítems están deshabilitados. Cada ítem lateral es una sub‑pantalla que va **un nivel más abajo** que la hoja (hoja en nivel 2 → laterales en nivel 3; hoja en nivel 3 → laterales en nivel 4; hoja en nivel 4 → laterales en nivel 5).

Flujo:

1. Abrir la pantalla (la fila padre se queda, con su URL de búsqueda/inicio y niveles profundos vacíos).
2. Clic en el botón **Buscar** de la pantalla → abre el buscador (ver variantes abajo). Escribir el término, buscar, y **seleccionar la primera fila con contenido real** (saltear filas vacías o «`.`»).
3. Recién ahí `#formMenuLateral` se habilita y la URL suele pasar a `datos<X>.faces`. Leer los ítems: cada `<a>` sin `ui-state-disabled` tiene su `PrimeFaces.ab`; clickearlo navega a su `.faces`.
4. El **primer ítem lateral suele ser la misma pantalla de datos** (`datos<X>.faces`) — se lista igual (no es duplicado de la fila padre, que es la de búsqueda).
5. Ítem lateral que queda **deshabilitado para el registro elegido** → fila con URL vacía (no inventar). En la base de prueba, ítems que dependen de un dato que no existe quedan siempre deshabilitados (ej. en Código Prestación, «Códigos … Asociadas» y «Genérico Asociado» porque solo hay prestaciones SIMPLE).
6. Si el menú lateral tiene **un solo ítem y es la misma pantalla** (habilitación), no duplicar la fila.
7. Filtros, calendario, tabs internas o ficha no son otro nivel de menú. **Pero** varios ítems laterales que recargan la misma `.faces` (cambia solo el resaltado o el contenido por ajax) **sí se listan**, con la URL repetida — es correcto (visto en Paciente y en Interfaces Migración → Tablas De Conversión, donde 8 tabs comparten `tablasConversion.faces`).

### Variantes de buscador

El botón Buscar de la pantalla dispara un `PrimeFaces.ab`. El buscador puede ser un **diálogo popup** (`ui-dialog`, id tipo `popUpBuscador*` / `buscador*`) **o un formulario embebido** en la página (id `formBuscador*`, sin `ui-dialog`). Localizarlo genérico: contenedor **visible** cuyo id contiene `uscador` y que tiene un `input[type=text]`.

- **Término de búsqueda:** varía por pantalla. Probar en orden: unas pocas letras, **exactamente 3 letras** (Código C.I.E./Diagnóstico solo trae filas con 3, ej. `ter`), y el comodín `+++` (funciona en varios buscadores como asterisco, pero **no en todos** — en el de Código Prestación por DESCRIPCIÓN da 0). Tener una lista de términos de fallback (`A,E,O,ABD,CON,PRE,SER,…,+++`) y quedarse con el primero que trae filas.
- **Dropdowns previos:** algunos buscadores exigen setear un `selectonemenu` **antes** de buscar. Guía Médica Convenio: elegir **Convenio** (ej. GALENO) y recién ahí `+++` en Guía Médica. Regla útil: setear cualquier `selectonemenu` del buscador cuyo label esté vacío/«Seleccione» al primer valor real.
- **Input correcto:** ignorar los inputs `…_focus` (proxies de selectonemenu). Tomar el `input[type=text]` visible que no termina en `_focus`. **Limpiar el campo antes de tipear** (Ctrl+A/Delete): a veces queda precargado el registro anterior y el texto se concatena.
- **Selección de la fila:** preferir el `<a>` commandlink de la fila (`a[onclick]` / `a.ui-commandlink`, ej. el código en Código C.I.E.); si no hay, clic en un `td`. Algunas datatables necesitan **clic real de Playwright** (no `.click()` por JS) para que PrimeFaces registre la selección. Si aún así no habilita el lateral ni navega a `datos<X>.faces`, la selección no se pudo automatizar (pedir las URLs a mano) — pasó en Facturación → Padrones → Formato Archivo Padrón Convenio.
- **Overlay `#LoadingDialog_modal`:** aparece durante el ajax y bloquea los clics de Playwright. Esperar a que se oculte (`offsetParent` nulo) antes de interactuar, o clickear por `evaluate`.
- **Lateral ya habilitado sin registro:** excepción (Interfaces Migración → Tablas De Conversión) — el lateral viene enabled al cargar; se recorre directo, sin buscar.

Varias hojas de Paciente en Agenda Simplificada recargan `datosPaciente.faces` y solo cambia el resaltado (`ui-state-highlight`). La URL se repite. Es correcto. Confirmar el resaltado con la página quieta, de a un ítem.

## Buscar paciente (Agenda)

En `asignacionTurnos/agenda.faces`:

- Buscar abre `#popUpBuscadorPaciente`. Documento: input con etiqueta «Nro. Documento».
- El botón Buscar de PrimeFaces procesa solo `@this` y no manda el documento. Hay que disparar `PrimeFaces.ab` procesando el formulario completo del buscador.
- La tabla es `PrimeFaces.widgets` de `tablaPacientes`. Clic en el primer `td` de la fila.
- No cerrar con Cancelar: limpia el paciente y vuelve a deshabilitar el menú. Si el diálogo queda abierto, ocultarlo con el widget del popup.
- Con el paciente ya cargado, el mismo botón abre «Paciente Existente». Cerrar con Volver. No pulsar Aceptar, Eliminar ni Suspender.

## URL que da 404

Si el ítem de menú cae en 404 (en Turnos, Agenda Simplificada iba a `pages/agenda/inicioAgenda.faces` y redirigía a `/null/loginByPass.faces`), no abortar `**/null/**` en el browser: eso corta los `goto` siguientes. Anotar la URL rota y, si quien pide el inventario da la URL correcta, recorrer **esa** pantalla y reemplazar la URL de la fila padre.

## Qué no hacer

- No inventar URLs, textos de pantalla ni filas para completar el árbol.
- No confirmar acciones que escriben o borran (Aceptar del paciente, Eliminar, Suspender).
- No encadenar muchos clics en un solo script. El tope del browser (~60 s) deja el script corriendo y el siguiente clic pisa la navegación. Un ítem, esperar título estable, leer URL y resaltado, seguir.
- No usar `setTimeout` dentro del snippet de Playwright. Usar `page.waitForTimeout`.
- En esta máquina `python` / `py` no están. Escribir el Excel por COM de Excel. Leer y escribir JSON en UTF-8 con `[System.IO.File]`, no confiar en la consola. Borrar el JSON auxiliar al terminar.
- `target/` del repo de reportes no es el lugar del inventario.

## Escribir el Excel

- Por COM de Excel (no hay Python). Leer las filas de un JSON UTF‑8 con `[System.IO.File]::ReadAllText`; no pasar los textos por la consola (rompe acentos). Backup del `.xlsx` antes de cada escritura y chequear que no esté abierto (lock `~$...xlsx`).
- Escribir cada rama en su bloque y en orden de menú; verificar releyendo el `.xlsx`.
- Si hace falta `menu nivel 5`: insertar una columna antes de `url` por COM (`$ws.Columns.Item(6).Insert()` corre `url` de F a G), poner el header `menu nivel 5`, y a partir de ahí escribir 7 columnas.
- Celdas de logout: dejar el texto y pintarlas con `$cell.Font.Color = 255` (rojo).

## Deducir y completar desde el fuente legacy

El fuente está en `C:\Proyectos\GEA Cursor\Hospital-Legacy\Hospital-Legacy`. Webapp: **HOSPITAL_2** (`WebRoot/pages/**.xhtml` + backing beans `src/ar/com/thinksoft/beans/**/BB*.java`); lógica de negocio en **HOSPITAL-BUSINESS**. Con esto se explican y se resuelven los pendientes sin depender del ambiente de prueba.

**Por qué te lleva a `login.faces`** (`WebRoot/WEB-INF/web.xml`):
```
<error-page><exception-type>javax.faces.application.ViewExpiredException</exception-type>
  <location>/pages/login.faces?faces-redirect=true</location></error-page>
<error-page><exception-type>java.lang.Throwable</exception-type>
  <location>/pages/login.faces</location></error-page>
```
Cualquier **excepción no atrapada** (o vista/sesión expirada) redirige a login. O sea, cuando un clic al lateral cae en login, o (a) se expiró la sesión, o (b) **el bean de esa pantalla explota al abrirse** (falta un dato en el ambiente o hay un bug). **No es falta de permiso**: el permiso lo maneja `SecurityFilter` (solo sobre GET, con `Seguridad.tienePermiso`) y, si falta, manda a **`access-denied.faces`**, no a login. → La pantalla existe igual; su URL está en el fuente.

**Por qué no se habilita el menú lateral**: cada `<p:menuitem>` tiene `disabled="#{...entidad eq null}"`. El lateral se habilita solo cuando el bean tiene la entidad cargada, y a veces esa entidad requiere un dato que el registro elegido no tiene. Ej. Padrones → Formato Archivo Padrón Convenio: `disabled="#{bb.fmtArchPadronConv.fmtArchPadronConv eq null}"` → hace falta un convenio que **ya tenga un formato de padrón cargado**; los de prueba no lo tienen. O directamente la **tabla está vacía** (buscador sin filas). Es condición de dato, no bug.

**Completar las URLs pendientes desde el fuente (sin ambiente):** la ruta de cada sub‑pantalla está literal en el `action` del `<p:menuitem>` (`action="#{bb.actionSelectMenu(..., '/pages/.../X.faces')}"`). Método:
1. Ubicar el `.xhtml` de la pantalla (misma ruta que su `.faces`: `WebRoot/pages/.../<pantalla>.xhtml`). Si el menú no aparece ahí, la pantalla lo incluye aparte: buscar `<ui:include src="./menuLateral<X>.xhtml"/>` y leer ese archivo.
2. Extraer los `/pages/**.faces` de cada `<p:menuitem>` **en orden** (contemplar menuitems auto‑cerrados `/>` y con cuerpo `</p:menuitem>`).
3. **Alinear por índice** con los ítems del lateral y **verificar** contra los ya capturados en vivo (el basename `.faces` debe coincidir). Si alinean, completar los vacíos con la `.faces` de su índice. Si no alinean (menú partido en includes, ítems condicionales), usar el método de diferencia: la `.faces` del fuente que **no** está entre las ya capturadas es la que falta (funcionó para Convenio: 71 en `menuLateralConvenio.xhtml`, 70 capturadas → 1 faltante).
4. La URL capturada en vivo (leyendo `location.href`) es la autoritativa; el `action` del fuente coincide salvo lógica dinámica. Ante duda, priorizar la capturada.

En Administración General así se completaron las 38 celdas que el ambiente no dejaba abrir (tablas vacías, condición de dato, o bean que falla). Las 6 de "bean falla → login" se dejaron con su URL real pero en **rojo** (existen pero fallan en este ambiente); las de dato/tabla vacía, en negro.

## Cierre

Decir la ruta del `.xlsx`, cuántas filas quedaron, qué hojas comparten URL, cuáles tienen `.faces` propio, cuáles quedaron **deshabilitadas para el registro elegido** o **por tabla vacía**, y cuáles **redirigen a login por bean que falla** (URL en rojo). Si algo no se pudo abrir en el ambiente, completar la URL **desde el fuente** (sección anterior) en vez de dejarla vacía.
