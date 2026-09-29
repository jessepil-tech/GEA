---
title: Cortes — Administración General (maestros)
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.relevamiento-maestros.cortes
---

# Cortes propuestos — maestros / ABM básicos

`phase_id:` **`sdd.hospital.relevamiento-maestros.cortes`**  
Fecha: **2026-09-16**  
Solo después de pipeline + maestros.

Orden M1a→M1c sale del pipeline A1–A9 **cerrado** (centro → servicio → especialidad),
no de firmas PERSONAS/GENERAL (`muestra`). No abrir spec del tile entero. Techo por
corte: 8 xhtml / 15 beans / 20 firmas ([`loop-migracion-corte.md`](../../canon/loop-migracion-corte.md)).

BODY: `PERSONAS` / `GENERAL` **on-demand**. No tomar `TURNOS` (reservado). No
segundo Flyway sobre tablas de otro owner el mismo sprint.

| Orden | Corte | Qué incluye | Prerrequisito | Slug / estado |
|-------|-------|-------------|---------------|---------------|
| M0 | Relevamiento capa 3 | Esta carpeta | — | **Hecho** 2026-09-16 |
| M1a | Tronco territorial — centro | ABM `centro_atencion` (10203) · shell+datos+buscador | Identity tile; seed grp/prov/loc | **gate-done** 2026-09-18 [`maestros-m1a-centro/`](../../cortes/maestros/maestros-m1a-centro/) |
| M1b | Servicio (catálogo) + vínculo | `servicio` 10002. `servicio_centro` 10204 hoja datos | centro M1a para el vínculo | **gate-done** 2026-09-18 [`maestros-m1b-servicio/`](../../cortes/maestros/maestros-m1b-servicio/) |
| M1c | Especialidad | 10003 | M1a/M1b | **gate-done** 2026-09-18 [`maestros-m1c-especialidad/`](../../cortes/maestros/maestros-m1c-especialidad/) |
| M2 | Catálogos globales | tipo_documento, nacionalidad, provincia, motivo (si no basta T1) | M1 o paralelo si no escribe las mismas tablas | Diferido |
| M3 | Personal ABM | xhtml `personal/personal` (10208) — **no** es T1 picker | M1 | Diferido; T1 sigue **gate parcial** |
| M4 | Paciente ABM | 10241 + ciclo 10242/10243/10244 | M2 tipo doc | Diferido; AGI/agenda solo consumen |
| M5 | Convenio / entidad / plan | Completar CU-A (planes, entidad 10603) | M1 | CU-A **parcial**; no reabrir módulo facturación |
| M6 | Nomenclador | sección + `cod_prestacion` (+ req realización si el CU otorga lo exige en ABM) | M1 | Diferido |
| M7 | Puesto mostrador | recepción 10205, caja 10206 | M1; no pisar P3 | Con [`paridad-recepcion-gate/`](../../cortes/recepcion/paridad-recepcion-gate/) |
| M8 | Parámetros | `paramGeneral` y hermanos **uno por corte** | M1 | Diferido |
| — | Anunciador / terminal | 10211–10216 | — | **P3** [`anunciador-agi-config-abm/`](../../cortes/anunciador/anunciador-agi-config-abm/) — no duplicar |
| — | Hab / horarios / grilla | 10810–10828 | — | Stream **Turnos** T2–T4 **gate-done**; T6 diferido |
| M9 | Resto `modulos` + dominios + interfaces | Farm, compras, CIE, forms HC, padrones, migración | A–C del stream dueño | **N/A** como corte de este relevamiento |

## Qué no hacer primero

- Un SDD “migrar configuracion” con 223 hojas.
- Tratar seed de centro/servicio como M1 cerrado.
- Portar `PERSONAS` BODY completo.
- Meter Farm/Compras en M1 porque el menú cuelga de `modulos`.
- Segundo owner sobre `ts.turno`.

## Primer CU (recomendación)

**M1a** — alta real en `ts.centro_atencion` desde UI Hospital-Web. Evidencia = id de
fila nacido del CU. Fixture de grp/provincia/localidad = `Hospital-Api/infrastructure/src/main/resources/db/dev-seed/`
(no cierra el ABM). Flyway n/a. M1a **gate-done**. M1b **gate-done**. M1c **gate-done** 2026-09-18.
