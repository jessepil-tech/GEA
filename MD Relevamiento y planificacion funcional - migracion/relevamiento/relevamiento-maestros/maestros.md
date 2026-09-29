---
title: Maestros / seguridad — Administración General
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.relevamiento-maestros.maestros
---

# Maestros y seguridad — Administración General

`phase_id:` **`sdd.hospital.relevamiento-maestros.maestros`**  
Fecha: **2026-09-16**  
Padre: [`README.md`](README.md)

Tabla anti-omisión (Fase C). **Copia Oracle / seed ≠ paridad de ABM.**

En este módulo la Fase C **es** el inventario: cada fila es a la vez maestro de
otro circuito.

| Maestro / permiso | ¿Bloquea operación HIS si falta? | ¿Existe CU/SDD? | Estado migración |
|-------------------|----------------------------------|-----------------|------------------|
| Perfil menú `ADMINISTRACION_GENERAL_NA` | Sí — no entra al tile | Shell Web + dump O1 | **Parcial** — tile; M2 Identity menús pendiente |
| Usuario Identity ↔ `ts.personal` | Sí para ABM que auditan `actualizado_por` | T1 lookup | **Parcial** |
| `ts.centro_atencion` | Sí — casi todo FK | V28 (SDD Turnos); ABM M1a | **DDL**; ABM **gate-done** [`maestros-m1a-centro`](../../cortes/maestros/maestros-m1a-centro/) |
| `ts.servicio` | Sí | ABM M1b | **DDL**; ABM **gate-done** [`maestros-m1b-servicio`](../../cortes/maestros/maestros-m1b-servicio/) |
| `servicio_centro` (vínculo) | Sí — habilita el servicio en el centro | M1b | **gate-done** [`maestros-m1b-servicio`](../../cortes/maestros/maestros-m1b-servicio/) (tabs [`maestros-m1b-servicio-tabs`](../../cortes/maestros/maestros-m1b-servicio-tabs/)) |
| `ts.especialidad` | Sí para personal/agenda | M1c | **DDL**; ABM **gate-done** [`maestros-m1c-especialidad`](../../cortes/maestros/maestros-m1c-especialidad/) (vínculo [`maestros-m1c-especialidad-serv`](../../cortes/maestros/maestros-m1c-especialidad-serv/)) |
| `ts.personal` | Sí para otorgar, indicar, firmar | T1 **gate parcial** (picker, no ABM 10208) | **DDL+lookup**; ABM → M3 |
| `ts.paciente` | Sí | AGI + agenda ficha (consumo); ABM 10241 **no** | **Uso**; ABM → M4 |
| `ts.convenio` / plan | Sí al otorgar / facturar | [`cu-clinico-a-catalogo-abm/`](../../cortes/recepcion/cu-clinico-a-catalogo-abm/) | **Parcial** (slice) |
| `ts.prestacion` / sección nomenclador | Sí al otorgar | V31 DDL; ABM 10402/10403 **no** | **DDL**; ABM → M6 |
| `tipo_documento` / `nacionalidad` / `provincia` | Sí al alta persona | — | **Diferido** M2 (catálogos chicos) |
| `motivo` / `tipo_motivo_df` | Sí en suspender/sobreturno/etc. | T1 GET+seed | **Seed+GET**; ABM diferido |
| `ts.recepcion` | Sí cola / gate puesto | seed IT; ABM 10205 **no** | **Diferido** (con recepción-gate) |
| `caja` | Sí cobro | — | **Diferido** (stream Caja) |
| Anunciador / diccionario / terminal AG | Sí TV/tótem | [`anunciador-agi-config-abm/`](../../cortes/anunciador/anunciador-agi-config-abm/) P3 | **Active**; C5 **Diferido(`anunciador-agi-config-abm-c5`)** |
| `hab_turnos_*` | Sí oferta turnos | T2 **gate-done** | **Hecho** (no reabrir acá) |
| Parámetros `param_*` | Condicional (comportamiento HIS) | — | **Diferido** M8 |
| `server_mail` / `server_sms` | No para ABM; sí notificación | jobs mail/SMS | **Diferido**(jobs) |
| Dietas nutrición id=11804 | Sí planilla nutrición | relevamiento-nutricion N1 | **Diferido** (stream Internación/Nutrición) |
| Items / depósito / compras (hojas 11000–11137) | Sí Farm/Compras | — | **N/A este stream** — A–C del módulo dueño |
| Formularios HC / CIE (11400) | Sí HC | — | **N/A este stream** |
| SEGURIDAD (perfiles) | Sí quién ve el tile | oleada Identity; tile origin no ve SEGURIDAD | **Diferido** stream Seguridad |

## Duplicados de menú (no contar dos veces)

| Capacidad | En este tile | También en |
|-----------|--------------|------------|
| Personal | 10208 | ACREDITACION_PROFESIONALES 50102 (misma `ACCION`) |
| Servicio / servicio_centro | 10002 / 10204 | ADMISION_INTERNADOS 90007 / 90008 |
| tipo_ambiente | 10214 y 11355 | Admisión |
| tipo_equipo / equipo / sub_tipo_item | operativa 10280 y depósito 11040 | mismo xhtml |

Al cortar: **una** pantalla canónica; la otra hoja de menú reusa ruta.

## Regla de cierre

Cada familia M1–M8: **done / diferido(slug) / WAIVE / N/A**. WAIVE solo si el HIS
no tiene el ABM (aquí casi todos existen).
