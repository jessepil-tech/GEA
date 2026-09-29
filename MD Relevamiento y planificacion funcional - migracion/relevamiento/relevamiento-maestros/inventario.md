---
title: Inventario — Administración General (ABM)
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.relevamiento-maestros.inventario
---

# Inventario de capacidades — Administración General

`phase_id:` **`sdd.hospital.relevamiento-maestros.inventario`**  
Fecha: **2026-09-16**  
Padre: [`README.md`](README.md)

Fuente: `dump-menu-aplicacion.csv` (app HOSPITAL=2), descendientes de id=10000.
**223** hojas con `ACCION`. Beans: no listados (legacy no montado); al firmar un
corte usar `./tools/indice-legacy.sh --semilla <basename>`.

Jobs del circuito (capa 3b′): [`relevamiento-procesos-programados/`](../relevamiento-procesos-programados/)
— `CheckHabTurnosJob` (hab), `EnvioMailsJob`/`EnvioSmsJob`/`ProcesarSmsJob` (servers
10016/10017), `MigraPersonalV8AV9Job` (personal). Frecuencia = `ts.tarea_programada`.

---

## 1. configuracion_general — 22 hojas

Catálogos y parámetros **hospital-wide**. Escritura típica: ABM `ts.*` vía PERSONAS/GENERAL.

| id | Clave menú | ACCION | Escritura | Estado |
|----|------------|--------|-----------|--------|
| 10002 | servicio | `/pages/configuracion/servicio` | Sí | **gate-done** [`maestros-m1b-servicio`](../../cortes/maestros/maestros-m1b-servicio/) |
| 10003 | especialidad | `/pages/configuracion/especialidad` | Sí | **gate-done** [`maestros-m1c-especialidad`](../../cortes/maestros/maestros-m1c-especialidad/) |
| 10004 | tipo_documento | `/pages/configuracion/tipoDocumento` | Sí | **Diferido** M2 |
| 10005 | nacionalidad | `/pages/configuracion/nacionalidad` | Sí | **Diferido** M2 |
| 10006 | provincia | `/pages/configuracion/provincia/provincia` | Sí | **Diferido** M2 |
| 10008 | motivo | `/pages/configuracion/motivo/motivo` | Sí | Seed T1; ABM diferido |
| 10009–10026, 11808 | param_* + forma_pago + mail/sms + doc menú + farmacia_externa | `paramGeneral` … `paramHistoriaClinica` | Sí | **Diferido** M8 |

## 2. configuracion_operativa — 36 hojas — **ABM básicos**

| Familia | ids | ACCION (muestra) | Escritura | Estado |
|---------|-----|------------------|-----------|--------|
| Centro | 10203, 10217, 10219 | `centroAtencion`, `grpCentroAtencion`, `unidadNegocio` | Sí | **gate-done** [`maestros-m1a-centro`](../../cortes/maestros/maestros-m1a-centro/) (10203); grp [`maestros-m1a-grp`](../../cortes/maestros/maestros-m1a-grp/) |
| Vínculo | 10204, 10292 | `servicioCentro`, `grpServicioCentro` | Sí | **gate-done** [`maestros-m1b-servicio`](../../cortes/maestros/maestros-m1b-servicio/) (10204 hoja datos); tabs [`maestros-m1b-servicio-tabs`](../../cortes/maestros/maestros-m1b-servicio-tabs/); grp 10292 fuera |
| Puesto / dinero / TV | 10205, 10206, 10211, 10212, 10215, 10216 | recepción, caja, anunciador, diccionario, terminales | Sí | Recepción/caja diferido; anunciador **P3** |
| Personal | 10208 | `personal/personal` | Sí | **Diferido** M3 (T1 ≠ este ABM) |
| Paciente | 10241–10245 | paciente, desconfirmación, habilitar, tipo | Sí | **Diferido** M4 |
| Documentación | 10221, 10222, 10261, 10262 | tipo doc adi, doc requerido, modelos | Sí | Con convenio/HC |
| Equipo | 10281–10283 | tipo / item / subtipo | Sí | Duplicado depósito |
| Otros | 10213, 10214, 10218, 10223, 10244, 10291, 10293, 10294, 10296–10300 | CP, tipo ambiente, instit. derivante, unificación, encuestas, tareas | Sí | Hijo por dominio |

## 3. nomenclador — 6 hojas

| id | Clave | ACCION | Estado |
|----|-------|--------|--------|
| 10402 | seccion_nomenclador | `seccionNomen` | **Diferido** M6 |
| 10403 | cod_prestacion | `codPrestacion` | **Diferido** M6 |
| 10404 | unidad_arancel | `unidadArancel` | Con M6 |
| 10405 | consentimiento_informado | `consentimientoInformado` | Diferido |
| 10406 | tipo_extra | `tipoExtra` | Diferido |
| 10407 | req_realizacion | `requerimientoRealizacion` | T5.1e-q **lee**; ABM **no** |

## 4. facturacion (bajo este tile) — 24 hojas

Convenio 10604 = CU-A **parcial**. Entidad, listas de precio, padrones, consultas,
fmt factura, empresa, cotización, tarjeta, coeficientes: **diferido** stream Facturación
salvo que un CU clínico las necesite (entonces hijo, no el tile).

## 5. dominios_medicos — 23 · dominios_enfermeria — 5

Form HC, CIE, indicaciones, guías, tipos cirugía/CP, nutrición **11804**, forms
enfermería: **N/A como corte de Maestros**. Viajan con HC / Internación / Nutrición /
Cirugía. Nutrición ABM ya está en [`relevamiento-nutricion/`](../relevamiento-nutricion/) N1.

## 6. interfaces_migracion — 6 hojas

Carga masiva pacientes/personal/proveedor/atención/internación + tablas conversión.
**No** es ABM de mostrador. Diferido; no mezclar con M4/M3.

## 7. modulos — 101 hojas (no es “un ABM”)

| Subárbol | Qué es | Qué hacer |
|----------|--------|-----------|
| turnos 10801 | Hab, horarios, call center, feriados, **generar/suspender/eliminar agenda** | Ya T2–T4; T6 padre diferido; no recontar |
| admision 11350 | Camas, sectores, tipos internación | Stream Internación |
| deposito 11000 | Ítems, stock, vademécum | Stream Farmacia |
| compras 11100 | Proveedor, centro compra | Stream Compras |
| acreditacion 11201 | Categorías RRHH / prescriptor | Duplica ACREDITACION |
| contable / cobranza / cirugía / infecto / IIBB | Dominio | Stream dueño |

Filesystem `pages/configuracion` (224 xhtml / 562 beans) **incluye** popups e includes
que el menú no lista. Al firmar universo: semilla = `ACCION` + 1 hop; techo 8 xhtml.

## Packages

| Package | Uso en este tile |
|---------|------------------|
| `PERSONAS` | Personal, paciente, unificación |
| `GENERAL` | `next_id`, parámetros, motivos |
| `TURNOS` | Solo hojas 10801–10828 |
| `ANUNCIADORES` | 10211–10216 |
| `FARMACIAS` / `COMPRAS` / `ADMISION` / `FACTURACION` | Hojas `modulos` y facturación |
| `TBL_AUD_*` | Si el ABM escribe tabla auditada → `diferido(auditoria)` o portar |

Port **on-demand por firma** del CU. Prohibido “traducir PERSONAS entero”.
