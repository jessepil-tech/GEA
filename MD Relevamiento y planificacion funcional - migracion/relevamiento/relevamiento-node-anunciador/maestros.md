# Maestros y seguridad — Anunciador + Tótem AGI

`phase_id:` **`sdd.hospital.relevamiento-node-anunciador.maestros`**  
Fecha: **2026-08-26**  
Padre: [`README.md`](README.md)

Tabla anti-omisión (Fase C). **Seed ≠ paridad de configuración.**

---

## Inventario

| Maestro / permiso | ¿Bloquea operación si falta? | ¿Existe CU/SDD? | Estado migración |
|-------------------|------------------------------|-----------------|------------------|
| Usuario / JWT (ops) | Sí | [`identidad-oleada-a/`](../../cortes/plataforma/identidad-oleada-a/) | **Migrado** |
| API Key display TV | Sí (sala sin JWT) | Piloto anunciador / Identity | **Migrado** (demo key) |
| Permiso menú Config anunciador | Sí para ABM; no para TV si hay API Key + URL | Parcial Web `menuKey: ANUNCIADOR` | **Parcial** — sin paridad URL HOSPITAL_2 |
| Permiso menú Terminal AG | Sí para config tótem | Ninguno (ABM) | **Diferido** → [`anunciador-agi-config-abm`](../../cortes/anunciador/cierre-paridad-agi-anunciador/) (P3) |
| Claim menú `AGI` / `RECEPCION` | Sí si menú filtrado | Shell Web | **Parcial** / demo |
| `ts.anunciador` | Sí — sin fila no hay TV/llamar a ese id | RF-6 piloto diferido | **Seed**; ABM **diferido** (`anunciador-agi-config-abm`) |
| `ts.anunciador_ambiente_amb` | Sí — “activo” y alcance por ambientes | — | **Seed**; ABM **diferido** |
| `ts.ambiente_amb` / sector | Sí para vínculo y ocupación | Ocupación N1 | **Seed**; sync UAT **diferido** |
| `ts.diccionario_anunciador` | No duro (TTS degradado) | N3 read | **Read migrado**; write **diferido** |
| Logo / img fondo (`ts.anunciador`) | No (display vacío) | N2 read | **Read migrado**; upload **diferido** |
| `pack_logos` | Sí en tótem legacy (logos por centro) | Matriz Node: no migrado | **No migrado** (tabla no en Flyway V27–V33 Api) |
| `ts.terminal_ag` | Sí — tótem no arranca | G1 GET terminales | **DDL + seed/IT**; ABM **diferido** |
| `ts.opcion_terminal_ag` | Sí — opciones del tótem | G1 | **DDL + seed/IT**; ABM **diferido** |
| `ts.recepcion` / puestos / cola | Sí para cola A y llamar | Paridad cola M1–M4 | **Parcial** (ops sí; sync UAT P1) |
| `ts.llamado_anunciador` | N/A (resultado de operación) | Cutover + ciclo vida | **DDL migrado**; ciclo **gate-done** (núcleo TV) |

---

## Evidencia ABM legacy (escritura)

| Capacidad | Legacy | Destino hoy |
|-----------|--------|-------------|
| Alta/edición anunciador | `BBDatosAnunciador` / `Anunciadores.insert|update` · `pages/configuracion/anunciador/` | Solo **GET** `/api/v1/anunciadores` |
| Vínculo anunciador↔ambiente | `BBAnunciadorAmbienteAmb` | Lectura implícita; seed |
| Terminal AG CRUD | `BBTerminalAutoGestion` · `terminalAutogestion/` | **GET** `/api/v1/agi/terminales` |
| Opciones terminal | `BBOpcionTerminalAutogestion` | Usadas vía seed/IT en G1 |
| Diccionario CRUD | `BBDiccionarioAnunciador` | Solo **GET** `/diccionario` |
| Pack logos | Node `packLogos` + `ts.pack_logos` | **Ausente** en Api |

DDL destino: `Hospital-Api` Flyway `V27__ts_anunciador.sql`, `V31__ts_agi_maestros.sql`, seed `db/dev-seed/ts_anunciador_demo.sql`.

---

## Regla de cierre

No marcar perímetro AGI+Anunciador **cerrado** mientras esta tabla tenga filas
**diferidas** en silencio. Cada fila debe permanecer **done / diferido(slug) / WAIVE**.

Slug de deuda config/ABM: **`anunciador-agi-config-abm`** — SDD
[`anunciador-agi-config-abm/`](../../cortes/anunciador/anunciador-agi-config-abm/) (P3, Clarify propuesto 2026-09-07).
