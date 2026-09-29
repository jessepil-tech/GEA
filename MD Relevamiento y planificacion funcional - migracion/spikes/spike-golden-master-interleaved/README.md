# Spike — Golden master `f_get_interleaved_2_5`

`phase_id:` **`sdd.hospital.spike-golden-master-interleaved`**

Fecha: 2026-08-13  
Estado: **PASS** (captura Oracle + replay Java por HEX)

## Objetivo

Segundo spike del método golden master: rutina **pura** (sin `SYSDATE` ni tablas),
alta en reportes BIRT (98 usos).

## Rutina (extracto)

Codifica pares de dígitos → `CHR(n)` con reglas `< 32 → +192`, `= 32 → 159`, envuelve
con `CHR(167)` / `CHR(172)`. Charset BD: **WE8MSWIN1252**.

## Evidencia

| Artefacto | Ruta |
|-----------|------|
| Fixture | `tools/golden-master/fixtures/f_get_interleaved_2_5.json` (compara `outputHex`) |
| Fuente | `tools/golden-master/fixtures/f_get_interleaved_2_5.sql` |
| Captura | `tools/golden-master/src/CaptureInterleaved25.java` |
| Impl | `Hospital-Api/.../general/Interleaved25Encoder.java` |
| Test | `Interleaved25EncoderGoldenMasterTest` |

## Lecciones

1. Comparar por **HEX de bytes**, no por `String` Unicode (p. ej. `CHR(159)` ≠ encoding
   windows-1252 de U+009F).
2. Longitud impar: el último dígito se **descarta** (igual que el PL/SQL).
3. `trim` vacío / null → null.

## Resultado

**PASS** — 16 casos vs Oracle 11.2.

## Siguiente

- Spike de diseño `f_next_id_tabla` (secuencias / contadores), o
- Función PostgreSQL de interleaved + edad para BIRT (`migracion-package-general.md`).
