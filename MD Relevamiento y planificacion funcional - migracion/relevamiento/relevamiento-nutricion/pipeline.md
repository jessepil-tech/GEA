# Pipeline — Nutrición

`phase_id:` **`sdd.hospital.relevamiento-nutricion.pipeline`**  
Fecha: **2026-09-07**  
Padre: [`README.md`](README.md) · Proceso: [`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md)

Guion **configuración → internado → planilla**. Ninguna etapa en silencio.

---

## Diagrama

```text
Personal + rol NUTRICIONISTA
    → Permiso menú NUTRICION (130000) + ABM Config (11804)
    → Vínculo personal–servicio de internación → centros del gate
    → Maestros: ts.nutricion + ts.nutricion_no_compatible
    → Internación viva: sector / cama / censo (tmp_censo_cama)
    → Indicación médica: ts.det_indica_nutricion_int (escribe INTERNACIÓN, no este tile)
         ↓
Operación nutricionista: listado internado / vigentes / ajustes
         ↓
Planilla dieta del día: ts.dieta_internado + ts.det_dieta_internado
         ↓
Side-effects: BIRT Nutricion / NutricionInternado / IndicacionesVigentesNutricion
              · registro enfermería (id_det_indica_nutricion_int)
              · farmacia (generico_equiv.nutricion)
```

**Anti-sesgo:** la planilla del almuerzo es A7, no el módulo.

---

## Fases A1–A9

| # | Etapa | Legacy (evidencia) | Destino | Estado |
|---|-------|--------------------|---------|--------|
| A1 | Identidad / personal | Operador: `UserSession.isNUTRICIONISTA()` (`BBInicioNutricion`); centros vía `selectCentroAtencionDistinctPorPersonalInternacion` | JWT Identity; **sin** rol `NUTRICIONISTA`; `ts.personal` parcial (Turnos T1) | **No migrado** (rol + gate) |
| A2 | Roles / perfiles | Módulo `NUTRICION` 130000; ABM 11804 bajo Configuración | Tile `menuKey: NUTRICION` → `pending()` **disabled** | **Shell only** |
| A2b | Orientación | Padre operación = **130000**; padre ABM = **11800** `dominios_enfermeria` (no el xhtml `pages/nutricion`) | Tile HIS; hojas operación no en sidebar | **Mapa O1**; rutas Web **no** |
| A3 | Maestros | `ts.nutricion` (dieta/item: kcal, CHO, proteínas, ClNa, flags de carga); `ts.nutricion_no_compatible` | Inventario Oracle/PG **sí**; Flyway Api **no**; UI **no** | **DDL inventario; ABM no** |
| A4 | Habilitación | No hay `HAB_NUTRICION_*`. Enciende: personal en servicio de internación + centro en sesión | Mismo vínculo `personal_servicio` que internación/enfermería | **No migrado** (comparte internación) |
| A5 | Parámetros / reglas | Tipo dieta planilla (almuerzo-cena / desayuno-merienda); menú VIP/especial/pediátrico; incompatibilidades entre ítems | Constantes en `BBDietaInternado`; no tabla de tipos | **No migrado** |
| A6 | Generación | Planilla: filas `dieta_internado` / `det_dieta_internado` por fecha+centro+sector. Listado: lee censo, **no** genera internación | Sin Api | **No migrado** |
| A7 | Operación diaria | `planillaNutricion.xhtml` (`BBDietaInternado`); `nutricion.xhtml` (`BBNutricionInternado`); `indicacionesVigentes.xhtml` | Ausente | **No migrado** |
| A8 | Ciclo de vida | Indicación: vigencia / suspende (`fecha_suspende`, `estado`); ajuste `ajuste_det_indica_nutricion`; repetición `hs_repite_indica_nutricion_int` | Ausente | **No migrado** |
| A9 | Side-effects | BIRT 3 diseños; HC eventos `INDICACIONES NUTRICION`; `det_reg_enf_int`; flag farmacia `generico_equiv.nutricion` | Reports: diseños **listados no migrados**; resto no | **No migrado** |

N/A con evidencia:

| Etapa | N/A | Evidencia |
|-------|-----|-----------|
| A6 “grilla de turnos” | N/A | No hay `f_gen_*` de nutrición; la oferta es el censo de internación |
| Consultas 130301/130302 | Prestadas | Paths `admisionInternados/*` — módulo admisión, no dietética |

---

## PASS / gaps de esta fase

| Criterio | Resultado |
|----------|-----------|
| A1–A9 inventariadas | **Sí** |
| A1–A5 sin tratar seed como “cerrado” | **Sí** — ver [`maestros.md`](maestros.md) |
| Operación A7 como única evidencia de paridad | **Prohibido** — sin A1–A4 no hay nutricionista ni catálogo |
