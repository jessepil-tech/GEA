---
title: SDD — CU clínico B.1 · Llamar recepción (paridad)
description: Paridad HOSPITAL_2 btnLlamar / llamarPaciente → Anunciador.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-b1-llamar-recepcion
---

# Spec — CU-B.1 Llamar recepción

Padre: [`cu-clinico-b-post-recepcion/`](../cu-clinico-b-post-recepcion/) (lista espera **gate-done**).

## Por qué existe

Legacy **sí** tiene botón **Llamar** en el puesto de recepción
(`HOSPITAL_2/.../cabeceraRecepcion.xhtml` → `bbRecepcionColaEsperaRecep.actLlamar`
→ `Ambulatorio.llamarPaciente` / `llamarPacienteSeleccionado`). Eso publica el
paciente hacia el **Anunciador**.

No está en el tótem AGI (solo ticket). Por eso no entró en el gate de CU-B lista,
pero **no es WAIVE de paridad**: quedó como etapa posterior obligatoria — **este slice**.

## Resultado (entregado)

1. `POST /api/v1/agi/recepciones/{id}/llamar` (+ body opcional `{ "anunciadorId" }`).
2. Side-effect: INSERT `llamado_paciente` (`llamar=S`) + marca `recepcion_agi.llamado`.
3. Default anunciador: seed `ANU-DEMO` (`hospital.agi.anunciador-default-id`).
4. UI `/agi/espera`: botón **Llamar** por fila.
5. Throttle ~5s (paridad Oracle).
6. IT + smoke.

## Criterios de aceptación

1. Tras llamar, el Anunciador (API de llamados) refleja el paciente/lugar. ✅
2. Paridad mínima con selección por id (equiv. `actLlamparPac`). ✅
3. Sin esto, **no** declarar paridad de puesto recepción. ✅ (ahora sí para el acto Llamar)

## No objetivos

- TTS nativo en Hospital-Web (lo hace Anunciador)
- Triage / GYE llamar
- “Siguiente de cola” sin id (equiv. `actLlamar` puro) — opcional post-gate
- Cola rica (filtros/poll/últimos-10) — diferido post-B.1
- **Auto-anunciar al confirmar recepción / ticket** — legacy tampoco lo hace en AGI;
  el disparo es el botón Llamar del puesto (este CU)