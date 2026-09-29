---
title: Spec — Llamar fila Cola B
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cola-b-llamar
---

# Spec — Llamar Cola B (`serv_amb` → TV)

## Problema

`e2e-mostrador` deja el turno autorizado en `ts.cola_espera_serv_amb` (Cola B).
CU-B.1 (`POST …/recepciones/{id}/llamar`) solo existe sobre `ts.cola_espera_recep`
(Cola A): 404 si el id es `recep_amb`. El menú de `/recepcion/espera-amb` (paridad
`colaEspera.xhtml`) **no tiene Llamar**. El acto HIS que anuncia esa fila es
`actionBtnLlamarPacienteColaEsperaPersonal` en lista de espera atención médica.
Sin este corte no hay TV del mismo ticket conferido.

## Capacidades

| Capacidad | Evidencia legacy | ¿Escritura? | ¿Realtime? | Después |
|-----------|------------------|-------------|------------|---------|
| Llamar fila `cola_espera_serv_amb` (cola personal, primer vertical) | `listaEsperaAtencionMedica.xhtml` → `insertAnunciarPacienteConCola` → `f_set_paciente_anun_cola` → `p_insert_llamado_anunciador` | `ts.llamado_anunciador` (`id_cola_espera_serv_amb`, `tipo_atencion='ATENCION_MEDICA'`) | WS display | ciclo S→N ya cobrado |
| Gate anunciador del ambiente | `anunciador_ambiente_amb` / `f_ambiente_tiene_anunciador` | READ | — | oculta/error si 0 TV |
| Listar la fila a llamar | M4 `GET …/espera-amb` | no | no | ya cobrado |
| Menú M5 Cola B | `colaEspera.xhtml` menuitems | sí (otros CUs) | — | `diferido` (corte M5) |
| Llamar Cola A / throttle 5 s | `cabeceraRecepcion.xhtml` `actLlamar` | `cola_espera_recep` | WS | **N/A** (B.1 gate-done) |
| Llamar cola servicio / en atención / cabecera | mismo bean clínico | INSERT | WS | `diferido(cu-llamar-atencion-medica)` |
| Auto-atención de cola | `AtencionAutomaticaColaEsperaAmbJob` | atención | — | **N/A** este corte |
| Purga cola / apaga TV | jobs | sí (prod) | no | `diferido(relevamiento-procesos-programados)` |

## Clarify

1. **Pipeline** — M4 lista Cola B gate-done. Ciclo TV gate-done. Puerto
   `AnunciadorWritePort.insertLlamado` existe para Cola A (`tipo CONSULTA`); este
   corte lo **extiende** (cola serv_amb + `ATENCION_MEDICA`). No Flyway nuevo.
   Gate centro/box = `diferido(paridad-recepcion-gate)`.
2. **Happy path** — fila `cola_espera_serv_amb` vigente → Llamar →
   `llamado_anunciador` con FK de esa cola → display `ANU-DEMO` 1001 muestra
   PACIENTE/Demo. Contra `hospital_api`. Evidencia 2026-09-16: cola B **5009**,
   turno **5**, llamado **id=5** (5008/turno 4 del viaje previo se conserva).
3. **Ciclo de vida** — no borrar Cola B ni turnos 4–5 (evidencia
   `e2e-mostrador` TSK-f4). Quitar al cancelar = `diferido` (ciclo-vida Fase 4).
   No throttle 5 s (el path clínico no lo tiene; no inventar el de recepción).
4. **UI / disparador — FIRMADO B (2026-09-16).** HIS no llama desde el menú
   Cola B. Opciones:
   - **A:** botón Llamar por fila en `/recepcion/espera-amb` = rediseño de
     disparador. **No** se implementa.
   - **B (elegido):** sin UI aquí; UI = `/ambulatoria/espera-atencion` en
     `cu-llamar-atencion-medica`; este corte solo API
     `POST /api/v1/recepcion/espera-amb/{id}/llamar`. El e2e-mostrador consume el
     POST.
   - **C:** inventar Llamar en Cola A para el autorizado → **rechazado** (rompe
     `f_genera_recepcion`: autorizado → serv_amb, no recep).
5. **Errores / acceso** — menú origen HIS del acto = lista espera atención médica
   (perfil clínico). Con **B** el sujeto de la API es staff JWT (mismo que lista
   espera-amb). 401 sin Bearer cubierto. `f_set_paciente_anun_cola` **no** consulta
   `rol_funcional`. Destino: `@Authenticated` — JWT `user_role` llega al comando
   (IT 404, no 403). Paridad perfil→menú = `diferido(acceso)` hasta emisor
   `menu:KEY` ([`destino-seguridad-identity.md`](../../../planificacion/destino-seguridad-identity.md)
   paso 2). Mensaje de negocio si ambiente sin anunciador.
6. **Fuera** — M5 menú · Cola A B.1 · C5 ABM · T6 · GYE · `colaEsperaImagen` ·
   reportes de la cadena · `AtencionAutomaticaColaEsperaAmbJob` · clones cola
   servicio / en atención.
7. **Paridad UI / Gate arranque** — C4 = **B**: Gate UI **N/A** (API). Sin xhtml
   nuevo de módulo. Geometría de espera-amb ya cobrada en M4.
8. **Viaje Playwright** — **e2e-migrado**: `e2e-mostrador` live POST Llamar + TV.
   Mocks **no** cierran. Legacy HIS = N/A (sin fixture Oracle de este viaje).

**Firma producto:** Clarify **#4 = B** (2026-09-16). Golden master de
`f_set_paciente_anun_cola` **PASS** 2026-09-16 (3 errores capturados en Oracle 11.2
+ 3 mapeos sintéticos del BODY; CALL de la función prohibido porque INSERTA).
HTTP 404 `colaEsperaServAmb` sigue siendo la superficie REST (Clarify B); el texto
`ORA-20001` vive en el motor.

## Firmas PL/SQL

| Firma | Decisión |
|-------|----------|
| `ATENCION.f_set_paciente_anun_cola` | **portar en app** — GM PASS (`SetPacienteAnunColaEngine` + fixture `f_set_paciente_anun_cola.json`). No BODY en PG. Rareza: `an_id_centro_ate` no se usa; centro sale de la fila. Sin `FOR UPDATE`. |
| `ANUNCIADORES.p_insert_llamado_anunciador` | **ya parcial** en `JdbcAnunciadorWriteAdapter`; extender FK serv_amb + tipo. Cola A intacta. Resolución TV por `anunciadorId` (Clarify B), no por (sector, ambiente) HIS. |
| `RECEPCIONES.f_llamar_paciente_recepcion` | **N/A** este corte (Cola A / B.1). |

## Presupuesto no funcional (se mide al cerrar)

| Eje | Objetivo | Contra qué |
|-----|----------|------------|
| Tiempo | p95 del POST Llamar ≤ el de B.1 en la misma base | `llamado_anunciador` del día |
| Volumen | una cola de un centro (no traer histórico entero) | filas `cola_espera_serv_amb` del centro 1001 |
| Concurrencia | dos actores sobre el mismo `id_cola_espera_serv_amb` | el package **no** usa `FOR UPDATE`; riesgo de doble llamado — medirlo en TSK-c4-nfr, no inventar lock |

## RF de escritura

Llamar fila Cola B → estado visible en TV (ciclo S) → fin en display.
Ledger: `id` de `ts.llamado_anunciador` + query; `id_cola_espera_serv_amb` de
origen.

## Relevamiento

[`paridad-recepcion-cola/`](../paridad-recepcion-cola/) ·
[`cu-llamar-atencion-medica/`](../cu-llamar-atencion-medica/) ·
[`mapa-menu-hospital-web.md`](../../../relevamiento/mapa-menu-hospital-web.md) ·
[`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).
