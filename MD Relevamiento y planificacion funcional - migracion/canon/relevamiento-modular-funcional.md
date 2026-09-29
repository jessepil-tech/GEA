---
title: Proceso — Relevamiento modular / funcional
status: canonical
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.relevamiento-modular-funcional
indice_blurb: Relevamiento modular (config→maestros→operación) — capa 3
---
# Proceso — Relevamiento modular / funcional

`phase_id:` **`sdd.hospital.relevamiento-modular-funcional`**  
Fecha: **2026-08-26**  
**Estado:** **CANÓNICO** — capa 3 de [`gobierno-migracion.md`](gobierno-migracion.md)

Analiza un módulo **antes** de declarar viabilidad, priorizarlo o abrir SDD de
código. Completa (no reemplaza) las capas 1–2 (paridad negocio + DDL) y alimenta
la capa 4 ([`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md)).

---

## Problema que resuelve

Sesgo recurrente: analizar solo el **happy path operativo** (grilla del día, TV,
tótem que recibe) y tratar maestros, perfiles, ABM y habilitación como “seed”
o “después”.

| Caso observado | Prioridad incorrecta | Hueco típico |
|----------------|----------------------|--------------|
| Anunciador / Tótem | Llamar → TV / recepción | Config, terminal, ambientes, permisos, ABM |
| Turnos / agenda | Grilla u operación del día | Personal → roles/perfiles → habilitar → horarios → generar → agenda |

**Regla:** el relevamiento empieza por el **pipeline que da vida a la función**,
no por la punta visible.

---

## Cuándo es obligatorio

Antes de:

1. Responder “¿conviene migrar el módulo X?” / “¿es viable?”
2. Abrir un SDD de implementación (`spec` / `plan` / cortes CU)
3. Priorizar el módulo en backlog

Sin relevamiento vigente (o parcial **fechado y aceptado** por producto) → no se
declara factible con confianza ni se recomienda corte de código.

---

## Entregable

```text
docs/sdd/relevamiento-<modulo>/
  README.md      ← veredicto + índice + profundidad de análisis
  pipeline.md    ← etapas configuración → operación
  inventario.md  ← capacidades + pantallas + SP + tablas
  maestros.md    ← maestros / seguridad / prerrequisitos
  matriz.md      ← migrado | parcial | diferido(slug) | WAIVE | N/A
  cortes.md      ← CUs ordenados (solo después del inventario)
```

Si ya existe un relevamiento del dominio, **ampliarlo** (`pipeline.md` +
`maestros.md`) en lugar de abrir otra carpeta paralela.

---

## Fases (orden fijo)

### Fase A — Guion funcional

Recorrido de quien **configura y opera** el módulo (demo de negocio, o
reconstrucción desde xhtml / menú / manual).

| # | Etapa | Pregunta |
|---|-------|----------|
| A1 | Identidad / personal | ¿Quién debe existir? ¿Cómo se da de alta? |
| A2 | Roles vs perfiles | ¿Qué permiso abre el módulo / la acción? |
| A2b | Orientación | ¿Padre en `MENU_APLICACION` (no path xhtml)? ¿Gate centro/puesto/call center? |
| A3 | Maestros de negocio | Servicios, prestaciones, centros, convenios, … |
| A4 | Habilitación / vínculo | ¿Qué “enciende” el objeto para el módulo? |
| A5 | Parámetros / reglas | Horarios, grupos, límites, diccionarios |
| A6 | Generación / materialización | ¿Hay un paso que crea filas operativas? |
| A7 | Operación diaria | Agenda, otorgar, llamar, TV, consulta |
| A8 | Ciclo de vida | Anular, liberar, suspender, caducar, reasignar |
| A9 | Side-effects | PDF, WS, mail, otras tablas, BIRT |

**PASS:** ninguna etapa A1–A9 en silencio (cubierta / N/A con evidencia / diferida).

### Fase B — Evidencia legacy

Por cada etapa A: pantallas, bean/API, package/SP, tablas `ts.*`, estado en
Flyway / PG migrado. Prohibido inventar tablas `public` “para el análisis”.

### Fase C — Maestros y seguridad

| Maestro / permiso | ¿Bloquea operación si falta? | ¿Existe CU/SDD? | Estado |
|-------------------|------------------------------|-----------------|--------|
| … | Sí/No | path o “ninguno” | … |

Incluir siempre: personal/usuarios, roles/perfiles (frontera Identity),
catálogos que la UI valida, vínculos de habilitación del módulo.

### Profundidad de análisis (entre C y D)

Las Fases A–C responden **qué hace el módulo**. No responden **cuánto del grafo se
recorrió** para afirmarlo, y esa diferencia decide si los cortes de la Fase D se apoyan
en dependencias o en la secuencia visible de pantallas.

El pipeline A1–A9 es un grafo de prerrequisito funcional: profundidad **media**. Alcanza
para el tronco de cortes (maestros → habilitación → generación → operación) y **no**
alcanza para el comportamiento especializado, que vive en otras capas: el package, los
procesos programados, las FK y las integraciones. Un A–C que no dice hasta dónde llegó se
lee como cerrado, y el corte hijo hereda una certeza que nadie produjo.

Por eso el `README.md` del relevamiento lleva una sección con **nombre fijo**, con el mismo
régimen que A1–A9: nada en silencio.

```markdown
## Profundidad de análisis

| Capa | Qué cerraría | Estado | Evidencia |
|------|--------------|--------|-----------|
| Pipeline A1–A9 | Etapas configuración → operación | cerrado | `pipeline.md` |
| Escritores cruzados | Quién más escribe las tablas del módulo | muestra | … |
| Procesos programados | Cada job se porta / difiere / N/A | cerrado | `--jobs <dominio>` |
| Firmas del package | Universo de funciones del BODY, no una selección | muestra | … |
| Integridad referencial | FK diferidas y padres reales | muestra | … |
| Reportes e integraciones | BIRT del dominio y WS de terceros | N/A | … |
```

**Estados válidos** (los mismos cuatro, sin sinónimos):

| Estado | Significa |
|--------|-----------|
| `cerrado` | La capa se recorrió entera y la evidencia está en el entregable |
| `muestra` | Se vio una parte representativa; **el denominador sigue abierto** |
| `diferido(slug)` | No se recorrió y hay un corte que lo va a hacer |
| `N/A` | La capa no aplica a este módulo, con motivo |

**`muestra` es un resultado legítimo.** No se exige el call graph del package ni el grafo de
FK para abrir un A–C: se exige **declararlo**. Bloquear el relevamiento hasta tener el grafo
completo recrea el anti-patrón «el package es el módulo» y congela los módulos sin owner.

Lo que `muestra` sí prohíbe es tratar el stream como analizado en profundidad: ni el
veredicto de la Fase E ni el `cortes.md` pueden apoyarse en una capa que no se cerró. Se
cobra por corte, al firmar el universo ([`loop-migracion-corte.md`](loop-migracion-corte.md) paso 3).

### Fase D — Cortes (solo después de A–C)

Orden típico por dependencia: maestros/permisos → config/habilitación →
generación → operación → ciclo de vida / side-effects → UAT datos reales.

El orden sale de las capas **cerradas**. Un corte cuyo lugar en la secuencia depende de una
capa en `muestra` se numera, pero no se cita como «ordenado por dependencia».

### Fase E — Veredicto

Viable / viable con prerrequisitos / no — con primer CU, bloqueantes y diferidos
explícitos.

---

## Checklist (copiar al inicio del análisis)

- [ ] Fase A: pipeline config → operación (no solo punta visible); A2b padre de menú + gate de contexto
- [ ] Fase B: pantallas + beans + SP + tablas `ts`
- [ ] Fase C: maestros/seguridad con “¿bloquea si falta?”
- [ ] `## Profundidad de análisis` en el README: seis capas, ninguna en silencio
- [ ] Contraste con demo de negocio si hubo; si no, constancia
- [ ] Gap vs stack nuevo (Api / Web / Identity)
- [ ] Fase D: cortes por dependencia
- [ ] Fase E: veredicto + riesgo seed eterno / doble modelo
- [ ] Entregable bajo `docs/sdd/relevamiento-<modulo>/`

Omitir A1–A5 sin N/A → análisis **incompleto**.

---

## Relación con implementación

```text
Capa 3 (este proceso)  →  Capa 4 (proceso-sdd-paridad-completa)  →  código
   pipeline + maestros         spec / plan / verify
```

No se abre `spec.md` sin Fases A–C (salvo parcial fechado aceptado por producto).

---

## Aplicación

| Módulo | Acción |
|--------|--------|
| Anunciador / Tótem | **Ejemplo dorado:** [`relevamiento-node-anunciador/`](../relevamiento/relevamiento-node-anunciador/) |
| Turnos / agenda | **Hecho T0:** [`relevamiento-turnos/`](../relevamiento/relevamiento-turnos/) — no abrir spec del módulo entero |
| Nutrición | **Hecho T0:** [`relevamiento-nutricion/`](../relevamiento/relevamiento-nutricion/) — no abrir spec del módulo entero |
| Administración General / maestros | **Hecho T0:** [`relevamiento-maestros/`](../relevamiento/relevamiento-maestros/) — no abrir spec del tile entero |
| Mapa mental HIS (menú/home) | [`relevamiento-his-orientacion/`](../relevamiento/relevamiento-his-orientacion/) — no sustituye A–C por módulo |
| Módulo nuevo | Este proceso → veredicto → backlog |

---

## Anti-patrones

| Evitar | En su lugar |
|--------|-------------|
| Empezar por la pantalla del día a día | Empezar por quién configura y qué maestros faltan |
| “Con seed alcanza” | Seed ≠ paridad; diferir ABM/permisos con slug |
| “El package es el módulo” | Package = evidencia; unidad = capacidad/CU |
| Una tabla de firmas “de muestra” sin decir que es muestra | Declararla en `## Profundidad de análisis`; el silencio se lee como cerrado |
| “El stream ya está analizado” porque hay A–C | Vale la capa, no la carpeta: `cerrado` obliga, `muestra` no |
| Inventar `*_agi` / UUID de maestros | `ts.*` ([regla DDL](regla-ddl-postgres-migrado.md)) |
| Declarar viable sin Fase C | Condicionar a maestros / Identity |

---

## Mantenimiento

Dueño: quien abre el relevamiento. Al cerrar un CU, actualizar `matriz.md`.
Cambios de proceso: primero [`gobierno-migracion.md`](gobierno-migracion.md).
