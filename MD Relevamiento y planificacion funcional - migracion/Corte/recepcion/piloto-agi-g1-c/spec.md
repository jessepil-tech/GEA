---
title: SDD — Piloto AGI G1-c credencial/token
description: Paso opcional versión credencial / token según flags de convenio (seed).
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.piloto-agi-g1-c
---

# Spec — Piloto AGI G1-c

Padre: [G1-b](../piloto-agi-g1-b/). Paridad con
`BBRecepcionarPaciente` (versión credencial → token → atención), **sin** lectora física.

## Resultado

1. Flags en `convenio_agi`: `req_version_credencial`, `req_token`.
2. `POST /recepciones` acepta `versionCredencial` / `token` opcionales; si el convenio
   los exige y faltan → **422**.
3. UI: paso intermedio tras elegir turno cuando aplica.
4. Seeds: DNI `30777888` (credencial) · `30666888` (token).

## CA

1. Convenio sin flags → confirmar sin campos extra (regresión G1-b).
2. Convenio `req_version_credencial` sin body → **400**; con valor → 201.
3. Convenio `req_token` análogo.
4. Smoke + IT PASS.
