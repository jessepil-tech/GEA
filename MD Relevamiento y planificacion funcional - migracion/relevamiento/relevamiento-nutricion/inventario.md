# Inventario — Nutrición

`phase_id:` **`sdd.hospital.relevamiento-nutricion.inventario`**  
Fecha: **2026-09-07**  
Padre: [`README.md`](README.md)

---

## Menú HIS (O1, HOSPITAL=2)

### Operación — padre `NUTRICION` 130000

| Id | Clave | Path | Bean |
|----|-------|------|------|
| 130000 | NUTRICION | `/pages/nutricion/inicioNutricion` | `BBInicioNutricion` |
| (home interno) | — | `/pages/nutricion/inicio` | vacío (`contractDefault`) post-gate |
| 130200 | nutricion (grupo) | — | — |
| 130201 | planilla_nutricion | `/pages/nutricion/planillaNutricion` | `BBDietaInternado` |
| 130202 | nutricion | `/pages/nutricion/nutricion` | `BBNutricionInternado` |
| 130203 | indicaciones_vigentes | `/pages/nutricion/indicacionesVigentes` | `BBIndicacionesVigentes` |
| 130300 | consultas (grupo) | — | — |
| 130301 | altas_entre_fechas | `/pages/admisionInternados/consultasAdmision/altasEntreFechas` | **Admisión** (prestada) |
| 130302 | consulta_generador_censo | `/pages/admisionInternados/consultaCensoAgrupado` | **Admisión** (prestada) |

### Configuración — no es el tile

| Id | Clave | Path | Bean |
|----|-------|------|------|
| 11800 | dominios_enfermeria | (grupo bajo 10000 ADMIN) | — |
| 11804 | nutricion | `/pages/configuracion/nutricion/nutricion` | `BBNutricion` |
| — | datos | `/pages/configuracion/nutricion/datosNutricion` | `BBDatosNutricion` |
| — | no compatible | `/pages/configuracion/nutricion/nutricionNoCompatible` | `BBNutricionNoCompatible` |

Pantallas internación **fuera del tile** (escriben el mismo `det_indica_nutricion_int`):
`pages/indicaciones/nutricion.xhtml`, `BBIndicacionesInternacion`, resumen
`pages/internacion/resumenIndicacionesNutricion.xhtml`.

---

## Capacidades

| Capacidad | Qué hace | Tablas `ts` |
|-----------|----------|-------------|
| Gate | Centro + solo nutricionista | sesión; `personal_servicio` |
| ABM catálogo | Alta/baja/mod de ítem dieta | `nutricion` |
| Incompatibilidades | Pares de ítems que no coexisten | `nutricion_no_compatible` |
| Listado internado | Censo por sector/servicio; ver indicaciones; ajuste; alergias | `tmp_censo_cama`, `det_indica_nutricion_int`, `ajuste_det_indica_nutricion` |
| Indicaciones vigentes | Misma familia que listado; filtro vigencia | idem |
| Planilla | Dieta del día por sector (tipos almuerzo/cena vs desayuno/merienda; menú VIP/especial/pediátrico) | `dieta_internado`, `det_dieta_internado` |
| Imprimir | BIRT | diseños abajo |

---

## BIRT (no migrados)

| `reportId` legacy | Diseño | Quién imprime |
|-------------------|--------|----------------|
| NutricionInternado | `NutricionInternado.rptdesign` | `BBNutricionInternado.actionBtnImprimir` |
| IndicacionesVigentesNutricion | `IndicacionesVigentesNutricion.rptdesign` | `BBIndicacionesVigentes` |
| Nutricion | `Nutricion.rptdesign` | `BBDietaInternado` (planilla) |

Listados en [`listado-reportes-birt-legacy.md`](../listado-reportes-birt-legacy.md) (unchecked).

---

## Packages / persistencia

| Pieza | Mecanismo |
|-------|-----------|
| Catálogo `Nutricion` | Hibernate `NUTRICION` / `ImpBusNutricion` (facade, no package dedicado) |
| Indicaciones internación | Hibernate `DetIndicaNutricionInt` + packages **ADMISION** / **PERSONAS** / **ATENCION** / **FARMACIAS** / **HISTORIA_CLINICA** (side-effects) |
| Rol en atención | `rol_funcional in ('MEDICO','ENFERMERO','NUTRICIONISTA')` (package ATENCION) |

Prohibido inventar tablas `public` para el análisis.
