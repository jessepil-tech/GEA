---
title: Spec — Ciclo de vida llamado anunciador
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.ciclo-vida-llamado-anunciador
---

# Spec — Ciclo de vida llamado anunciador

## Problema

Hoy Hospital-Api solo **crea** filas `ts.llamado_anunciador` con `llamar='S'` y publica
WS **cuando el usuario pulsa Llamar** (Espera AGI / Cola recepción). No hay
transición a `'N'`, caducidad ni borrado automático de negocio completo → sin
ciclo, la TV acumularía rojos.

**No es un bug** que la confirmación de recepción no anuncie: ver
[`cu-clinico-b1-llamar-recepcion/`](../../recepcion/cu-clinico-b1-llamar-recepcion/) y
[`mapa-menu-hospital-web.md`](../../../relevamiento/mapa-menu-hospital-web.md).

## Resultado deseado

1. Reglas de transición **documentadas desde legacy** (evidencia SP/Java) — **Clarify firmado**.
2. Implementación en Api (+ push WS) alineada a esas reglas.
3. Display Angular refleja estados sin lógica de negocio inventada en el front.

## Evidencia legacy

| Fuente | Rol |
|--------|-----|
| `TS.RECEPCIONES.f_llamar_paciente_recepcion` | Throttle 5s + `p_insert_llamado_anunc_recep` |
| `TS.ANUNCIADORES.p_insert_llamado_*` | INSERT `llamar='S'`; re-llamado mismo paciente → DELETE previo |
| `TS.ANUNCIADORES.f_get_paciente_anunciar` | Read TV: ventana minutos; **S→N al consumir** el `S` más viejo |
| `TS.ANUNCIADORES.f_quitar_llamado_anunciador` / `pp_quitar_*` | DELETE por paciente/servicio o cola |
| `TS.TURNOS.f_migra_turno_vencido` | Safety: `S→N` si `fecha_hora_llamado < SYSDATE−1h` |
| `RECEPCIONES.f_anula_ord_serv_amb` / `ATENCION.f_cancela_cola_espera_hr` | DELETE por anulación/cancelación |
| `ImpBusAtencionMedica` | `quitarLlamadoAnunciador` al volver a cola / flujos atención |
| Vue `Llamados.vue` | Pinta `S` rojo / `N` normal; no define ciclo |

Dump canónico: `tools/relevamiento/out/plsql/package_body/ANUNCIADORES.sql`.

### Schema destino (`ts.llamado_anunciador`)

Canónico en PG migrado vía **V27** (ids NUMERIC serializados string en JSON); el table/mirror
piloto `llamado_paciente` ya no existe (**retirado en V33**). Columnas referenciadas por este
ciclo:

`id_anunciador_paciente` (numeric), `id_anunciador`, `leyenda_paciente`, `leyenda_lugar_atencion`,
`nro_orden`, `tipo_atencion`, `id_recepcion`, `id_puesto_recepcion`, `id_cola_espera_recep`,
`fecha_hora_llamado`, `llamar`, `ctd_llamados`, `fecha_last_update`, `actualizado_por`.

Schema canónico según [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

## Clarify — **FIRME** (2026-08-18)

| # | Pregunta | Respuesta |
|---|----------|-----------|
| 1 | ¿Al llamar al **siguiente**, el anterior pasa a `LLAMAR=N` o se borra? | **Ninguno en el INSERT del siguiente paciente distinto.** El `S→N` ocurre al **leer** la TV (`f_get_paciente_anunciar`): toma el `S` más antiguo de la ventana, lo devuelve como “a llamar” y en el mismo call hace `UPDATE llamar='N'`. Re-llamar el **mismo** paciente/cola: **DELETE** de su fila previa + nuevo `INSERT S`. |
| 2 | ¿Hay TTL / job que limpie llamados viejos? | **Sí, dos capas:** (a) ventana de display `ctd_minutos_muestra_pac` (default **5 min**) — fuera de ventana no se listan; (b) batch `f_migra_turno_vencido`: `S→N` si lleva **> 1 h** en `S`. No hay job que **DELETE** solo por edad. |
| 3 | ¿Anulación / fin de atención borra el llamado? | **Sí (DELETE)** en anula OS, cancela cola, `f_quitar_llamado_anunciador` (paciente+servicio) desde atención. Al generar atención desde cola a veces solo `UPDATE id_cola_espera_serv_amb=null` (conserva fila). |
| 4 | ¿El display muestra históricos `N` o solo activos + últimos K? | **Ambos** dentro de la ventana de minutos: todos los `N` recientes + el `S` pendiente (que pasa a `N` al consumirse). Orden: `LLAMAR DESC`, `fecha DESC`. Vue: paginador 7 filas; rojo solo si `LLAMAR='S'`. |

### Decisión de destino (paridad de comportamiento)

| Pieza | Enfoque en Hospital-Api / Web |
|-------|-------------------------------|
| **S→N** | Al servir lista para display (REST GET llamados y/o snapshot WS): consumir el `S` más viejo de la ventana → `UPDATE llamar='N'` + devolver esa fila aún como “activo” en esa respuesta **o** equivalente observable (rojo una vez + TTS). Push WS con lista actualizada. |
| **Ventana** | Filtrar por `creado_en` / equivalente usando minutos configurables del anunciador (default 5). |
| **Safety 1h** | Job/scheduler opcional en C2 que marque `S→N` si `> 1h` (paridad `f_migra_turno_vencido`). |
| **DELETE** | Puerto `quitar`/`delete` en disparadores de anulación/cancelar/quitar atención cuando existan en destino; re-llamar mismo ticket → delete previo + insert. |
| **Front** | Solo pinta por flag `llamar`; **sin** heurística de caducidad local. |

**No** implementar “solo el último en rojo” inventado en el front: el legacy lo hace vía **consumo en lectura** + ventana + deletes de negocio.

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Relevamiento firmado (tabla Clarify) — **done** |
| RF-2 | Transición `llamar S→N` al consumir en lectura display (+ delete re-call / anulación) vía puerto escritura |
| RF-3 | Tras cada transición, push WS `nuevos-llamados` (lista completa actual filtrada por ventana) |
| RF-4 | Display: rojo solo si `llamar===true`; sin heurística local de caducidad |
| RF-5 | IT + smoke: llamar → rojo → re-poll/WS consume S→N → aparece como N / sale de ventana |
| RF-6 | Link desde B.1 / M2 verify: deuda cerrada o WAIVER de **negocio** |

## No objetivos

- Mirror 1:1 schema `LLAMADO_ANUNCIADOR` (sigue W-NODE-3 / D-ANU-01) — **superseded:** la
  tabla ya vive en `ts.llamado_anunciador` (V27), no es un mirror diferido. Schema canónico
  según [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).
- TTS / ocupación / logos (ya N1–N3)
- Portar literal `tmp_paciente_anunciador` Oracle
- Cambiar Cola A/B salvo side-effect necesario al llamar / anular

## Criterios de aceptación

| CA | Verificación |
|----|--------------|
| CA-1 | Clarify firmado en este spec — **PASS** (2026-08-18) |
| CA-2 | Tras consumir display, no quedan `S` “fantasma” indefinidos dentro de la ventana |
| CA-3 | WS actualiza display sin refresh manual |
| CA-4 | IT Api verde; smoke documentado |

## Relación con regla de paridad

Diferido desde B.1/M2 **con slug propio** — no WAIVE. Ver
[`regla-waiver-paridad-legacy.md`](../../../canon/regla-waiver-paridad-legacy.md) y
[`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).
