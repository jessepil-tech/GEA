# Cortes — Nutrición (Fase D)

`phase_id:` **`sdd.hospital.relevamiento-nutricion.cortes`**  
Fecha: **2026-09-07**  
Padre: [`README.md`](README.md)

Orden por dependencia **después** de A–C. **No** abre spec. Un corte ≠ el módulo.

---

## Propuesto

| Corte | Qué | Depende de | Notas |
|-------|-----|------------|--------|
| **N0** | Gate: centro + rol `NUTRICIONISTA` + tile deja de ser solo `pending()` | Identity rol + menú 130000 | Paridad `BBInicioNutricion` |
| **N1** | Flyway `ts.nutricion` + `nutricion_no_compatible` + ABM bajo **Configuración** (11804), no como hijo inventado del tile | N0 (permiso); catálogo | Primer CU de **negocio** del módulo |
| **N2** | Lectura censo internación (sector/cama) para el nutricionista | **Internación** (censo) — puede ser SDD de internación, no de este tile | Bloqueante de N3–N5 |
| **N3** | Listado internado (`nutricion.xhtml`) lectura de indicaciones | N1 + N2 | Consumo; no prescribir |
| **N4** | Indicaciones vigentes + ajuste | N3 | Escribe `ajuste_det_indica_nutricion` |
| **N5** | Planilla dieta (`dieta_internado`) | N2 + N1 (ítems) | Operación diaria visible |
| **N6** | BIRT tres diseños | N3–N5 datos reales | Hospital-Reports |

Fuera de esta escalera (otro módulo):

| Corte ajeno | Qué |
|-------------|-----|
| Internación — indicar nutrición | Médico escribe `det_indica_nutricion_int` |
| Admisión 130301/130302 | Consultas prestadas; no dietética |
| Farmacia `generico_equiv.nutricion` | Filtro de ítems nutricionales |

---

## Qué no hacer

- Spec “migrar Nutrición” entero.
- Empezar por N5 planilla con seed de dietas y sin N0/N1/N2.
- Colgar el ABM 11804 como hoja del tile 130000 (rompe orientación: padre = Configuración).
- Tratar 16 usuarios de producción como “prioridad baja = WAIVE”.
