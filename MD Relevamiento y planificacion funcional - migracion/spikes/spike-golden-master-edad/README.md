# Spike — Golden master `f_get_edad_anio`

`phase_id:` **`sdd.hospital.spike-golden-master-edad`**

Fecha: 2026-08-13  
Estado: **PASS** (captura Oracle + replay Java)

## Objetivo

Probar el método del dossier §6.2 con una rutina pura de `TS.GENERAL`:

1. Capturar salidas en Oracle **11.2** (VPN).
2. Reimplementar en Quarkus/Java sobre Postgres (sin cablear al 11.2).
3. Comparar con reloj congelado al `SYSDATE` de captura.

## Rutina

```sql
function f_get_edad_anio (adt_fecha_nac in date) return number IS
  ln_diferencia number;
begin
  if (adt_fecha_nac is not null) then
    ln_diferencia := FLOOR(MONTHS_BETWEEN(sysdate, adt_fecha_nac) / 12);
  end if;
  return ln_diferencia;
end;
```

| Hallazgo | Implicancia |
|----------|-------------|
| Un solo argumento DATE | No hay “as of” explícito |
| Usa **`SYSDATE`** | Fixtures deben guardar timestamp de captura; tests congelan `Clock` |
| Cálculo puro | Ideal para el primer spike (sin tablas de negocio / PHI) |

## Evidencia

| Artefacto | Ruta |
|-----------|------|
| Fixture JSON | `tools/golden-master/fixtures/f_get_edad_anio.json` |
| Fuente extraída | `tools/golden-master/fixtures/f_get_edad_anio.sql` |
| Captura JDBC | `tools/golden-master/src/CaptureEdadAnio.java` |
| Impl Java | `Hospital-Api/.../general/EdadAnioCalculator.java` |
| Test replay | `EdadAnioCalculatorGoldenMasterTest` |

Captura: `oracleVersion=11.2.0.4`, `capturedAtOracleSysdate=2026-08-13T22:09:57`,
12 casos (fechas sintéticas + null).

## Resultado

**PASS** — fórmula Java `FLOOR(months_between_oracle / 12)` iguala los 12 casos.

## Siguiente

1. Misma metodología con `f_get_interleaved_2_5` (sin `SYSDATE`).
2. Port a función PostgreSQL cuando BIRT lo exija (`migracion-package-general.md`).
3. Spike de `f_next_id_tabla` (diseño, no solo traducción).

## Relación con el plan A

Confirma: Oracle 11.2 = oráculo vía captura; Quarkus no necesita datasource 11.2;
UAT/corte del producto nuevo sigue aislado (ver `analisis-oracle19-vs-postgres.md`).
