---
title: Tasks — CU clínico B.1
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-b1-llamar-recepcion
---

# Tasks — CU-B.1

1. [x] **TSK-ops-1** Clarify: anunciador default seed ANU-DEMO (`hospital.agi.anunciador-default-id`); body opcional `anunciadorId`
2. [x] **TSK-app-1** Command `LlamarRecepcion` + `POST .../recepciones/{id}/llamar` + `AnunciadorWritePort` + V16
3. [x] **TSK-app-2** IT `cuB1_*` + smoke `smoke-cu-clinico-b1.sh`
4. [x] **TSK-app-3** UI botón Llamar en `/agi/espera`
5. [x] **TSK-ops-2** verify-report PASS
