---
title: Spec — T6.3 hijo · Excel Historial Turnos
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.turnos-agenda-historial-export.spec
---

# Spec — Excel Historial Turnos

## Semilla y universo

| Ítem | Valor |
|------|-------|
| Semilla | `historialTurno.xhtml` south `actBtnExportarExcel` · `BBHistorialTurno.actBtnExportarExcel` L339–362 |
| 1 hop | `XLSParser.parse` (HOSPITAL_2) · `TableClass(label, attr, true)` en Fecha / Fecha Modificacion |
| Fuera | Info popup · lista JSON · T5.5 Excel · T6.2 Excel · D-TUR-17 · T6 padre |
| Techo | 0 xhtml nuevo · 1 método bean · 0 firmas PL/SQL · 0 rptdesign |

Jobs: **N/A** (0 jobs hist; padre T6.3).

## Clarify — Camino 1 FIRME (D-TUR-73)

Francisco: «si habramoslo ahora». Camino 1: re-GET JDBC mismos filtros que Consultar; `ExcelExportPort.renderXls`; lista vacía = `.xls` 0 filas (no 204).

HIS: `XLSParser.parse("Historial Turnos", null, null, null, tableClass, listTurnos)` — **header/footer/widths null**. No copiar cabeceras `<TODOS>` de cola.

Filtros: `validateHistTurno` (solo horas; **no** `WRONG_INTERVAL_DATE_3`). **No** `requireCallCenterGate`. Equipo ignorado D-TUR-17.

Ruta: `GET /api/v1/turnos/agenda/historial/exportar.xls`.  
Filename: `historial-turnos-YYYY-MM-DD.xls` (`fechaDesde`).

### 14 columnas (orden HIS)

| # | Label HIS | Atributo | Formato celda |
|---|-----------|----------|---------------|
| 1 | Fecha | FechaHoraTurIni | `dd/MM/yyyy HH:mm` (`fechaHora=true` → `csRowDateHour`) |
| 2 | Duración | DuracionTurnoMtos | texto |
| 3 | Estado | EstadoTurnoLabel | texto (SUSPENDIDO→CANCELADO) |
| 4 | Paciente | Paciente | texto |
| 5 | Teléfono | NroTelPac | texto |
| 6 | Centro Atención | CentroAtencion | texto |
| 7 | Servicio | Servicio | texto |
| 8 | Profesional/Equipo | PersonalEquipo | texto |
| 9 | Cod Prestación | CodPrestacion | texto |
| 10 | Prestación | Prestacion | texto |
| 11 | Fecha Modificación | FechaModifica | `dd/MM/yyyy HH:mm` |
| 12 | Usuario Modifica | PersonalModifica | texto |
| 13 | Tipo Solicitud Turno | MedioSolTurno | texto |
| 14 | Tipo Cancelación Turno | MedioCancela | texto |

Sin columna Hora suelta. Título hoja: **Historial Turnos**.

## Acceso y trazabilidad

Mismos perfiles que T5 padre (menú Asignación de Turnos). Rol funcional: el SELECT de hist (sin extra). Actor sin rol: evidencia padre.  
Tablas auditadas: **no escribe**. `TBL_AUD_*` N/A.

## No funcionales

| Eje | Presupuesto | Cierre |
|-----|-------------|--------|
| Tiempo | p95 descarga = lista JSON mismo filtro | G6 |
| Volumen | dump COUNT hist=17; vacío + **diferido(perf-volumen)** | no PASS volumen |
| Concurrencia | GET sin `FOR UPDATE` | N/A |

## Instalación de referencia

Call Center Demo. `actBtnExportarExcel` sin `esClienteX()`. Ramas: N/A.

## Capacidades (universo)

| Capacidad | Decisión |
|-----------|----------|
| Excel 14 cols HSSF | **se porta** |
| Lista vacía → xls 0 filas | **se porta** |
| Cabecera filtros `<TODOS>` | **WAIVE** — HIS header=null |
| Equipo usable | **diferido(D-TUR-17)** |
| Lista / info | **fuera** padre T6.3 |
| Excel consulta / cola | **fuera** |
