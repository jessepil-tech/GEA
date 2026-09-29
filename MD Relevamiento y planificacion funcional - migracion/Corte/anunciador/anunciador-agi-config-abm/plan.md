---
title: Plan — ABM Anunciador + Terminal AG
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.anunciador-agi-config-abm
---

# Plan — `anunciador-agi-config-abm`

## Enfoque

| Capa | Decisión |
|------|----------|
| DDL | **Ya existe** V27/V29/V31. No rediseñar. FKs diferidas: no levantar las que no pida el CU ([`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md)). |
| Core | Ports escritura `AnunciadorConfigPort` / `TerminalAgConfigPort` / `DiccionarioAnunciadorPort` (nombres a ajustar). No mezclar con `AnunciadorWritePort` de **llamados**. |
| Application | Commands create/update/link; queries list ya parciales |
| Infrastructure | JDBC `ts.*`; NextId canónico |
| Presentation | `/api/v1/anunciadores` ampliar POST/PUT (datos + flags config avanzada); `/api/v1/agi/terminales` escritura; diccionario write |
| Web | Satélite ANUNCIADOR: pantallas config (incl. flags de `configuracionAvanzada.xhtml`); lista actual deja de ser solo “abrir display” |

Orden: Clarify **FIRME** + G0 **done** → **C1 API done** 2026-09-08 → **C2 API done** 2026-09-08 → **C3 API done** 2026-09-08 → C4-1 UI anunciador **click-through PASS** 2026-09-12 → C4-2 terminal/diccionario **UI 2026-09-12** → smoke → verify.

## Cortes internos

| Corte | Exit |
|-------|------|
| C0 | Clarify FIRME + inventarios copy/validaciones/geometría/interacción — **done 2026-09-08** |
| C1 | API anunciador + flags + vínculo + assets write + IT — **done 2026-09-08** (`AnunciadorConfigResourceIT`) |
| C2 | API terminal + opciones + IT — **done 2026-09-08** (`TerminalAgConfigResourceIT`) |
| C3 | API diccionario write + IT — **done 2026-09-08** (`DiccionarioAnunciadorResourceIT`) |
| C4 | UI paritaria (anunciador, ambientes, terminal, opciones, diccionario) — **C4-1 PASS** 2026-09-12 · **C4-2 UI** 2026-09-12 (`/anunciadores/terminales`, `/anunciadores/diccionario`; diccionario CRUD click PASS; alta terminal bloqueada por maestros vacíos en PG local) |
| C5 | Smoke filas **nacidas del CU** + verify + matriz/backlog/P3 — **`diferido(anunciador-agi-config-abm-c5)`** 2026-09-16 (sin pedido; carril pasa a M1a) |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Scope serv/triage | Rechazar PR; slug `anunciador-config-avanzada` (flags de la fila = v1) |
| Pack logos | Hijo; no inventar tabla `public` |
| Ambientes vacíos | Bootstrap padres; no ABM ambiente aquí |
| Pisar Turnos | No tocar `ts.turno` / hab / grilla |
| Evidencia con seed | Smoke obliga id ≠ demo; verify FAIL si solo ANU-DEMO |
| `ts.anunciador` sin `activo` | G0: DELETE físico HIS; no inventar columna; FK → 409 |

## Dependencias

- Relevamiento A–C anunciador **hecho**
- GET anunciador / display / terminales **gate-done** (lectura)
- Identity JWT + `menuKey` ANUNCIADOR
- `ts.ambiente_amb` con al menos un ambiente (seed o bootstrap)

No depende de T4/T5.
