# Spike — Golden master edades (`p_get_edad` + full / ano-mes-dia)

`phase_id:` **`sdd.hospital.spike-golden-master-get-edad`**

Fecha: 2026-08-14  
Estado: **PASS**

## Qué se portó

| Rutina | Java | Fixture |
|--------|------|---------|
| `p_get_edad` | `GetEdadCalculator` | `p_get_edad.json` |
| `f_get_edad_full_string` | `EdadFullStringCalculator` | `f_get_edad_full_string.json` |
| `f_get_edad_ano_mes_dia` | `EdadAnoMesDiaCalculator` | `f_get_edad_ano_mes_dia.json` |

## Hallazgo crítico

La rama de días usa `ADD_MONTHS(fecha_nac, TRUNC(MONTHS_BETWEEN(...)))`.
Si la fecha de nacimiento es **último día del mes** (p. ej. 29-feb), Oracle ancla
en el **último día del mes destino** (29-feb + N → 31-jul), no en el mismo día
de calendario que `LocalDate.plusMonths`. Sin esa regla falla el golden master.

## Fuentes / tools

`tools/golden-master/fixtures/general/p_get_edad.sql` (+ siblings)  
`tools/golden-master/src/VpnAdvance.java`, `FindProveedorFull.java`
