---
title: Flujo migrado — consulta agendas + imprimir (Playwright)
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-09-04
---

# Flujo migrado — Hospital-Web (Playwright)

**Spec:** `Hospital-Web/e2e/grilla-turnos-consulta.spec.ts`  
**Ejecución:** `npm run e2e` (puerto `4310`, mocks HTTP — no requiere Api/Reports).

## Pasos de usuario (paridad legacy)

| # | Acción | Selector / evidencia |
|---|--------|----------------------|
| 1 | Abrir pantalla | `/configuracion/grilla-turnos-consulta` · `grilla-turnos-consulta-page` |
| 2 | Modo **Servicio** (default) | radio servicio |
| 3 | Elegir centro | `grilla-turnos-consulta-centro` |
| 4 | Elegir servicio | `grilla-turnos-consulta-servicio` |
| 5 | Año + **Consultar** | `#gtc-anio` · `grilla-turnos-consulta-submit` |
| 6 | Click celda mes **S** | `grilla-turnos-consulta-mes-s` |
| 7 | Panel sur: calendario | `grilla-turnos-consulta-calendario` |
| 8 | Click día con turnos | `grilla-turnos-consulta-dia` (ej. día 14) |
| 9 | Tabla turnos cargada | `grilla-turnos-consulta-turnos` |
| 10 | **Imprimir** habilitado | `grilla-turnos-consulta-imprimir` |
| 11 | Descarga PDF | `consulta-agendas-{fecha}.pdf` |

## API mockeada en e2e

| Endpoint | Fixture |
|----------|---------|
| `GET .../buscadores/centros` | HOSPITAL-DEMO `1001` |
| `GET .../buscadores/servicios` | CLINICA MEDICA `10` |
| `GET .../horarios/serv/grupos` | Grupo demo `91101` |
| `GET .../grilla/consulta-agendas` | Matriz con septiembre `S` |
| `GET .../grilla/consulta-agendas/dias` | Septiembre 2026 (día 14 disponible) |
| `GET .../grilla/consulta-agendas/turnos` | 1 turno LIBRE 08:00 |
| `GET .../grilla/consulta-agendas/imprimir.pdf` | PDF stub |

Fixtures: `Hospital-Web/e2e/helpers/turnos-grilla-consulta-fixtures.ts`.

## Stack real (smoke integración)

Para PDF BIRT real: Api `:8081` + Reports `:8082` + PG piloto.  
El e2e **no** sustituye ese smoke; valida UI + contrato de descarga con mocks.
