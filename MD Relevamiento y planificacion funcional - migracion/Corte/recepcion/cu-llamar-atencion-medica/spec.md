---
title: Spec — CU Llamar atención médica (escritor clínico)
version: 0.2.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# Spec — Llamar atención médica → anunciador

## Problema

El stack nuevo alimenta la TV desde **recepción/AGI** (Cola A). En legacy, el volumen
clínico sale de **lista espera atención médica** (y clones) vía
`insertAnunciarPacienteConCola|ConAtencion` → `p_insert_llamado_anunciador` con
`tipo_atencion='ATENCION_MEDICA'`, `id_paciente`, `id_servicio`,
`id_cola_espera_serv_amb`.

Sin este writer, HOSPITAL_2 sigue obligatorio para el flujo ambulatorio principal
aunque el display ya esté en Quarkus/Angular.

## Inventario de capacidades (acto → side-effect)

| Capacidad | Evidencia legacy | ¿Escritura BD? | ¿Realtime? | Después |
|-----------|------------------|---------------|------------|---------|
| Llamar cola personal | `listaEsperaAtencionMedica.xhtml` → `actionBtnLlamarPacienteColaEsperaPersonal` → `insertAnunciarPacienteConCola` → `f_set_paciente_anun_cola` → `p_insert_llamado_anunciador` | INSERT `ts.llamado_anunciador` | WS push | Ciclo S→N (display) |
| Llamar cola servicio | mismo bean `…ColaEsperaServicio` | INSERT | WS | idem |
| Llamar en atención | `…EnAtencion` → `insertAnunciarPacienteConAtencion` → `f_set_paciente_anunciador_ate` | INSERT (+ UPDATE ambiente `atencion_amb`) | WS | idem |
| Gate anunciador disponible | `f_ambiente_tiene_anunciador` / `anunciador_ambiente_amb` | READ | — | Oculta/deshabilita Llamar |
| Re-call cabecera | `cabeceraAmbulatoria` → `bbAmbulatoria.llamarPaciente` | INSERT (re-call DELETE+INSERT) | WS | Diferido hijo |
| Quitar al cancelar | `cancelarAtencion` → `f_quitar_llamado_anunciador` | DELETE por paciente+servicio | WS | Fase 4 / hijo |
| Popup auto-atender al llamar | `generaAtencionAutLlamar` | atención | — | Diferido hijo |

Paths canónicos HOSPITAL_2:

- UI: `WebRoot/pages/ambulatoria/ambulatoria/listaEsperaAtencionMedica.xhtml`
- Bean: `beans/ambulatoria/ambulatoria/BBListaEsperaAtencionMedica.java`
- Delegator: `delegators/Anunciadores.java`
- Packages: `ATENCION.f_set_paciente_anun_cola`, `ANUNCIADORES.p_insert_llamado_anunciador`

## Schema destino

Canónico `ts.*` ([`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md)).

Columnas clave del INSERT clínico (vs Cola A):

| Columna | Cola A (ya migrado) | Este CU |
|---------|---------------------|---------|
| `tipo_atencion` | `'CONSULTA'` (adapter actual) | **`ATENCION_MEDICA`** |
| `id_cola_espera_recep` | set | null |
| `id_cola_espera_serv_amb` | null | **set** (path cola) |
| `id_paciente` / `id_servicio` | null en muchos inserts Cola A | **set** (desde cola/atención) |
| `leyenda_*` | box recepción | ambiente / paciente persona |

Puerto actual: `AnunciadorWritePort.insertLlamado(…, idColaEsperaRecep, idPuestoRecepcion)` —
**hay que extender** (o overload) para path clínico; `quitarPorPacienteServicio` ya existe.

## Clarify — **FIRMADO** 2026-09-16 (filas 1–4; pedido «Implementalo» + E3)

Defaults 5–7 se mantienen. Writer E1–E2 = [`cola-b-llamar/`](../cola-b-llamar/).
UI E3 = `/ambulatoria/espera-atencion` (opción A).

| # | Pregunta | Respuesta propuesta | Evidencia |
|---|----------|---------------------|-----------|
| 1 | ¿Primer vertical? | **Lista espera — cola personal** (`insertAnunciarPacienteConCola`). Cabecera / en-atención / servicio = hijos. | Lista es el acto primario de sala de espera |
| 2 | ¿Origen de datos? | Fila `ts.cola_espera_serv_amb` + ambiente (centro/sector/ambiente) del profesional | `f_set_paciente_anun_cola` |
| 3 | ¿Cómo se elige anunciador? | Resolver via `ts.anunciador_ambiente_amb` del ambiente (paridad gate). Si varios → push a todos (como `p_insert`). Seed demo OK solo si el ambiente de prueba está vinculado. | `p_insert_llamado_anunciador` |
| 4 | ¿UI destino v1? | Ruta clínica mínima paridad lista (p.ej. `/ambulatoria/espera-atencion`) con **Llamar** por fila. **No** colgar Llamar en `/recepcion/espera-amb` (Cola B) sin Clarify aparte. | Anti-inventar Cola B |
| 5 | ¿Quitar en este gate? | Smoke puede ejercer `quitarPorPacienteServicio`. Enganche UI cancelar/atención = **diferido** (`ciclo-vida` Fase 4 / hijo). | Sin botón “quitar” en lista legacy |
| 6 | ¿Auto-atender / popup al llamar? | **Fuera** de este gate (slug hijo). | Side-effect opcional legacy |
| 7 | ¿Throttle 5s? | **No inventar** en path clínico: `f_set_paciente_anun_cola` no throttlea (a diferencia de recepción). | Package ATENCION leído 2026-08-27 |

**Firma producto (2026-09-16):** filas **1–4** firmadas. Filas **5–7** defaults técnicos vigentes.

### Detalle Clarify (para firma)

#### C1 — Primer vertical (lista cola personal)

En legacy la **misma pantalla** tiene tres menús Llamar:

| Acto UI | Bean | Package path | ¿En este gate? |
|---------|------|--------------|----------------|
| Cola **personal** | `actionBtnLlamarPacienteColaEsperaPersonal` | `insertAnunciarPacienteConCola` → `f_set_paciente_anun_cola` | **Sí (v1)** |
| Cola **servicio** | `…ColaEsperaServicio` | mismo insert; puede pedir elegir ambiente personal vs servicio | Diferido |
| **En atención** | `…EnAtencion` | `insertAnunciarPacienteConAtencion` → `f_set_paciente_anunciador_ate` | Diferido |
| Cabecera ambulatoria | `bbAmbulatoria.llamarPaciente` | solo path ConAtención (re-call) | Diferido |

**Por qué personal primero:** es el happy path de “paciente en mi cola → anunciar a mi box”, un ambiente, sin popup dual.  
**Alternativa rechazada por defecto:** empezar por cabecera (solo re-call; no valida el flujo de sala de espera).

#### C2 — Origen de datos

`f_set_paciente_anun_cola(id_cola, centro, sector, ambiente)`:

1. Lee `id_paciente`, `id_centro_ate`, `id_servicio` de `ts.cola_espera_serv_amb`.
2. Llama `p_insert_llamado_anunciador(paciente, servicio, id_cola_espera_serv_amb, centro, sector, ambiente)`.

El Api **no** puede limitarse a “leyenda + anunciadorId” como Cola A: debe persistir FKs clínicas o el `quitar` por paciente+servicio no tiene paridad.

**Input mínimo del POST v1:** `idColaEsperaServAmb` + `idCentroAte` + `idSectorAmb` + `idAmbienteAmb` (o resolución de ambiente desde sesión/puesto, si se acuerda en E2).

#### C3 — Elección de anunciador (TV)

`p_insert_llamado_anunciador` **no** recibe `id_anunciador`. Busca todos los de:

`ts.anunciador_ambiente_amb` WHERE centro + sector + ambiente.

Si hay 0 → error de negocio / Llamar oculto (`f_ambiente_tiene_anunciador` / flags `anunciadorDisponible` en el bean).  
Si hay N → INSERT + push a **cada** anunciador.

**Implicancia DEV:** el seed de prueba debe vincular el ambiente usado al anunciador demo; hardcodear solo `ANU-DEMO` **rompe** paridad cuando un ambiente apunta a otra TV.

#### C4 — UI destino (crítica)

| Opción | Pros | Contras |
|--------|------|---------|
| **A — Ruta clínica nueva** `/ambulatoria/espera-atencion` | Paridad con `listaEsperaAtencionMedica.xhtml`; mapa claro | Más UI que un botón suelto |
| **B — Botón en** `/recepcion/espera-amb` | Reusa lista Cola B ya migrada | **Inventa** Llamar en pantalla de recepción; Cola B M5 aún Clarify; roles distintos |
| **C — Solo API + smoke** | Rápido para probar puerto | No cierra paridad de acto de usuario |

**Default: A.** B queda bloqueada hasta Clarify explícito en `paridad-recepcion-cola`. C solo como spike técnico, no gate.

#### C5 — Quitar

Legacy **no** tiene botón “Quitar llamado” en la lista. El DELETE ocurre al **cancelar atención** / volver a cola (`f_quitar_llamado_anunciador`).

- Este gate: IT/smoke llaman el puerto `quitarPorPacienteServicio` ya existente.
- Enganche UI cancelar = **diferido** (Fase 4 ciclo-vida), no WAIVE.

#### C6 — Auto-atender al llamar

Tras anunciar, legacy hace:

```text
if (!generaAtencionAutLlamar) → atenderPaciente();
```

Es side-effect de **generar atención**, no del anunciador. Meterlo en v1 mezcla dos CUs.  
**Diferir** con slug hijo; documentado para no quedar en silencio.

#### C7 — Throttle 5s

Cola A / `f_llamar_paciente_recepcion` sí tiene throttle ~5s.  
`f_set_paciente_anun_cola` **solo** SELECT cola + `p_insert` — **sin** throttle.

Default: **no inventar** throttle en el path clínico. Re-call del mismo ticket ya lo maneja `p_insert` (DELETE previo + INSERT, `ctd_llamados`).

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Clarify firmado (tabla arriba) |
| RF-2 | Extender write-port: INSERT con `tipo_atencion=ATENCION_MEDICA`, `id_paciente`, `id_servicio`, `id_cola_espera_serv_amb` + re-call DELETE + WS |
| RF-3 | `POST` clínico (cola serv amb + contexto ambiente) → paridad `f_set_paciente_anun_cola` mínima |
| RF-4 | Gate: sin vínculo ambiente↔anunciador → no publicar (error/omitir Llamar) |
| RF-5 | UI mínima lista + Llamar (ruta clínica, no Cola B recepción) |
| RF-6 | IT + smoke: llamar → TV `llamar=true` → consume `false` → quitar API |

## No objetivos

- Portar toda `BBListaEsperaAtencionMedica` (filtros, generación atención, permisos finos)
- GYE / consultorio / oftalmo / lab (otros slugs)
- ABM anunciador
- Sustituir path Cola A (`p_insert_llamado_anunc_recep`)

## Criterios de aceptación

| CA | Verificación |
|----|--------------|
| CA-1 | Clarify firmado |
| CA-2 | Fila en `ts.llamado_anunciador` con `tipo_atencion=ATENCION_MEDICA` y FKs paciente/servicio/cola serv |
| CA-3 | Display/WS refleja llamado sin HOSPITAL_2 |
| CA-4 | IT Api + smoke documentado PASS |
| CA-5 | Capacidades diferidas con slug (no silencio) |

## Relación paridad

Diferido desde circuito recepción **con slug propio** — no WAIVE.
Ver [`regla-waiver-paridad-legacy.md`](../../../canon/regla-waiver-paridad-legacy.md).
