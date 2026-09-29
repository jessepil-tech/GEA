---
title: Propuesta — gobierno de la documentación del programa
version: 1.0.0
status: proposed
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.propuesta-gobierno-documentacion
---

# Propuesta: cómo dejar de perder el control de la documentación

**Estado: P1–P6 ejecutados el 2026-09-15** (P5/P5-C y P6 más temprano el mismo día; P1–P4 esta tanda). P3 cubre los hubs y todo `docs/canon/*.md`; el resto de los ~80 archivos sin front-matter (READMEs de corte, árboles dump) sigue pendiente.

## Diagnóstico, medido

| | |
|--|--|
| Documentos `.md` | **390** |
| Dentro de 72 slugs | 336 · en `docs/sdd/` raíz **39** · en `docs/` raíz 15 |
| Links internos entre documentos | **1.352** |
| Sin front-matter | **96 (25 %)**, y **8 de los 10 documentos más citados** |
| Fuera del índice de entrada | 283 (72 %), incluidas **dos piezas que el canon declara canónicas** |
| Slugs desconectados del grafo | 5, de los cuales **4 son spikes de golden master** |
| Avance de la migración | ~6 % |

Lo que hace daño no es el volumen: los links están sanos (2 roto de 1.352) y el front-matter,
donde existe, es consistente. Los tres problemas reales son otros.

### 1. El índice de entrada es la fuente menos fiable

Tres afirmaciones falsas, ya corregidas al detectarlas: decía que el linter audita «los 68»
slugs cuando son 72; que el dump de menú estaba **pendiente** cuando se cerró el 31 de agosto y
produjo cuatro artefactos; y que hay «3/37 módulos con A–C» cuando el documento dueño del dato
dice 2 de 37 más un satélite, y además **advierte explícitamente** contra escribir 3/37.

Y dos artefactos que el canon declara canónicos no se alcanzan desde el índice:
`regla-playwright-migracion.md` (capa 4, con Team Rule propia) y
`relevamiento-his-inventario-global/` (capa 3b, sede del tablero de 16 streams).

### 2. Ninguna cifra tiene dueño, así que todas derivan

El caso más caro son los denominadores con los que se mide el avance:

| Magnitud | Valores que circulan | Dónde |
|----------|----------------------|-------|
| Hojas de menú del HIS | **1.032** · **~1.200** · **1.240** | 11 documentos; seis llaman «hojas» al 1.240, que el inventario define como **filas** |
| Archivos Java del legacy | 9.458 · 9.459 · **9.462** | los tres inventarios, medidos con un día de diferencia |
| xhtml en menú | 935 · 941 · 1.032 | ídem |
| Reportes BIRT invocados | 231 · 250 | ídem |
| Packages de dominio | 61 · **62** | dentro del **mismo** archivo (`dossier-migracion.md`), y 61 en el inventario |

El de packages ya lo resolví midiendo: hay **326** package bodies, 264 de auditoría y **62** de
dominio, así que corregí el inventario. El de hojas de menú es el más serio, porque con tres
denominadores el porcentaje de avance de UI varía cerca de un 20 % según qué documento lea
quien lo calcule. Y es exactamente lo que el propio inventario pide evitar: «no se cita un % del
HIS sin decir el denominador».

Un matiz que vale la pena, porque muestra el costo de no tener dueño: la auditoría reportó como
error el «264 tablas auditadas», sospechando que era un conteo de packages disfrazado de tablas.
Fui a medirlo y **el número está bien**: hay 264 packages `TBL_AUD_*`, uno por tabla, relación 1
a 1. Pero nadie podía dirimirlo leyendo la documentación, y de paso apareció lo que sí faltaba:
de los **1.419 triggers** de auditoría del legacy, 1.155 solo estampan el sello de último
cambio. O sea que el dato no estaba mal, estaba **incompleto**, y no había dónde ir a
verificarlo.

### 3. El canon está escrito cuatro veces y las copias ya divergieron

Cada regla existe como fuente (`regla-<tema>.md`), como fila en `gobierno-migracion.md`, como
paso en `loop-migracion-corte.md` y **reescrita completa** en `cursor-team-rules.md`. La regla
de escritura con `id` aparece cinco veces con cinco redacciones.

La divergencia ya ocurrió: `regla-paridad-ui-legacy.md` está en **v1.14** y tres documentos la
fijan en «v1.10+», con uno congelando «geometría = DoD v1.9» cuando v1.13 redefinió justamente
la geometría de dialogs. El documento que describe la regla describe una regla que ya cambió
dos veces.

## Propuesta

Seis piezas, en orden de impacto. Las tres primeras se pueden hacer sin mover un solo archivo.

### P1. Cada dato tiene un dueño, y los demás citan

La pieza que resuelve el problema 2, y la única que evita que vuelva.

Un registro corto —`datos-canonicos.md`— con una fila por magnitud del programa: qué mide, cuál
es el valor vigente, **qué documento es su dueño** y cómo se remide. Regla asociada: un
documento que no es dueño de un dato **no repite la cifra**, la cita con link. Si necesita el
número en prosa, lo escribe una vez y al lado el link al dueño.

Con eso, «hojas de menú» pasa a tener un solo valor y una sola unidad, y actualizar el dato es
editar un lugar.

### P2. El índice se genera, no se escribe

El índice mintió tres veces porque es una tabla a mano que hay que acordarse de actualizar. La
solución es la que ya usamos para el índice del legacy: generarlo desde el front-matter, con
`docs/README.md` como salida y no como fuente. Un documento nuevo aparece en el índice porque
tiene front-matter, no porque alguien lo agregó a mano.

Efecto colateral: los documentos sin front-matter dejan de ser invisibles, porque no aparecer en
el índice se vuelve un error del linter.

### P3. Front-matter obligatorio y estado legible por máquina

Los 96 documentos sin front-matter se completan, empezando por los diez hubs. El estado deja de
declararse en prosa: hoy `gobierno-migracion.md` dice **CANÓNICO** en negrita y
`regla-waiver-paridad-legacy.md` dice **CANÓNICA**, y ninguna de las dos formas la lee el
linter, así que los tres documentos más citados del programa figuran como «sin status».

Vocabulario cerrado, y se elimina el `done` huérfano que convive con `gate-done`. Se agregan dos
campos que hoy faltan y explican por qué nadie sabe si un documento sigue valiendo:
`reemplaza_a` y `reemplazado_por`.

### P4. El canon se escribe una vez

`gobierno-migracion.md` y `loop-migracion-corte.md` se quedan con **una línea y un link** por
regla, en vez de la paráfrasis. `cursor-team-rules.md` es el caso especial: existe porque hay
que pegar texto en el dashboard de Cursor, así que ahí la copia es inevitable —pero pasa a
**generarse** desde las reglas fuente, con un encabezado que diga de qué versión salió.

Y una regla de cita que evita el problema de la v1.10: **no se citan números de versión** de una
regla, salvo que el requisito sea literalmente esa versión. Se cita la regla y su sección.

### P5. Taxonomía por naturaleza

Hoy `docs/sdd/` es un cajón con 39 archivos sueltos y 72 carpetas al mismo nivel, y el nombre no
dice de qué tipo es cada cosa: las cinco piezas de proceso llevan cinco prefijos distintos.

```
docs/
  README.md          ← generado (P2)
  canon/             13 reglas + 5 procesos
  planificacion/     backlog · alcance · trabajo paralelo · cutover
  relevamiento/      los 7 relevamientos + índice generado del legacy
  estado/            snapshots y deudas
  arquitectura/      los 12 análisis de docs/ raíz
  cortes/<stream>/   los slugs de trabajo (turnos · anunciador · recepcion · plataforma)
  spikes/            los 7 spikes de golden master
```

Beneficio concreto además del orden: los 4 spikes de golden master que hoy están fuera del grafo
dejan de perderse, y son la evidencia que la regla de paridad de lógica exige.

### P6. Idioma: español, sin capa espejo — **hecho** 2026-09-15

Narrativa en español; el inglés queda para código, identificadores y
`spec.md` / `plan.md` / `tasks.md` / `verify-report.md`.

Glosario: [`glosario-migracion.md`](../canon/glosario-migracion.md). Los tres pares:

| Par | Resolución |
|-----|------------|
| corte / slice | **corte** en prosa; `slice` solo en nombres ya publicados (`abrir-sdd-slice`) |
| stream / carril | **stream** = dominio técnico; **carril** = una persona trabajando en él |
| gate / compuerta | **gate** = estado del corte; **compuerta** = linter / hook |

## Alcance: tres variantes, con su precio

La reorganización de P5 es la única pieza cara, porque las referencias no están solo en `docs/`:

| | Qué toca | Riesgo |
|--|----------|--------|
| **A. Completa** | 1.352 links internos + 69 referencias en reglas, skills y scripts + referencias en `Hospital-Api`, `Hospital-Web` y el workspace + **volver a pegar las Team Rules** del dashboard (no están en git) | Alto en un paso, bajo si se verifica con el linter de links |
| **B. Con punteros** | Igual, pero deja un archivo de una línea en cada ruta vieja apuntando a la nueva | Menor: nada se rompe aunque quede una referencia sin actualizar. Cuesta 39 archivos puente |
| **C. Sin mover** | Solo P1–P4 y P6. La estructura se aplica a lo nuevo | Nulo. El cajón sigue creciendo, aunque más lento |

Mi recomendación es **B**, y hacerla en un solo cambio verificable: el linter de links ya existe
y puede comprobar las 1.352 aristas antes y después. Los puentes se borran cuando el linter
confirme que nadie los usa.

## Plan de ejecución

Etapas independientes, cada una útil sola:

1. **P1 + los datos ya medidos.** Registro de datos canónicos y unificación del denominador de
   hojas de menú, que es el que distorsiona el avance. Sin mover archivos.
2. **P3.** Front-matter en los 96, empezando por los 10 hubs. Sin mover archivos.
3. **P2.** Índice generado + chequeo de cobertura en el linter. A partir de acá, un documento
   fuera del índice es un `FAIL`.
4. **P6.** Glosario y los tres pares resueltos.
5. **P4.** Reducir gobierno y loop a punteros; generar las Team Rules.
6. **P5** con la variante elegida.

## Lo que esta propuesta no resuelve

- **La redundancia de las Team Rules** no desaparece: mientras el dashboard requiera texto
  pegado, hay una copia. Se mitiga generándola, no se elimina.
- **Los tres inventarios del legacy** seguirán midiendo distinto, porque uno es script y otro es
  recorrido manual. Lo que P1 arregla es que se sepa **cuál manda** para cada dato; hoy el
  índice generado reconoce la discrepancia y la deja abierta.
- **La deriva de contenido** (un documento que ya no describe la realidad) no la detecta ningún
  linter. Para eso sirve `reemplazado_por` y la revisión al cerrar cada corte.
