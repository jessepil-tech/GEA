# Spike — EAN-13 `f_gen_caract_cod_barra_ean_13` + `CHAR_COD_BARRA`

`phase_id:` **`sdd.hospital.spike-golden-master-ean13`**

Fecha: 2026-08-14  
Estado: **capturado — catálogo vacío (sin GM usable)**

## Hallazgo VPN

| Ítem | Valor |
|------|-------|
| `TS.CHAR_COD_BARRA` | **0 filas** (`COUNT(*)`, `num_rows=0`, last_analyzed 2024-09-10) |
| Llamada con dígitos | **ORA-01403: no data found** (línea SELECT del catálogo) |
| Input null | ORA-06502 |
| Referencias PL/SQL | Solo `GENERAL` (+ trigger audit `TAUD_CHAR_COD_BARRA`) |
| Uso en ATENCION | Llamada **comentada** en históricos RDBMS (`--ls_cod_barra_det := ...ean_13`) |

Sin filas en `CHAR_COD_BARRA` **no hay golden master de salida positiva**. El algoritmo + helpers sí están extraídos.

## Artefactos

| Archivo | Contenido |
|---------|-----------|
| `f_gen_caract_cod_barra_ean_13.sql` | Función |
| `pf_obtiene_caracter.sql` | Helper left/right set |
| `pf_get_digito_verificador.sql` | DV (compartido con AFIP) |
| `char_cod_barra.csv` | Vacío (0 data rows) |
| `char_cod_barra_columns.csv` / `.ddl.sql` | Esquema |
| `char_cod_barra_STATUS.md` | Estado |
| `f_gen_caract_cod_barra_ean_13.json` | `catalogStatus=EMPTY` + casos error |

## Implicancia para el destino

1. **No bloquear** migración por EAN-13 caracterizado.
2. Si algún módulo lo reactiva: hace falta **seed del catálogo** (10 dígitos 0–9 con prefix/left/right/set_a/set_b) — no está en el monorepo.
3. Preferir Interleaved 2/5 + AFIP 40 dígitos (ya con GM PASS) para barcodes vivos.

Tool: `tools/golden-master/src/CaptureEan13.java` / `ProbeEanEmpty.java`.
