---
title: Spec — T6.2 hijo · Excel cola Reasignación
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.turnos-agenda-cola-reasignar-export
---

# Spec — Exportar Excel cola Reasignación

Padre: [`turnos-agenda-cola-reasignar/`](../turnos-agenda-cola-reasignar/) T6.2 **gate-done**.  
Ruta: **`/turnos/agenda`** accordion Reasignación. **No** fork. **No** sidecar.  
Clarify **FIRME Camino 1** — 2026-09-17. D-TUR-69. Francisco: «ok siguiente trabajo seria el excel».

## Instalación de referencia

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | Call Center Demo |
| Ramas `esClienteX()` | ninguna en `actionBtnExportarExcel` / `XLSParser` |
| Decisión | sin ramas · resto `diferido(multi-instalacion)` |

## Problema

T6.2 dejó Exportar Excel **disabled**. El HIS arma `.xls` POI desde `listTurnos`, título `reasignacion_turnos`.

## Resultado

South **Exportar Excel** enabled (150px). GET `.xls` HSSF: título `Reasignación de Turnos`, header HIS (centro/servicio siempre, personal si hay, equipo `<TODOS>`, fechas `dd/MM/yy`, horas), **17 columnas** de `actionBtnExportarExcel`. Filas = JDBC `listarColaReasignar`. Lista vacía: HIS igual llama `parse` → xls con 0 filas (no silencio T5.5).

## Clarify — **FIRME Camino 1** (2026-09-17)

| # | Pregunta | Respuesta Camino 1 | Evidencia |
|---|---------|-------------------|-----------|
| 1 | ¿Pipeline? | T6.2 + print **gate-done**. `ExcelExportPort` ya T5.5-excel. | verify padre |
| 2 | ¿Misma ruta? | **Sí** accordion. **No** Reports. **No** fork. | xhtml L192–194 |
| 3 | ¿Happy path? | Consultar → Exportar Excel → `.xls` OLE HSSF = grilla + 17 cols. | BB L487–569 |
| 4 | ¿Ciclo? | Solo lectura. | — |
| 5 | ¿Errores? | Fechas/horas = Consultar cola (no DATE_9). Vacío = xls 0 filas (HIS no return). Call center T5. | BB no chequea empty |
| 6 | ¿Side-effects? | GET `/api/v1/turnos/agenda/cola-reasignar/exportar.xls`. Reusa JDBC cola. **No** Flyway. Jobs **N/A**. | `XLSParser` |
| 7 | ¿Gate UI? | **N/A** xhtml nuevo. Encender botón 150px. | `turnosAReasignar.xhtml` |
| 8 | ¿Playwright? | **e2e-migrado** stub `.xls`. G6 abrir archivo **sí**. | regla-playwright |
| 9 | ¿Fuera? | Sidecar Excel. SheetJS. Equipo usable. PDF. mail_persona valor. T6 padre. | abajo |

**Descartado:** emitter sidecar; SheetJS; 204 si vacío (eso es T5.5 Consulta, no esta pantalla).

## Inventario

| Capacidad | Legacy | Este corte | ¿Escritura? |
|-----------|--------|------------|-------------|
| Exportar Excel cola | `actionBtnExportarExcel` | **In scope** | No |
| 17 cols + header `<TODOS>` | `XLSParser.parse("reasignacion_turnos", …)` | **In scope** | No |
| Equipo usable | combo | **diferido** D-TUR-17 (header `<TODOS>`) | No |
| Correo con `mail_persona` | getter MailPaciente | columna **sí**, valor vacío | No |
| Jobs | — | **N/A** | — |

## Firmas PL/SQL

Ninguna nueva. Lista = JDBC T6.2 (equivalente `TURNOS.f_get_turnos_a_reasignar`, **no clonar**). Decisión: **N/A** este hijo.

## Acceso

Mismo menú `/turnos/agenda` que T6.2. Sin Raise en el acto Excel. Prueba negativa = padre T5.

## NFR

| Eje | Objetivo |
|-----|----------|
| Tiempo | p95 GET Excel ≤ Consultar + 2 s (G6) |
| Volumen | filas xls = filas grilla dump |
| Concurrencia | **N/A** — GET; HIS no `FOR UPDATE` |

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Botón south enabled 150px, copy Exportar Excel. |
| RF-2 | GET `.xls`; filename `reasignacion-turnos-YYYY-MM-DD.xls`. |
| RF-3 | Header: Centro/Servicio (label o `<TODOS>`), Personal si hay, Equipo `<TODOS>`, Fecha/Hora desde-hasta. |
| RF-4 | Columnas HIS: Centro Atención, Servicio, Fecha, Hora, Duración, Cod. Prest., Paciente, Tipo Documento, Número Documento, Correo, Profesional/Equipo, Prestación, Convenio, Plan Convenio, Estado, Observaciones Reasignar Turno, Nro. Teléfono. |
| RF-5 | Estado = `A REASIGNAR`. Correo vacío. |
| RF-6 | 0 filas: xls con cabecera, no toast. |
| RF-7 | e2e stub; G6 abrir archivo. |
| RF-8 | Cabecera de columnas: gris 25% + negrita (`XLSParser` `csTableHead`). Mismo renderer que Consulta Agenda. |
| NFR-1 | POI solo infrastructure. No motor de reportes en Api. |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-69 | Camino 1 Excel cola: POI HSSF; GET `cola-reasignar/exportar.xls`; reusa JDBC T6.2; header `<TODOS>`; no sidecar; vacío = xls 0 filas. |

## No objetivos

| Ítem | Destino |
|------|---------|
| PDF cola | print **gate-done** |
| Excel Consulta Agenda | T5.5-excel **gate-done** |
| Equipo usable | D-TUR-17 |
| Valor correo | como T5.5-excel (columna vacía) |
