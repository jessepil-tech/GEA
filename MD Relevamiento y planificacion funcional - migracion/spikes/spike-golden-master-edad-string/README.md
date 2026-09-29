# Spike — Golden master `f_get_edad_string`

`phase_id:` **`sdd.hospital.spike-golden-master-edad-string`**

Fecha: 2026-08-14  
Estado: **PASS** (captura Oracle + replay Java)

## Objetivo

Completar el top BIRT de edades tras `f_get_edad_anio`: misma metodología golden master
con `TS.GENERAL.f_get_edad_string` (22 reportes).

## Rutina (extracto)

Usa `MONTHS_BETWEEN(SYSDATE, fecha)` y ramifica años / meses / días. Rarezas a preservar:

| Caso Oracle | Salida |
|-------------|--------|
| Exactamente 12 meses | `1 año` |
| 13 meses | `1 años` (bug legacy) |
| 1 día | `1 dias` (siempre plural) |
| `NULL` | VARCHAR2 vacío → JDBC `null` |

## Evidencia

| Artefacto | Ruta |
|-----------|------|
| Fixture + edges | `tools/golden-master/fixtures/general/f_get_edad_string.json` |
| Fuente | `tools/golden-master/fixtures/general/f_get_edad_string.sql` |
| Inventario GENERAL | `tools/golden-master/fixtures/general/general_routines.csv` (85) |
| Impl Java | `Hospital-Api/.../EdadStringCalculator.java` |
| Test | `EdadStringCalculatorGoldenMasterTest` |

Captura: `oracleVersion=11.2.0.4`, `capturedAtOracleSysdate=2026-08-14T10:30:09`.

## Relacionado (misma ventana VPN)

- `f_get_edad_full_string` / `f_get_edad_ano_mes_dia` fixtures + sources en `fixtures/general/`
- `f_get_domicilio_persona` **no** está en GENERAL → `TS.PERSONAS` (`fixtures/personas/`, sin PII)
