---
title: Inventario validaciones — T5.1 ficha paciente
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-ficha-paciente.validaciones
---

# Inventario validaciones — T5.1 (G0)

Plantilla gate: [`regla-paridad-ui-legacy.md`](../../../canon/regla-paridad-ui-legacy.md) v1.12.

Fuente: `BBAgenda` · `BBBuscadorPaciente` · `BBBuscadorConvenio` · MessageBundle.

Web destino: extender `turnos-agenda-validation.ts` + toast.

**Herencia:** reglas consultar/reservar/otorgar/liberar en [`turnos-agenda-otorgar/inventario-validaciones.md`](../turnos-agenda-otorgar/inventario-validaciones.md) — siguen vigentes.

## Búsqueda paciente

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Sin criterio mínimo | Legacy exigía criterio; producto: Buscar vacío lista todos (paginado) | G4 busca sin filtro | G2 lista paginada |
| Paciente obito | Filtro `obitoBoolean=false` en buscar | G2 excluir | G2 |
| Selección requerida antes otorgar | Debe seleccionar un paciente. | G4 (ya T5) | G3 |
| Un solo match auto | Legacy puede autoseleccionar | v1: **siempre dialog** (patrón hab-buscadores) | G2 |

## Búsqueda convenio

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Sin criterio mínimo | Legacy exigía texto; producto: Buscar vacío lista todos (paginado) | G4 busca sin filtro | G2 lista paginada |
| Convenio no vigente | Convenio no vigente | G4 dialog color rojo + WARN al seleccionar | G2 flag |
| Atención suspendida | Convenio con atención suspendida | amarillo + WARN | G2 |
| Plan suspendido | Plan convenio suspendido | al cambiar combo plan | G2/G4 |
| Sin plan elegido | Requerido al consultar grilla (T5) | G4 | G2 |

## North — afiliado / documento

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Nro afiliado / documento | HIS `disabled` salvo elegibilidad WS | G4 **disabled** v1 (se llenan al elegir paciente) | diferido WS |
| Nro afiliado elegibilidad | `NRO_AFILIADO_ELEGIBILIDAD_REQUIRED` | **diferido** hijo elegibilidad | **diferido** |
| Nro documento elegibilidad | `NRO_DOCUMENTO_ELEGIBILIDAD_REQUIRED` | **diferido** | **diferido** |
| Máscara afiliado | `inputMask` según convenio | **diferido** con elegibilidad | N/A |

## West — rango horas

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Hasta ≤ desde | El horario hasta debe ser posterior al horario desde | G4 (heredado T5) | G2 query params |

## Otros centros

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Sin paciente | Tabla vacía / no consulta | G4 silent | G2 skip si !idPaciente |
| Sin prestación | Legacy requiere prestación para query | G4 WARN al cargar | G2 |
| Asignar slot ocupado | Turno ocupado (T5) | toast | G3 reserva |

## Ficha west

| Regla | Mensaje legacy | UI | API |
|-------|----------------|----|-----|
| Sin paciente | Panel vacío | mostrar `—` o vacío | N/A |
| Teléfonos / mails | Join tablas contacto Oracle | v1: null si tablas ausentes PG | G1 gap documentado |

## Checklist anti-omisión T5.1

- [ ] Reservar/otorgar usan `idPaciente`/`idConvenio`/`idPlan` del north, no constantes demo
- [ ] Limpiar paciente resetea validación T5 consultar
- [ ] Elegibilidad WS: filas **diferido** explícitas (icono oculto v1)
- [ ] Consultar agenda otros centros: no implementar sin slug consultas
