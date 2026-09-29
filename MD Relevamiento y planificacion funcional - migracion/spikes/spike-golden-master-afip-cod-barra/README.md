# Spike — Golden master AFIP `f_get_cod_barra`

`phase_id:` **`sdd.hospital.spike-golden-master-afip-cod-barra`**

Fecha: 2026-08-14  
Estado: **PASS**

## Qué se capturó (VPN)

| Artefacto | Contenido |
|-----------|-----------|
| `f_get_cod_barra.json` | 18 OK (empresas 1/2/4 × A/B FACTURA × 3 CAI) + 2 errores |
| `empresa_afip_inputs.csv` | 19 empresas con CUIT |
| `f_get_tipo_comp_afip.sql` + `_map.csv` | Mapa A/B/C × FACTURA/ND/NC |
| `pf_get_digito_verificador.sql` | Algoritmo DV (privado; no invocable desde SQL) |

Comprobante (pto/CAI/fecha) **sintético**; CUIT real de `ts.empresa` (necesario para paridad AFIP).

## Java

`AfipCodBarraEncoder` + `AfipCodBarraEncoderGoldenMasterTest` → **PASS**.

## Nota

`f_gen_caract_cod_barra_ean_13` (tabla `CHAR_COD_BARRA`) quedó fuera de este spike;
no bloquea el código de barras AFIP de 40 dígitos.
