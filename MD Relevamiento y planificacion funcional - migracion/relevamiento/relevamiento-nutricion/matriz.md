# Matriz — Nutrición (legacy ↔ destino)

`phase_id:` **`sdd.hospital.relevamiento-nutricion.matriz`**  
Fecha: **2026-09-07**  
Padre: [`README.md`](README.md)

Leyenda: **migrado** · **parcial** · **diferido(slug)** · **WAIVE** · **N/A** · **no**.

---

## UI / menú

| Capacidad | Destino | Estado |
|-----------|---------|--------|
| Tile HIS `NUTRICION` | `hospital-menu.catalog.ts` `moduleOnly` | **parcial** (visible disabled) |
| Gate `inicioNutricion` | — | **no** |
| Planilla / internado / vigentes | — | **no** |
| ABM Config 11804 | — | **no** |
| Consultas 130301/130302 | Admisión | **N/A** (otro módulo) |
| Prescripción desde internación | Módulo INTERNACION | **diferido(internacion-indicaciones)** |

---

## Schema `ts` (inventario Oracle = PG)

| Tabla | Flyway Api | Estado |
|-------|------------|--------|
| `nutricion` | no | **diferido(nutricion-maestros)** |
| `nutricion_no_compatible` | no | **diferido(nutricion-maestros)** |
| `det_indica_nutricion_int` | no | **diferido(internacion-indicaciones)** |
| `ajuste_det_indica_nutricion` | no | **diferido(internacion-indicaciones)** |
| `hs_repite_indica_nutricion_int` | no | **diferido(internacion-indicaciones)** |
| `dias_repite_indica_nutri_int` | no | **diferido(internacion-indicaciones)** |
| `dieta_internado` | no | **diferido(nutricion-planilla)** |
| `det_dieta_internado` | no | **diferido(nutricion-planilla)** |
| `tmp_censo_cama` (cols plan nutrición) | no | **diferido(internacion-censo)** |
| `generico_equiv.nutricion` | no | **diferido(farmacia)** — flag, no este tile |
| `centro_atencion` | V28 | **parcial** (DDL; ABM diferido) |
| `paciente` | V31 | **parcial** (AGI) |

Sin **WAIVE**: legacy tiene la capacidad.

---

## Identity / seguridad

| Capacidad | Destino | Estado |
|-----------|---------|--------|
| Rol `NUTRICIONISTA` | — | **diferido(nutricion-identity-rol)** |
| Menú 130000 en JWT/perfiles | Identity menús (oleada B+) | **no** |

---

## Reports

| Diseño | Hospital-Reports | Estado |
|--------|------------------|--------|
| `Nutricion.rptdesign` | no | **diferido(birt-nutricion)** |
| `NutricionInternado.rptdesign` | no | **diferido(birt-nutricion)** |
| `IndicacionesVigentesNutricion.rptdesign` | no | **diferido(birt-nutricion)** |
