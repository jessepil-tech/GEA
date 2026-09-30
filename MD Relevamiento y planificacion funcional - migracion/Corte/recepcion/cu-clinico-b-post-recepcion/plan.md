---
title: Plan — CU clínico B
version: 1.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-b-post-recepcion
---

# Plan — CU-B

## Enfoque

| Capa | Decisión |
|------|----------|
| Lectura | Extender `AgiPort` con `listRecepciones(...)` |
| API | `GET /api/v1/agi/recepciones` (colección; no choca con POST) |
| UI | `/agi/espera` |
| Llamar (RF-4) | **Diferido** → [`../cu-clinico-b1-llamar-recepcion/`](../cu-clinico-b1-llamar-recepcion/) — **no WAIVE** ([regla](../../../canon/regla-waiver-paridad-legacy.md)) |

## Orden (ejecutado)

1. List API + IT  
2. UI lista  
3. Smoke  
4. RF-4 → slug B.1 (deuda de paridad)

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Confusión path POST vs GET recepciones | Misma colección REST; documentar OpenAPI |
| Confundir enlace Anunciadores con Llamar | Regla: enlace ≠ acto; B.1 obligatorio |
| Cola HOSPITAL_2 más rica que lista AGI | Documentar deuda en spec § paridad; no WAIVE silencioso |
