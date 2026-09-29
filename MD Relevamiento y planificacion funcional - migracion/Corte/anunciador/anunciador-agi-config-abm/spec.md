---
title: Spec — ABM Anunciador + Terminal AG
version: 0.3.0
status: active
owner: grupogea
last_updated: 2026-09-11
phase_id: sdd.hospital.anunciador-agi-config-abm
---

# Spec — ABM config satélite Anunciador / Tótem

Padre: [`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/) · corte **7** · P3  
[`cortes.md`](../../../relevamiento/relevamiento-node-anunciador/cortes.md) · [`maestros.md`](../../../relevamiento/relevamiento-node-anunciador/maestros.md) ·
[`inventario.md`](../../../relevamiento/relevamiento-node-anunciador/inventario.md).

Canon datos: [`criterio-avance-e2e-datos.md`](../../../canon/criterio-avance-e2e-datos.md) — evidencia = **INSERT/UPDATE
desde este CU** en `ts.*`, no seed ni dump Oracle.

## Problema

Sala y tótem **leen** `ts.anunciador` / `ts.terminal_ag` (GET + seed). No hay alta/edición
en destino. PostgreSQL es la SoT: sin este ABM no se puede **provisionar** una TV o un
terminal nuevo, y el perímetro no cierra (P5).

**Seed `ANU-DEMO` / `demo-api-key` ≠ paridad de configuración.**

## Resultado (propuesto)

1. **API JWT** CRUD sobre `ts.anunciador` (datos + **flags de `configuracionAvanzada.xhtml`**)
   + vínculo `ts.anunciador_ambiente_amb`; upload logo/fondo (contrato N2 ya existente en GET).
2. **API JWT** CRUD `ts.terminal_ag` + `ts.opcion_terminal_ag`.
3. **API JWT** CRUD `ts.diccionario_anunciador` (palabra / equivalente).
4. **UI** bajo satélite `menuKey: ANUNCIADOR` (no inventar tile HIS): lista/alta/edición
   paritaria a xhtml de `pages/configuracion/anunciador/*` y `terminalAutogestion/*`.
5. IT + smoke: **crear** anunciador + vínculo + terminal + opción + palabra diccionario
   en PG y que display/AGI **lean** esas filas (sin tocar el seed como evidencia).

## Clarify — **FIRME** (2026-09-08 · “avancemos P3”)

Producto pidió cerrar el satélite. Filas **1–10 FIRME**. G0 inventarios UI **hecho**. **C1 API done** 2026-09-08. **C2 API done** 2026-09-08 (`TerminalAgConfigResourceIT`). **C3 API done** 2026-09-08 (`DiccionarioAnunciadorResourceIT`). **C4-1 UI anunciador click-through PASS** 2026-09-12. **C4-2 UI terminal+diccionario 2026-09-12** (diccionario CRUD PASS; alta terminal pendiente de padres en PG). Template Angular C4 contra inventarios G0.

| # | Pregunta | Respuesta FIRME | Evidencia / nota |
|---|----------|-----------------|------------------|
| 1 | ¿Pipeline previo? | A–C **hecho** 2026-08-26. Operación A6–A8 gate-done. Este SDD es **A3–A5 escritura**. | `relevamiento-node-anunciador/` |
| 2 | ¿Superficies v1? | **Anunciador + vínculo ambientes + assets + terminal AG + opciones + diccionario write.** | cortes.md “primer CU” ítems 1–3 |
| 3 | ¿Pack logos? | **diferido(`anunciador-pack-logos`)** — tabla no en Flyway V27–V33; Node `packLogos`. | inventario “No migrado” |
| 4 | ¿Config avanzada / serv / triage anunciador? | Flags de `configuracionAvanzada.xhtml` **entran en v1** (misma fila `ts.anunciador`). Solo `anunciadorServ` / `anunciadorTriage` / `anunciadorEspServ` → **diferido(`anunciador-config-avanzada`)**. | FIRME 2026-09-08 (sesión previa) |
| 5 | ¿ABM `ambiente_amb` / sector? | **Fuera.** Vínculo a ambientes **existentes**. Alta de ambiente = maestro ambulatorio. Bootstrap padres OK. | `ts.ambiente_amb` V27 · G0 `BBAnunciadorAmbienteAmb` |
| 6 | ¿API Key TV por anunciador? | v1: misma key Identity de sala (`X-API-Key`). Alta de key por anunciador → **diferido** Identity. | display actual |
| 7 | ¿Quién muta? | JWT autenticado + `menuKey` ANUNCIADOR (claim menú). Permiso fino 1:1 URL legacy → **parcial** (igual A2). | pipeline A1–A2 |
| 8 | ¿Borrado? | **DELETE físico** como HIS: anunciador `actBtnEliminarAnunciador`; terminal `deleteTerminalAg`; opciones y diccionario DELETE; vínculo ambiente DELETE (no el ambiente). Checkbox `activo`/`activa` **no** sustituye Eliminar. `ts.anunciador` **sin** columna `activo` → **prohibido inventarla**. Si FK (llamados, etc.) impide → toast/409, no silenciar. | G0 `BBAnunciador` L113–117 · `inventario-validaciones.md` |
| 9 | ¿Paridad UI? | Inventarios G0 **done**: copy, validaciones, geometría, interacción. Template C4 contra esos archivos. | `regla-paridad-ui-legacy` |
| 10 | ¿Orientación menú? | Satélite **ANUNCIADOR** (catalog). HIS 10211/10212/10215 bajo Centro atención; **no** colgar bajo RECEPCIÓN ni inventar tile. Display sin menú. | `inventario-geometria.md` |

**Firma producto:** 2026-09-08 (avance P3). Objetivo: cerrar P3 v1 (provisionar sala+tótem en PG). Pack logos y serv/triage no bloquean. P5 **no** se declara cerrado mientras `anunciador-config-avanzada` esté abierto. T5.1 Turnos = **otro carril** (no bloquea ni se pisa).

### Detalle

#### C2 — Por qué terminal en el mismo slug

Sin terminal+opciones el tótem G1 no se puede **dar de alta** en destino. Es el mismo satélite (pipeline A3–A5), no Recepción.

#### C4 — Flags vs serv/triage (FIRME)

`configuracionAvanzada.xhtml` **no es otro módulo**: es otra hoja del mismo anunciador
(voz, multimedia, ocupación, tipo de llamado, ctd chars, minutos, logo/fondo). Esas
columnas **ya están** en `ts.anunciador` (V27). Diferir el xhtml entero violaba
[`regla-waiver-paridad-legacy.md`](../../../canon/regla-waiver-paridad-legacy.md) § disciplina de
diferidos (“hacer ahora el acto de *esta* pantalla”).

v1 **persiste y edita** esos flags en el ABM. Display hoy honra voz + ocupación;
reproducir `url_multimedia` en la TV es gap de display, no de este ABM.

`anunciadorServ.xhtml` / `anunciadorTriage.xhtml` / `anunciadorEspServ.xhtml` son **otras
tablas/vínculos** (servicios, triage). Pagaré:
[`anunciador-config-avanzada/`](../anunciador-config-avanzada/). Gatillo: primera sala
que filtre por servicio/triage, o al retocar esas hojas.

#### C5 — Ambientes

Ocupación N1 y el vínculo necesitan `ts.ambiente_amb`. Si DEV no tiene ambientes no-seed, bootstrap padres **#2** (no ABM de ambiente aquí).

#### C8 — DELETE físico; no inventar `activo` en `anunciador`

G0: HIS **sí** borra (`Anunciadores.deleteAnunciador`). Destino = la misma semántica. El checkbox `activo` de terminal/opción es flag de operación, no el botón Eliminar. FK que falle = error visible, no columna nueva.

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este slice |
|-----------|------------------|------------|
| Alta/edición anunciador | `BBDatosAnunciador` · `pages/configuracion/anunciador/` | **In scope** |
| Flags config avanzada (misma fila) | `configuracionAvanzada.xhtml` → cols V27 | **In scope** (FIRME 4) |
| Vínculo anunciador↔ambiente | `BBAnunciadorAmbienteAmb` | **In scope** |
| Upload logo / fondo | ABM datos BLOB (también en config avanzada) | **In scope** (write; GET N2 ya existe) |
| Diccionario TTS write | `BBDiccionarioAnunciador` | **In scope** |
| Terminal AG CRUD | `BBTerminalAutoGestion` | **In scope** |
| Opciones terminal | `BBOpcionTerminalAutogestion` | **In scope** |
| Pack logos | Node `packLogos` | **diferido(`anunciador-pack-logos`)** |
| Vínculo serv / triage / esp-serv | `anunciadorServ.xhtml`, `anunciadorTriage.xhtml`, `anunciadorEspServ.xhtml` | **diferido(`anunciador-config-avanzada`)** |
| ABM ambiente/sector | maestros amb | **Fuera** (padres) |
| Listar / display / WS / Llamar | ya migrado | **Fuera** (no reabrir) |
| Cola B menú / gate recepción | recepción | **Fuera** |
| Llamar atención médica | [`cu-llamar-atencion-medica/`](../../recepcion/cu-llamar-atencion-medica/) | **Fuera** |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Listar / obtener anunciador (GET ya hay; alinear DTO escritura) |
| RF-2 | Crear / actualizar anunciador (`ts.anunciador`, NextId), **incl. flags** de `configuracionAvanzada.xhtml` |
| RF-3 | PUT logo / img fondo |
| RF-4 | Vincular / desvincular ambientes (`ts.anunciador_ambiente_amb`) |
| RF-5 | CRUD terminal AG |
| RF-6 | CRUD opciones terminal (PK `id_terminal_ag, nro_opcion`) |
| RF-7 | CRUD diccionario (`palabra` PK) |
| RF-8 | UI autenticada, paridad chrome + geometría |
| NFR-1 | Solo `ts.*`; no `*_agi` / `public` |
| NFR-2 | Smoke de evidencia **sin** usar solo filas seed |

## Criterios de aceptación

1. Clarify 1–10 **FIRME** (2026-09-08). Inventarios G0 en verify.
2. IT: POST anunciador + vínculo + terminal + opción + diccionario → 2xx; GET los ve.
3. Smoke UI: alta y edición; toast success; error en pantalla (no `/500`).
4. Display abre el **id creado** (API Key existente) y no solo `ANU-DEMO`.
5. AGI lista terminales incluye el **terminal creado** (o documentar gap si G1 GET no alcanza).
6. Verify: ninguna capacidad de inventario en silencio.
7. Inventarios copy + validaciones en verify (gate UI).

## No objetivos (este slice)

- Reabrir ciclo `LLAMAR`, WS, ticket, Node.
- Impresora R3.1.
- Pack logos.
- Vínculo serv / triage / esp-serv (`anunciador-config-avanzada`).
- Reproducir video/multimedia en el display (gap TV, no ABM).
- Dual-write Oracle.
- Recepción gate / Cola B M5.
