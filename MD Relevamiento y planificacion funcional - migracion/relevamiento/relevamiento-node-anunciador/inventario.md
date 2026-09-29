# Inventario de capacidades — Anunciador + Tótem AGI

`phase_id:` **`sdd.hospital.relevamiento-node-anunciador.inventario`**  
Fecha: **2026-08-26**  
Complementa la matriz Node→Api ([`matriz.md`](matriz.md)) con el eje **config + AGI**.

---

## Configuración / ABM

| Capacidad | Evidencia legacy | ¿Escritura BD? | ¿Realtime? | Después qué | Estado |
|-----------|------------------|---------------|------------|-------------|--------|
| Alta/edición anunciador | `BBDatosAnunciador` · xhtml config | INSERT/UPDATE `anunciador` | No | Vínculo ambientes | **Diferido** `anunciador-agi-config-abm` |
| Vínculo anunciador↔ambiente | `BBAnunciadorAmbienteAmb` | INSERT/DELETE vínculo | No | Operación / ocupación | **Diferido** idem |
| Upload logo / fondo | ABM datos anunciador BLOB | UPDATE | No | Display N2 | **Diferido** idem |
| Diccionario TTS write | `BBDiccionarioAnunciador` | INSERT/UPDATE/DELETE | No | Pronunciación sala | **Diferido** idem |
| Pack logos | Node `packLogos` · `pack_logos` | READ (+ config centro) | No | Tótem visual | **No migrado** |
| Terminal AG CRUD | `BBTerminalAutoGestion` | INSERT/UPDATE | No | Opciones + runtime | **Diferido** idem |
| Opciones terminal | `BBOpcionTerminalAutogestion` | INSERT/UPDATE | No | Generación cola tótem | **Diferido** idem |
| Permisos menú config | `Seguridad.tienePermiso` | N/A | No | Abre ABM | **Parcial** Identity/Web |

## Operación (sala + tótem + recepción)

| Capacidad | Evidencia legacy | ¿Escritura BD? | ¿Realtime? | Después qué | Estado |
|-----------|------------------|---------------|------------|-------------|--------|
| Listar / ver anunciador | Node GET / Vue ops | No | No | Abrir display | **Migrado** |
| Display TV llamados | Vue sala / Node | No (lee) | Sí | Ciclo `LLAMAR` | **Migrado** |
| Ocupación ambientes | Node + Vue | No | Sí | — | **Migrado** (N1) |
| Logo / fondo read | Node | No | No | — | **Migrado** (N2) |
| Diccionario read | Node | No | No | TTS cliente | **Migrado** (N3) |
| WS nuevos-llamados | Socket.IO | No | Sí | — | **Migrado** |
| Tótem: identificar / turnos / recepción | AGI `BB*` | Sí (colas/recep) | No | Ticket / espera | **Migrado** (G1) |
| Ticket PDF | BIRT | No | No | Impresora | **Migrado** eng.; UAT impresora **P2** |
| Llamar → TV | Recepción / packages | INSERT `llamado_anunciador` | Sí | Caducidad / anular | **Migrado** write; ciclo **parcial** |
| Auth ops | `api_seguridad_nodejs` / JSF | — | No | — | **Migrado** → Identity |
| Auth TV | API Key Node | — | No | — | **Migrado** → Identity API Key |

## Ciclo de vida / excepciones

| Capacidad | Evidencia | Estado |
|-----------|-----------|--------|
| Consume-on-read / ventana / safety 1h | `ciclo-vida-llamado-anunciador` C1+C2.2 | **Parcial** done |
| DELETE / limpieza al anular | PL/SQL legacy | **Puerto done**; enganche CU **diferido** [`cu-llamar-atencion-medica/`](../../cortes/recepcion/cu-llamar-atencion-medica/) |
| Sync colas Oracle→PG | UAT | **Diferido** P1 |

Detalle HTTP Node↔Api: [`matriz.md`](matriz.md).
