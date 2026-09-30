---
title: Verify — CU clínico B.1 Llamar recepción
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-b1-llamar-recepcion
---

# Verify — CU-B.1 Llamar recepción

## verdict

**PASS** — IT `cuB1_*` + smoke host `smoke-cu-clinico-b1.sh` (2026-08-14).

Paridad mínima: `POST /api/v1/agi/recepciones/{id}/llamar` → INSERT `ts.llamado_anunciador`
(`llamar=S`) en anunciador demo; UI botón **Llamar** en `/agi/espera`.

## CA

| CA | Resultado |
|----|-----------|
| Tras llamar, Anunciador refleja paciente/lugar | pass (IT + smoke) |
| Paridad `actLlamparPac` (selección por id) | pass |
| Throttle ~5s (legacy) | pass (DomainException si spam) |
| 404 recepción inexistente | pass (IT) |
| UI Llamar | entregada |

## Cómo

```bash
cd Hospital-Api
mvn -pl presentation-api -am -Dtest=AgiRecepcionResourceIT#cuB1_llamar_publicaLlamadoEnAnunciador,AgiRecepcionResourceIT#cuB1_llamar_recepcionInexistente_returns404 -Dsurefire.failIfNoSpecifiedTests=false test
bash tools/smoke-cu-clinico-b1.sh
```

UI: **Espera AGI** → **Llamar** → display `/display/anunciadores/:id`.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Publicar llamado (`llamar=S`) + ver en TV | **done** | este verify — solo vía **Llamar**, no al confirmar recepción |
| Ciclo `LLAMAR` S→N / delete / caducidad | **done** (núcleo TV) | [`ciclo-vida-llamado-anunciador/`](../../anunciador/ciclo-vida-llamado-anunciador/) gate-done 2026-08-26; enganche anulación → Fase 4 |
