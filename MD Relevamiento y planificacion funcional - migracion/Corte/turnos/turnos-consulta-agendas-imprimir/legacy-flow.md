---
title: Flujo legacy — consulta agendas generadas (Playwright discovery)
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-09-04
---

# Flujo legacy — discovery con Playwright

**Fuente xhtml:** `Hospital-Legacy/.../atencionTurno/consultaAgendasGeneradas.xhtml`  
**Bean:** `BBConsultaAgendasGeneradas` · imprimir → `actBtnImprimirTurnos` → `ConsultaAgendaGeneradas.rptdesign`  
**Menú:** `consulta_agendas_generadas` → `/pages/atencionTurno/consultaAgendasGeneradas`

## Cuándo usar

- Relevar comportamiento no documentado en xhtml/BB (ej. `estadoTurno` al imprimir).
- Capturar selectores JSF/PrimeFaces estables antes de escribir asserts de paridad.
- Generar evidencia (trace) para verify SDD.

## Spec opt-in

`Hospital-Web/e2e/legacy/grilla-turnos-consulta-legacy.discovery.spec.ts`

Variables de entorno (piloto GrupoGEA):

| Variable | Valor piloto |
|----------|----------------|
| `LEGACY_E2E_BASE_URL` | `http://10.0.0.28:29300/HOSPITAL` |
| `LEGACY_E2E_USER` | `origin` |
| `LEGACY_E2E_PASSWORD` | (no versionar — vault local) |
| `LEGACY_E2E_CENTRO` | opcional; default `DEMO` → matchea `HOSPITAL-DEMO` |
| `LEGACY_E2E_SERVICIO` | opcional; default `CLINICA` → matchea `CLINICA MEDICA` |

```powershell
$env:LEGACY_E2E_BASE_URL="http://10.0.0.28:29300/HOSPITAL"
$env:LEGACY_E2E_USER="origin"
$env:LEGACY_E2E_PASSWORD="***"
# opcional:
# $env:LEGACY_E2E_CENTRO="HOSPITAL-DEMO"
# $env:LEGACY_E2E_SERVICIO="CLINICA MEDICA"
cd Hospital-Web
npm run e2e:legacy
```

**Nota:** el spec usa `playwright.legacy.config.ts` (no levanta Angular en `:4310`).  
Si la matriz no tiene celdas `S` para el centro/servicio elegido, el test **pasa** con anotación discovery (no falla CI); ajustar filtros o datos piloto legacy.

Sin variables → el spec se **omite** (CI no falla).

## Pasos legacy (referencia)

| # | Acción | Evidencia legacy |
|---|--------|------------------|
| 1 | Login portal | `login.faces` |
| 2 | Navegar consulta agendas | `consultaAgendasGeneradas.faces` |
| 3 | Radio **Servicio** / Profesional / Equipo | `tipoFiltro` |
| 4 | Centro + servicio (modo servicio) | `#centroAtencion` · `#servicio` |
| 5 | Grupo + año + **Consultar** | matriz grupo × mes S/N |
| 6 | Click mes **S** | calendario south + leyendas |
| 7 | Click día | `tablaTurnos` |
| 8 | **Imprimir** | `actBtnImprimirTurnos` → PDF BIRT |

## Entregables al cerrar discovery

1. Actualizar tabla de paridad en [`verify-report.md`](verify-report.md).
2. Ajustar fixtures e2e migrado si hay divergencia documentada (`diferido` / nota).
3. Adjuntar trace o capturas en carpeta del SDD si el equipo lo requiere.
