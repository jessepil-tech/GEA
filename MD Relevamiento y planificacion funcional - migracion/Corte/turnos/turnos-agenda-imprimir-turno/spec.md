---
title: Spec — T5 hijo · PDF turno (Agenda Imprimir)
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-22
phase_id: sdd.hospital.turnos-agenda-imprimir-turno
---

# Spec — PDF turno desde Agenda

Padre: [`turnos-agenda-otorgar/`](../turnos-agenda-otorgar/) (T5 **gate-done**).  
Ruta: **`/turnos/agenda`** grilla del día. **No** fork. **No** re-portar `Turno.rptdesign`.  
Clarify **FIRME Camino 1** — 2026-09-22. D-TUR-77. Francisco: «ok cobrar esa deuda».

## Instalación de referencia

Canon: [`regla-instalacion-referencia.md`](../../../canon/regla-instalacion-referencia.md). Misma que T5.

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo (dump piloto, `cliente="TS"`) |
| Ramas `esClienteX()` | ninguna en `BBAgenda.actBtnImprimirTurno` |
| Decisión | camino genérico · resto `diferido(multi-instalacion)` |

## Problema

T5 dejó el menú engranaje de la fila **sin Imprimir**. En el HIS `agenda.xhtml` L290: `IMPRIMIR` → `BBAgenda.actBtnImprimirTurno` → sidecar `Turno.rptdesign` (`f_imprime_turno_pac` solo arma `montoCoseguro`). El diseño **ya** está en Hospital-Reports.

## Resultado (objetivo Camino 1)

Gear de una fila **con paciente y no `RESERVADO`** → Imprimir → PDF sidecar `Turno` con datos del turno (paciente/hora/prestación). Coseguro 0 hasta el hijo del SP. **No** es Consulta Agenda ni ticket de recepción.

## Clarify — **FIRME Camino 1** (2026-09-22)

Firma producto: Francisco — «ticket PDF (hijo de T5) en que pantalla falta ese pdf?» + «ok cobrar esa deuda». Camino 1 = cable T6.2-print: GET Api → sidecar; no re-portar diseño; no Flyway; no BODY TURNOS.

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline previo? | T5 **gate-done**. Reports `Turno` portado. | verify T5 · CHECKLIST Reports |
| 2 | ¿Misma ruta / diseño? | **Sí** `/turnos/agenda` gear. **No** re-migrar layout. **No** embeber BIRT. | `actBtnImprimirTurno` · example `Turno.json` |
| 3 | ¿Happy path? | Consultar grilla → gear → Imprimir (fila OTORGADO) → PDF. Disabled si no hay paciente o `RESERVADO`. | xhtml L290–291 |
| 4 | ¿Ciclo de vida? | **No** escribe `ts`. Lee `turno` + `persona.fecha_nacimiento` para edad. | GET |
| 5 | ¿Errores? | Sin fila → 404 HIS `TURNO_NO_ENCONTRADO`. Sin paciente / `RESERVADO` → validación (paridad disabled). JWT → 401. Sidecar caído + stub = **FAIL** G6. | BBAgenda |
| 6 | ¿Side-effects / DDL? | Flyway **n/a**. HIS llama `f_imprime` (tmp + `f_calc_valores_recep`) **solo** para coseguro. Camino 1 **no** lo porta (techo FACTURACION/RECEPCIONES). `montoCoseguro=0`. `urlLogo=SDLC_VM`. | example Reports |
| 7 | ¿Paridad UI / Gate? | **N/A** pantalla nueva. Añadir ítem Imprimir al menú gear (copy `msg.IMPRIMIR`). | Gate UI padre cerrado |
| 8 | ¿Viaje Playwright? | **e2e-migrado** stub blob CI. G6 visual **sí** (motor BIRT). | [`regla-playwright-migracion.md`](../../../canon/regla-playwright-migracion.md) |
| 9 | ¿Fuera? | `TurnoTicket`. Matricial. Ficha west. `f_imprime`. Pack logos. Reenviar mail. HOS-APP. Consulta Agenda PDF. | slugs abajo |

**Caminos descartados**

| Camino | Por qué no |
|--------|------------|
| Portar `f_imprime_turno_pac` | Escribe `tmp_recepcion_amb*`; llama FACTURACION + RECEPCIONES; excede techo |
| `TurnoTicket.rptdesign` | Solo si `AccionAnterior=Recepcion` (box); no es Agenda |
| Re-portar `.rptdesign` | Ya en Reports; DoR cable ≠ port |
| PDF = ConsultaAgenda | Otro reporte; otro botón |

## Inventario de capacidades

| Capacidad | Evidencia legacy | Este corte | ¿Escritura BD? |
|-----------|------------------|------------|----------------|
| Imprimir PDF de un turno (gear Agenda) | `BBAgenda.actBtnImprimirTurno` · `agenda.xhtml` | **In scope** | No |
| Diseño `Turno.rptdesign` | HIS ReportManager | **hecho** Reports | No |
| `f_imprime_turno_pac` (coseguro) | TURNOS BODY | **diferido** `turnos-agenda-imprimir-turno-cose` | — |
| Imprimir lista turnos del paciente (oeste) | `datosPacienteTemplate.xhtml` mismo actBtn | **diferido** `turnos-agenda-ficha-imprimir` (mismo GET) | No |
| `TurnoTicket` / matricial | box recepción | **N/A** este circuito | — |
| Reenviar mail | `actBtnReenviarMail` | **diferido** T7 hijo | — |
| Pack logos centro | `packLogos.getLogoImpresion` | **diferido** `turnos-agenda-imprimir-logo` | No |

## Jobs / integraciones

`./tools/indice-legacy.sh --jobs turnos` — este CU es botón de pantalla.

| Job / interfaz | Decisión |
|----------------|----------|
| `CheckHabTurnosJob` | **N/A** (T2; no es print) |
| MailTurnoJob / T7 | **N/A** (otro circuito) |
| Integraciones externas | **N/A** (sidecar interno Reports) |

## Firmas PL/SQL

| Firma | Decisión | Motivo |
|-------|----------|--------|
| `TURNOS.f_imprime_turno_pac` | **diferido** hijo | No entra al universo Camino 1; techo + escritura tmp |
| Datasets BIRT `Turno` (SQL `ts.turno` + `personas.f_get_persona_telefono`) | **hecho** Reports | No re-portar |

## Requisitos (Camino 1)

| Id | Requisito |
|----|-----------|
| RF-1 | GET `/api/v1/turnos/agenda/{idTurno}/imprimir.pdf` → `reportId=Turno`. |
| RF-2 | Params: `idTurno`, `idPaciente`, `edad` (`AgendaPrepEdadEngine`), `montoCoseguro=0`, `prestacionConvenida=false`, `usuario` login, `urlLogo=SDLC_VM`, `leyenda=""`. |
| RF-3 | 404 si no existe; validación si `idPaciente` null o `RESERVADO`. 401 sin JWT. |
| RF-4 | Web: ítem Imprimir en gear; disabled paridad HIS; blob `turno-{id}.pdf`. |
| RF-5 | G6: PDF con paciente/hora del OTORGADO (sidecar real). |
| NFR-1 | Tiempo: GET p95 observado en G6. Volumen: 1 turno (no vacío). Concurrencia: **N/A** (GET; HIS no `FOR UPDATE` en print). |
| NFR-2 | No re-portar layout. No BIRT en Api. |

## Acceso y auditoría

Mismo perfil de menú que T5 (`/turnos/agenda`). Sin rol funcional extra en `actBtnImprimirTurno`. Prueba negativa = padre T5 (no reabrir). No escribe tablas auditadas → auditoría **N/A**.

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-77 | Camino 1 hijo print Agenda: GET sidecar `Turno`; no BODY; no Flyway; coseguro 0; `urlLogo=SDLC_VM`; ficha west y `TurnoTicket` fuera; G6 visual = datos del turno. |

## No objetivos

| Ítem | Destino |
|------|---------|
| Coseguro vía `f_imprime_turno_pac` | `turnos-agenda-imprimir-turno-cose` |
| Imprimir ficha oeste | `turnos-agenda-ficha-imprimir` |
| `TurnoTicket` / matricial | recepción box |
| Pack logos | `turnos-agenda-imprimir-logo` |
| Reenviar mail | T7 hijo |
| PDF Consulta Agenda / cola | **gate-done** otros slugs |
| HOS-APP | **WAIVE** otra app |

## Evidencia (paths)

```
Hospital-Legacy/.../BBAgenda.java  actBtnImprimirTurno
Hospital-Legacy/.../agenda.xhtml  L290 IMPRIMIR
Hospital-Reports/src/main/resources/designs/Turno.rptdesign
Hospital-Reports/examples/api-requests/Turno.json
Hospital-Api/.../GetImprimirTurnoPdfQueryHandler.java
Hospital-Web/.../turnos-agenda-row-menu.component.ts  Imprimir
```
