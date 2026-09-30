---
title: Spec — Apagar ANUNCIADOR Node
description: Retiro de Express/Socket.IO/api_seguridad_nodejs; paridad en Quarkus/Angular.
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.apagar-anunciador-node
---

# Spec — Apagar Node

## Problema

El satélite Node (`ANUNCIADOR/anunciador` + `api_seguridad_nodejs` + Vue) sigue
siendo el runtime de sala en muchos sitios. El stack nuevo ya cubre **A1+A2
(llamados)**; sin un corte explícito, Node y Quarkus conviven y el Vue legacy
sigue atado a Socket.IO / rutas `/anunciador/*`.

## Resultado deseado

1. Ningún display/ops de anunciador en el perímetro migrado habla con Node.
2. Auth solo Identity; realtime solo WS Quarkus (o poll).
3. Runbook de apagado: stop proceso, DNS/config, checklist smoke.
4. Features Vue no portadas: **implementadas** o **diferidas con pérdida
   aceptada** documentada (no WAIVE silencioso — ver
   [`regla-waiver-paridad-legacy.md`](../../../canon/regla-waiver-paridad-legacy.md)).

## Evidencia Vue → Node (debe morir o reemplazarse)

| Uso | Archivo Vue | Endpoint / evento Node |
|-----|-------------|------------------------|
| Llamados push | `Llamados.vue` | Socket `nuevos-llamados` + GET pacientes |
| Ocupación | `OcupacionAmbientes.vue` | Socket `ocupacion-ambientes-cambio` + GET ocupacion |
| Reloj | `FechaHora.vue` | Socket `actualizar-hora` |
| Catálogo / assets | `AnunciadorService.js` | anunciador, logo, imgFondo, diccionario |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Display Angular de llamados operable **sin** Node (API Key + WS/poll) — ya |
| RF-2 | Ops listado anunciadores + detalle llamados en Web **sin** Node — ya |
| RF-3 | Si Clarify=sí ocupación: `GET` ocupación + evento WS (o poll) + UI Web |
| RF-4 | Logo/fondo: API o assets estáticos acordados; Vue no consulta Node |
| RF-5 | Diccionario: port o defer explícito (sin TTS hasta N3) |
| RF-6 | `api_seguridad_nodejs` fuera de config de despliegue del perímetro |
| RF-7 | Doc de corte + smoke post-apagado |

## WAIVE explícitos (permitidos)

| Id | Qué | Por qué |
|----|-----|---------|
| W-NODE-1 | Reloj vía Socket `actualizar-hora` | Reloj del cliente / NTP; no regla de negocio |
| W-NODE-2 | Protocolo Socket.IO byte-a-byte | Ya WAIVE en anunciador-ws; WS Quarkus canónico |
| W-NODE-3 | Mirror 1:1 `LLAMADO_ANUNCIADOR` | Fuera de apagado Node; ver relevamiento D-ANU-01 |

## No objetivos

- Apagar HOSPITAL_2 Java que aún escriba Oracle (otro corte).
- Migrar todo ANUNCIADOR Vue pantalla a pantalla si Angular ya cubre el CU.
- Implementar todos los skins/logos históricos en el primer corte.

## Criterios de aceptación (apagado)

| CA | Verificación |
|----|--------------|
| CA-1 | Smoke display + llamar recepción **con Node down** → OK |
| CA-2 | Ninguna URL de config Web apunta a host Node |
| CA-3 | N1–N3 según Clarify: done o defer firmado en verify-report |
| CA-4 | Runbook ejecutado en un entorno (dev/UAT) |

## Clarify — **CERRADO 2026-08-18**

N1 ocupación, N2 logos/fondo, N3 diccionario/TTS: **todos obligatorios**
(paridad funcional legacy; se migran todas las salas).  
Default de proyecto: ver `regla-waiver-paridad-legacy.md`.
