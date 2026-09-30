---
title: Inventario despliegues Node ANUNCIADOR
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.apagar-anunciador-node
---

# Inventario — hosts/env que aún apuntan a Node (TSK-ops-2)

## Perímetro migrado (dev local 2026-08-18) — **sin Node**

| Componente | Config | Destino |
|------------|--------|---------|
| Hospital-Web | `public/appsettings.json` | `apiUrl` → Identity `:8080`; `hospitalApiUrl` → Api `:8081`; `hospitalDisplayApiKey` |
| Hospital-Api | Quarkus | PG + WS `/ws/anunciadores/{id}` |
| Hospital-Identity | Quarkus | Login / JWT |
| Smoke | `Hospital-Api/tools/smoke-node-down-anunciador.sh` | Assert Node ports down + display N1–N3 |

**No** hay `API_ANUNCIADOR` / `VUE_APP_API_ANUNCIADOR` en Hospital-Web.

## Legacy aún en el monorepo (no usar en perímetro migrado)

| Path | Refs |
|------|------|
| `ANUNCIADOR/anunciadorVue/.env` | `VUE_APP_API_ANUNCIADOR=http://192.168.40.136:8081/api` · `VUE_APP_API_ANUNCIADOR_SOCKET=…:8081` |
| `ANUNCIADOR/anunciadorVue/config/configuration.js` | placeholders `$VUE_APP_API_ANUNCIADOR*` |
| `ANUNCIADOR/anunciadorVue/entrypoint.sh` | `envsubst` de esas vars |
| `ANUNCIADOR/anunciador/config/config.js` | `API_WS` → WS-HOSPITAL legacy |
| `ANUNCIADOR/api_seguridad_nodejs/` | JWT propio (reemplazado por Identity) |

## URLs que **rompen** si Vue legacy queda encendido con Node down (TSK-ops-n0-2)

| URL / recurso Vue | Efecto con Node down |
|-------------------|----------------------|
| `GET {API_ANUNCIADOR}/anunciador/…` | Connection refused / timeout |
| Socket.IO `{API_ANUNCIADOR_SOCKET}` | No `nuevos-llamados` / ocupación |
| `GET …/logo/` · `…/imgFondo/` | Sin branding |
| `GET …/diccionarioAnunciador/` | Sin lexema TTS |
| `api_seguridad_nodejs` auth | Login sala Vue falla |

**Mitigación:** no desplegar `anunciadorVue` en centros migrados; usar Hospital-Web `/display/anunciadores/{id}`.

## Acción N4/N5

1. Despliegues → solo Identity + Api + Web (este inventario).
2. Centros aún en Vue+Node → cutover por centro (no N5 global hasta checklist).
3. Código `ANUNCIADOR/` se **archiva** (no borrar en este corte); ver [runbook-stop-node.md](runbook-stop-node.md).
