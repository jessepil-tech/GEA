# Cortes propuestos — Anunciador + Tótem AGI

`phase_id:` **`sdd.hospital.relevamiento-node-anunciador.cortes`**  
Fecha: **2026-08-26**  
Solo después de pipeline + maestros ([`pipeline.md`](pipeline.md), [`maestros.md`](maestros.md)).

Orden por **dependencia** (no por pantalla bonita).

| Orden | Corte | Qué incluye | Prerrequisito | Estado / slug |
|-------|-------|-------------|---------------|---------------|
| 0 | Auth Identity + API Key TV | Login ops, key sala | — | **Hecho** oleada A / piloto |
| 1 | DDL `ts` anunciador + AGI + cutover | V27–V33, retiro `public` | Regla DDL | **Hecho** |
| 2 | Operación TV + WS + N1–N3 | Display, ocupación, assets read, diccionario read | Seed | **Hecho** (Node apagable DEV) |
| 3 | Tótem G1 + ticket PDF | Terminal seed, recepción AGI, BIRT | Seed terminal | **Hecho** eng.; impresora **P2** |
| 4 | Llamar recepción/AGI → `ts.llamado_anunciador` | Write + WS | Cortes 1–3 | **Hecho** |
| 5 | Ciclo vida `LLAMAR` | Núcleo TV + puerto quitar | Corte 4 | **Hecho** [`ciclo-vida-llamado-anunciador/`](../../cortes/anunciador/ciclo-vida-llamado-anunciador/) gate-done |
| 5b | Writer clínico | Lista espera atención médica | 5 | **Hecho** [`cu-llamar-atencion-medica/`](../../cortes/recepcion/cu-llamar-atencion-medica/) + [`cola-b-llamar/`](../../cortes/recepcion/cola-b-llamar/) **gate-done** 2026-09-16 |
| 6 | Sync datos reales / Cola B menú | UAT Oracle→PG | — | **P1** cierre |
| 7 | **Config / ABM / permisos** | Alta anunciador (incl. flags config avanzada), vínculo ambiente, terminal+opciones, diccionario write; pack logos y serv/triage = hijos | Cortes 1–2 | **Active** [`anunciador-agi-config-abm/`](../../cortes/anunciador/anunciador-agi-config-abm/) (P3; fila 4 FIRME; pack logos y serv/triage = [`anunciador-config-avanzada/`](../../cortes/anunciador/anunciador-config-avanzada/)) |
| 8 | Gate perímetro cerrado | Verify anti-gap + firma producto | 5 + decisión explícita sobre 7 | [`cierre-paridad-agi-anunciador/`](../../cortes/anunciador/cierre-paridad-agi-anunciador/) P5 |

**Regla:** el corte 7 no puede “absorberse” en seed eterno. Si producto acepta
perímetro **solo operativo**, debe quedar **diferido(slug)** o **WAIVE** con
evidencia — nunca silencio.

## Primer CU si se retoma config

**`anunciador-agi-config-abm`** — SDD abierto 2026-09-07 ([`../anunciador-agi-config-abm/`](../../cortes/anunciador/anunciador-agi-config-abm/)). Clarify propuesto.

1. CRUD anunciador + vínculo ambiente (sobre `ts.*`)
2. CRUD terminal AG + opciones
3. Write diccionario (opcional mismo slice o hijo)
4. Pack logos (hijo o WAIVE si centro piloto no lo usa — con evidencia)
5. Alinear `menuKey` / permisos Identity con pantallas nuevas
