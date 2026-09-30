---
title: Runbook — Stop ANUNCIADOR Node
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.apagar-anunciador-node
---

# Runbook — apagar Node (N5)

## Precondiciones

- [x] N1–N3 implementados (ocupación, logo/fondo, diccionario/TTS)
- [x] Hospital-Web sin `API_ANUNCIADOR` ([inventario](inventario-despliegues-node.md))
- [ ] Ops confirma centro/entorno a cortar (dev/UAT/prod por oleada)

## Pasos (dev local / UAT)

1. **Verificar stack nuevo up**
   ```bash
   curl -sS http://127.0.0.1:8080/q/health   # Identity
   curl -sS http://127.0.0.1:8081/q/health   # Hospital-Api
   ```
2. **Detener satélite Node** (si corre):
   - proceso Express `ANUNCIADOR/anunciador`
   - `api_seguridad_nodejs`
   - `anunciadorVue` (nginx/static + envsubst)
   - liberar puertos típicos `3000`, `4201`, `8088` (ajustar por sitio)
3. **Smoke Node-down**
   ```bash
   bash /Volumes/External/Development/osw/grupogea/Hospital-Api/tools/smoke-node-down-anunciador.sh
   ```
4. **UI sala** (opcional manual):  
   `Hospital-Web` → `/display/anunciadores/33333333-3333-3333-3333-333333333301`
5. **No reencender** Vue/Node en ese perímetro; DNS/proxy solo a Identity/Api/Web.

## Rollback

1. Volver a levantar Express + Vue **solo** en centros no migrados.
2. No mezclar `hospitalApiUrl` Quarkus con `VUE_APP_API_ANUNCIADOR` en el mismo display.

## Evidencia

Ver [verify-report.md](verify-report.md).
