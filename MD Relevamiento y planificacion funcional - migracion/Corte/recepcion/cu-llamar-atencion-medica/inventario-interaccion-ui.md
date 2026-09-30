---
title: Inventario interacción UI — Lista espera atención médica
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.cu-llamar-atencion-medica
---

# Inventario interacción — E3

Fuente: `listaEsperaAtencionMedica.xhtml` · `BBListaEsperaAtencionMedica`.

**Prohibido:** botón Llamar inline en la grilla o en `/recepcion/espera-amb`.

## Norte

| Control | Disparador | Web E3 |
|---------|------------|--------|
| Especialidad | `selectOneMenu` + ajax `onEspecialidadChange` | combo filtra lista local |
| Pacientes | `selectOneMenu` + ajax `onPacientesChange` | combo filtra lista local (ítems = filas cargadas; catálogo HIS completo **diferido**) |
| Referencias | `onclick` popup | visible **disabled** · `diferido(cu-llamar-atencion-medica-norte)` |
| Turnos | `nuevoTurno()` | visible **disabled** · mismo diferido |
| Pacientes atendidos | `actBtnVerFormularios` | visible **disabled** |
| Historia clínica | `actBtnHistorico` | visible **disabled** |
| Turnos del dr | `rendered=false` | **N/A** |

## Cola personal (v1 Llamar)

| Control | xhtml | Disparador | Web E3 |
|---------|-------|------------|--------|
| Engranaje | `fa-gear` + `p:tieredMenu` overlay 240px | clic icon-only | `espera-personal-menu-trigger-{id}` |
| LLAMAR PACIENTE | menuitem + confirm JS | menú | menú → confirm → `POST …/espera-amb/{id}/llamar` |
| ATENDER / INFORMACIÓN / CI / HC | menuitem | menú | ítems **disabled** · `diferido(cu-llamar-atencion-medica-ampliar)` |
| `*` sobreturno | icon + tooltip 333px | hover | `*` en hora; panel motivo **diferido** |

## En atención / cola servicio

| Control | Web E3 |
|---------|--------|
| Tabla en atención | visible; menú Llamar **disabled** · `diferido(cu-llamar-atencion-medica)` Clarify C1 |
| Tabla servicio | **no render** (flag `seleccionaCualquierMed` apagado en TS) |

## E2E

Mismo disparador: engranaje → LLAMAR PACIENTE → Aceptar confirm → TV.
No POST suelto como cierre de E3 (el POST ya cerró `cola-b-llamar`).
