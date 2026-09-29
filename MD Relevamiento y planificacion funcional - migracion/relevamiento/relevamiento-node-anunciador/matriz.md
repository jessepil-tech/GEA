---
title: Matriz Node ANUNCIADOR → Hospital-Api / Identity
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.relevamiento-node-anunciador
---

# Matriz de migración

> **Avance 2026-08-26:** ciclo `LLAMAR` marcado **Migrado** (núcleo TV). Schema canónico
> `ts.llamado_anunciador` (piloto `llamado_paciente` retirado V33).
> **2026-08-27:** writer clínico P1 diferido → [`cu-llamar-atencion-medica/`](../../cortes/recepcion/cu-llamar-atencion-medica/).

Fuente Node: `Hospital-Legacy/ANUNCIADOR/anunciador`  
(`services/routers/**`, `services/socket.js`).

Eje config/ABM (no solo Node): [`pipeline.md`](pipeline.md) · [`maestros.md`](maestros.md) · [`inventario.md`](inventario.md).

Estados:

| Estado | Significado |
|--------|-------------|
| **Migrado** | Equivalente usable en Quarkus/Angular (contrato puede diferir) |
| **Parcial** | Cubierto a medias o con otro shape |
| **No migrado** | Sin endpoint/feature en destino |
| **Fuera de Api** | Va a Identity u otro producto |

---

## 1. Auth (`api_seguridad_nodejs`)

| Node | Destino | Estado | Notas |
|------|---------|--------|-------|
| `POST /autenticacion`, validate token, API Key propia | **Hospital-Identity** `:8080` — `login` / `refresh` / `me` + `X-API-Key` | **Migrado** (oleada A) | No vive en Hospital-Api |
| Adapter rutas viejas Node | Identity / BFF | **No migrado** | Diferido; ver `docs/contrato-api-identidad.md` oleada E |

---

## 2. HTTP — anunciador

| Node (`/api/…`) | Hospital-Api | Estado | Evidencia |
|-----------------|--------------|--------|-----------|
| `GET /anunciador/anunciador/` | `GET /api/v1/anunciadores` | **Migrado** | Seed `ANU-DEMO`; IT + smoke |
| `GET /anunciador/anunciador/:id` | `GET /api/v1/anunciadores/{id}` | **Migrado** | Flags ocupación / voz |
| `GET /anunciador/anunciadorDatosMinimos/` | — (usar listado) | **Parcial** | Summary liviano en `AnunciadorSummary` |
| `GET /anunciador/pacientesAnunciador/` (+ `/:id`) | `GET /api/v1/anunciadores/{id}/llamados` | **Migrado** | Bearer **o** `X-API-Key` (sala) |
| `GET /anunciador/anunciador/:id/logo/` | `GET /api/v1/anunciadores/{id}/logo` | **Migrado** | data-URI text/plain (N2) |
| `GET /anunciador/anunciador/:id/imgFondo/` | `GET /api/v1/anunciadores/{id}/img-fondo` | **Migrado** | data-URI text/plain (N2) |
| `GET /anunciador/ocupacionAmbientesActual/:idAnunciador` | `GET /api/v1/anunciadores/{id}/ocupacion-ambientes` | **Migrado** | N1 + WS |
| `GET /anunciador/diccionarioAnunciador/` (+ `/:palabra`) | `GET /api/v1/anunciadores/diccionario` | **Migrado** | N3 (lista completa) |
| `GET /configuracion/packLogos/:idPackLogos` | — | **No migrado** | Pack visual |
| `GET /util/fechaHora/` | — | **No migrado** | Display puede usar reloj del cliente |
| `GET /version` | `GET /api/v1/piloto/ping` | **Parcial** | Health/identidad de servicio, no semver Node |

### Escritura de llamados (no era un POST “público” del Node)

En legacy, el insert a `LLAMADO_ANUNCIADOR` lo dispara el ecosistema Java/Oracle
(recepción / packages). El Node **lee** y empuja por Socket.IO.

| Flujo destino | Endpoint / efecto | Estado |
|---------------|-------------------|--------|
| Llamar desde Cola A | `POST /api/v1/recepcion/colas/{id}/llamar` → `INSERT ts.llamado_anunciador` + WS | **Migrado** (paridad M2 + cutover) |
| Llamar AGI | Flujos `/api/v1/agi/…` → mismo write-port anunciador | **Migrado** (piloto G1) |
| Ciclo `LLAMAR` S→N / delete / caducidad | Consume-on-read + ventana + re-call + safety 1h + WS re-push + puerto `quitar*` | **Migrado** (núcleo TV) → [`ciclo-vida-llamado-anunciador/`](../../cortes/anunciador/ciclo-vida-llamado-anunciador/) gate-done 2026-08-26. Enganche anulación/atención = Fase 4 |
| Llamar atención médica (cola serv amb) | Writer clínico P1 → `tipo_atencion=ATENCION_MEDICA` + FKs paciente/servicio | **Migrado** → [`cu-llamar-atencion-medica/`](../../cortes/recepcion/cu-llamar-atencion-medica/) + [`cola-b-llamar/`](../../cortes/recepcion/cola-b-llamar/) **gate-done** 2026-09-16 |

---

## 3. Tiempo real (Socket.IO → WebSocket Quarkus)

| Node Socket.IO | Destino | Estado |
|----------------|---------|--------|
| Query `idAnunciador` + `tipoEscucha` | `WS /ws/anunciadores/{id}?apiKey=` | **Migrado** (auth distinta) |
| Evento `nuevos-llamados` | JSON `{ "event":"nuevos-llamados", "payload":{ anunciadorId, items } }` | **Migrado** — ver `anunciador-ws` |
| Evento `ocupacion-ambientes-cambio` | mismo nombre de evento en WS Quarkus | **Migrado** (N1) |
| Evento `actualizar-hora` | Reloj cliente | **WAIVE** W-NODE-1 |
| Protocolo Socket.IO engine | WebSocket nativo Quarkus | **A propósito distinto** (WAIVE en piloto) |

Flag: `features.enable-realtime` (`true` en `%dev` / `%test`).

---

## 4. Persistencia de llamados

| Concepto | Legacy | Destino | Estado |
|----------|--------|---------|--------|
| Tabla de display | Oracle `LLAMADO_ANUNCIADOR` | PG **`ts.llamado_anunciador`** | **Migrado** (cutover V27–V33) |
| Catálogo anunciador | Oracle / Node | PG `ts.anunciador` (+ ambientes) | **DDL migrado**; datos **seed**; ABM **diferido** → [`maestros.md`](maestros.md) |
| Puente piloto `llamado_paciente` / `public.*` | — | Retirado (V33) | **Hecho** — no reabrir |

Columnas usadas por display (DTO `LlamadoPaciente`):

| `ts.llamado_anunciador` | JSON / DTO |
|-------------------------|------------|
| `id_anunciador_paciente` (numeric → string) | `id` |
| `id_anunciador` | path / grupo WS |
| `leyenda_paciente` | `paciente` |
| `leyenda_lugar_atencion` | `lugarAtencion` |
| `llamar` (`S`/`N`) | `llamar` (boolean) — escritura S + ciclo S→N / quitar **migrados** (núcleo) |
| `fecha_hora_llamado` | filtro ventana |

DTO API display: `LlamadoPaciente(id, paciente, lugarAtencion, llamar)`.

Detalle config/ABM: [`inventario.md`](inventario.md) · [`pipeline.md`](pipeline.md).

---

## 5. Front

| Vue legacy | Hospital-Web | Estado |
|------------|--------------|--------|
| Sala TV (`AnunciadorSimple` / llamados) | `/display/anunciadores/{id}` | **Migrado** (API Key + poll/WS) |
| Listado anunciadores ops | `/anunciadores` | **Migrado** |
| Ocupación ambientes | Display compuesto si `mostrarProfOcupaAmb` | **Migrado** (N1) |
| Config logos / fondo | Logo + fondo data-URI en display | **Migrado** (N2) |
| Diccionario / TTS | SpeechSynthesis + `/diccionario` | **Migrado** (N3) |

---

## 6. En Hospital-Api pero **no** vienen del Node

Incluidos para no confundir el inventorio “Node → Api”:

| Área | Prefijo | Origen SDD |
|------|---------|------------|
| AGI recepción / ticket / espontánea | `/api/v1/agi/` | `piloto-agi-g1` / G1 |
| Cola recepción A | `/api/v1/recepcion/colas/` | `paridad-recepcion-cola` M1–M3 |
| Cola espera amb B | `/api/v1/recepcion/espera-amb` | M4 |
| Catálogo convenios | `/api/v1/catalogo/convenios` | catálogo |
| Files / audit / test | varios | starter |

---

## 7. Checklist rápido “¿apagamos Node?”

| Condición | ¿OK hoy? |
|-----------|----------|
| Sala muestra llamados con JWT/API Key Quarkus | **Sí** |
| Push inmediato al llamar | **Sí** (WS) |
| Auth sin `api_seguridad_nodejs` | **Sí** (Identity) |
| Ocupación ambientes en sala | **Sí** (N1) |
| TTS / diccionario | **Sí** (N3) |
| Logos/fondo desde Api | **Sí** (N2) |
| Config Web sin `API_ANUNCIADOR` | **Sí** (N4) |
| Smoke con Node down | **Sí** — `smoke-node-down-anunciador.sh` |
| Reportes SQL sobre `LLAMADO_ANUNCIADOR` | Usar `ts.llamado_anunciador` (canónico post-cutover) |

**Conclusión operativa (2026-08-26):** perímetro sala **puede apagar Node** en DEV.
Config/ABM **no** está en paridad — ver [`maestros.md`](maestros.md).
Programa apagar Node: [`apagar-anunciador-node/`](../../cortes/anunciador/apagar-anunciador-node/).
