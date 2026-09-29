# Hallazgos golden master / GENERAL–PERSONAS–BIRT (VPN 2026-08-14)

`phase_id:` **`sdd.hospital.hallazgos-golden-master-vpn-2026-08-14`**

Estado: **cerrado documental** (evidencia en repo; replay Java parcial PASS)  
Oráculo: Oracle **11.2.0.4** (`192.168.40.100:1521` / SID `HOSPROD`)  
Origen de la instancia: **copia diaria de producción** (datos y packages fiables para golden master / inventarios)  
Destino: Quarkus + PostgreSQL (plan A — ver `analisis-oracle19-vs-postgres.md`)

Complementa: [`migracion-package-general.md`](migracion-package-general.md),  
[`dossier-migracion.md`](dossier-migracion.md) §6.2,  
[`tools/golden-master/README.md`](../../tools/golden-master/README.md).

---

## 1. Resumen ejecutivo

1. **Oracle 11.2 = oráculo** vía captura JDBC (`ojdbc7`); Quarkus **no** necesita datasource 11.2.
2. **`TS.GENERAL`**: 85 rutinas (`ALL_PROCEDURES`), 256 args, PACKAGE 209 / BODY **4135** líneas.
3. **Top BIRT real** no es solo GENERAL: el #1 es `PERSONAS.f_get_persona_full` (~300 usos).
4. Varios `.rptdesign` llaman **`GENERAL.f_get_*` inexistentes** (ORA-00904) — deuda de reportes, no de port.
5. Edades + interleaved tienen **golden master PASS** en `Hospital-Api`.
6. IDs: inventario next-id listo; motor = `pf_next_id_tabla` (no el wrapper).

---

## 2. Inventario GENERAL (VPN)

| Métrica | Valor | Artefacto |
|---------|------:|-----------|
| Rutinas | **85** | `tools/golden-master/fixtures/general/general_routines.csv` |
| Argumentos | **256** | `general_arguments.csv` |
| Spec / body | 209 / 4135 | `fixtures/misc/package_source_sizes.csv` |

> El dossier/histórico decía “82 públicas / ~3.481 líneas”. Usar **85 / 4135** como cifra VPN.

Herramienta: `tools/golden-master/src/GeneralInventory.java`.

---

## 3. BIRT — usos reales vs fantasmas

Conteo local sobre `.rptdesign` del monorepo  
→ `tools/golden-master/fixtures/misc/birt_function_counts.csv`.

| Llamada en reportes | ~Usos | Realidad Oracle |
|---------------------|------:|-----------------|
| `ts.personas.f_get_persona_full` | **301** | Existe (`PERSONAS`) |
| `ts.general.f_get_edad_anio` | 121 | Existe — GM PASS |
| `ts.general.f_get_interleaved_2_5` | 98 | Existe — GM PASS |
| `ts.personas.f_get_matricula_full` | 82 | Existe — fuente capturada |
| `ts.personas.f_get_especialidad_full` | 56 | Existe — fuente capturada |
| `ts.personas.f_get_personal_matricula` | 54 | Existe — fuente capturada |
| `ts.personas.f_get_persona_telefono` | 54 | Existe — fuente capturada |
| `ts.general.f_get_edad_string` | 22 | Existe — GM PASS |
| `ts.general.f_get_domicilio_persona` | 11 | **No existe** (ORA-00904) |
| `ts.general.f_get_proveedor_full` | 4 | **No existe** (ORA-00904) |
| `ts.general.f_get_persona_full` | 1 | **No existe** (ORA-00904) |
| `ts.general.f_prestacion` | 5 | Existe — fuente + bordes sintéticos |
| `ts.general.f_concat_val_pos_dom_hc` | 3 | Existe — fuente + bordes |

### Fantasmas (probado JDBC)

`fixtures/misc/birt_ghost_probes.csv` + lista de reportes `birt_ghost_reports.csv`:

| Call | Error |
|------|-------|
| `ts.general.f_get_proveedor_full` | ORA-00904 |
| `ts.general.f_get_domicilio_persona` | ORA-00904 |
| `ts.general.f_get_persona_full` | ORA-00904 |

**Sustitutos:**

| Fantasma / doc antiguo | Sustituto vivo |
|------------------------|----------------|
| `GENERAL.f_get_proveedor_full` | `PERSONAS.f_get_persona_full` |
| `GENERAL.f_get_domicilio_persona` | `PERSONAS.f_get_domicilio_persona` (devuelve **VARCHAR2** calle, no `id_domicilio`) |
| Doc “proveedor_full” en top-11 | En realidad el volumen es `persona_full` |

Reportes Retención* / OrdenPago que usan fantasmas: **corregir SQL** al migrar BIRT/PG; no inventar wrappers en GENERAL.

---

## 4. Golden masters Java (Hospital-Api)

| Rutina | Fixture | Clase | Test | Estado |
|--------|---------|-------|------|--------|
| `f_get_edad_anio` | `fixtures/f_get_edad_anio.json` | `EdadAnioCalculator` | `EdadAnioCalculatorGoldenMasterTest` | PASS |
| `f_get_interleaved_2_5` | `fixtures/f_get_interleaved_2_5.json` | `Interleaved25Encoder` | `…Interleaved…` | PASS (HEX/bytes) |
| `f_get_edad_string` | `fixtures/general/f_get_edad_string.json` | `EdadStringCalculator` | `…EdadString…` | PASS |
| `p_get_edad` | `fixtures/general/p_get_edad.json` | `GetEdadCalculator` | `GetEdadCalculatorGoldenMasterTest` | PASS |
| `f_get_edad_full_string` | `…full_string.json` | `EdadFullStringCalculator` | idem | PASS |
| `f_get_edad_ano_mes_dia` | `…ano_mes_dia.json` | `EdadAnoMesDiaCalculator` | idem | PASS |

### Rarezas a preservar (paridad)

| Caso | Comportamiento Oracle |
|------|------------------------|
| `f_get_edad_string` a 13 meses | `"1 años"` (plural incorrecto legacy) |
| Exactamente 12 meses | `"1 año"` |
| Días | Siempre `"dias"` (nunca singular) |
| `NULL` / VARCHAR2 vacío | JDBC `null` |
| `p_get_edad` + 29-feb | `ADD_MONTHS` ancla en **último día** del mes destino |
| Interleaved | Comparar **HEX/bytes** WE8, no String Unicode (`CHR(159)`) |
| Reloj | Funciones con `SYSDATE` → fixture guarda `capturedAtOracleSysdate`; tests congelan `Clock` |

SDDs:  
`docs/sdd/spike-golden-master-edad/`,  
`spike-golden-master-edad-string/`,  
`spike-golden-master-get-edad/`,  
`spike-golden-master-interleaved/`.

---

## 5. PERSONAS (frente BIRT, fuera de GENERAL)

Package: SPEC 205 / BODY **14721** líneas · **60** rutinas (`inventory_note.txt`).

Fuentes + args + casos sintéticos (null/-1, **sin PII**):

| Función | Artefactos |
|---------|------------|
| `f_get_persona_full` | `.sql` `.json` `_args.csv` |
| `f_get_matricula_full` | idem |
| `f_get_especialidad_full` | idem |
| `f_get_domicilio_persona` | idem |
| `f_get_personal_matricula` | idem |
| `f_get_persona_telefono` | idem |

Carpeta: `tools/golden-master/fixtures/personas/`.

`f_get_persona_full` = `apellido_razon_social || ', ' || nombre` (simple; alto ROI para PG/BIRT).

---

## 6. Códigos de barra / otros GENERAL (fuente, sin replay completo)

En `fixtures/general/`:

- `f_get_cod_barra.sql` — AFIP; null empresa → **ORA-20001** CUIT inválido
- `f_gen_caract_cod_barra_ean_13.sql`
- `f_cod_barra_atencion_amb.sql`, `_internacion.sql`, `_ord_serv_amb.sql`
- `f_identificar_cod_barra.sql`
- `f_prestacion.sql` + `.json` sintético
- `f_concat_val_pos_dom_hc.sql` + `.json` sintético

---

## 7. Identificadores (`f_next_id_tabla`)

SDD: `docs/sdd/spike-next-id-tabla/` · fixtures: `tools/golden-master/fixtures/next-id/`

| Ítem | Valor |
|------|------:|
| Secuencias `SEC_ID_*` | **110** |
| Tablas contador | **11** |
| Mapeos tabla→rama | **104** |
| `SEC_ID_TABLA` (genérico) | **348** filas |
| `SEC_ID_E_PAGO` | **Ausente** |

Motor real: **`pf_next_id_tabla`** (+ `_aut` honorarios). Wrapper `f_next_id_tabla` solo despacha.

### Snapshot seed (VPN 2026-08-14T11:25:08) — re-capturar en corte

| Archivo | Uso |
|---------|-----|
| `sec_id_sequence_seed.csv` | `last_number` → plan `setval` PG |
| `sec_id_counter_seed.csv` | `prox_id` limpio (sin operadores) |
| `sec_id_tabla_full.csv` | 348 filas rama genérica |
| `id_internacion_samples.csv` | IDs opacos recientes |
| `next_id_registry.csv` / `pg_sequence_catalog.csv` | Registro canónico + catálogo PG |
| `SEED_NOTES.md` | notas de captura |

**Implementación inicial (Hospital-Api):** `NextIdService` + Flyway `V10` (secuencias;
sin `setval`; sin rama `SEC_ID_TABLA`). Ver `docs/sdd/spike-next-id-tabla/`.

**Hallazgo diseño:** `SEC_ID_INTERNACION` **no es 1 fila** — es multi-clave
`(id_centro_ate, tipo_admision)` → ~19 filas. El ID compuesto es numérico de 10–11
dígitos (`lpad(centro,2)||tipo||lpad(seq,7)`); muestras recientes longitud 11.

Diseño destino (propuesta vigente): **todo → `SEQUENCE` PG** donde sea opaco;
internación requiere secuencia **por (centro, tipo)** o equivalente.

---

## 8. Acceso técnico (para reabrir VPN)

| Ítem | Valor |
|------|-------|
| Creds | `tools/relevamiento/oracle.env` (**gitignored**) |
| Driver | `HOSPITAL-BUSINESS/lib/ojdbc7.jar` |
| Python thin | **No** soporta 11.2 → usar JDBC |
| Capturas | `tools/golden-master/src/*.java` |

Replay típico:

```bash
cd /Volumes/External/Development/osw/grupogea/Hospital-Api
mvn -pl core -Dtest=EdadAnioCalculatorGoldenMasterTest,Interleaved25EncoderGoldenMasterTest,EdadStringCalculatorGoldenMasterTest,GetEdadCalculatorGoldenMasterTest test
```

---

## 9. Pendiente

| Prioridad | Trabajo | ¿VPN? |
|-----------|---------|-------|
| Alta | ~~Flyway PG secuencias + `NextIdService`~~ | **Hecho** (V10 + service); falta `SEC_ID_TABLA` + internación service + `setval` corte |
| Alta | Helpers BIRT PG (`persona_full` + edades) | **V11** + `PersonaFullFormatter`; ver `docs/sdd/birt-helpers-pg/` |
| — | Piloto Anunciador + G1 | **gate-done** (no abandonado) — `docs/sdd/estado-piloto-vs-general.md` |
| Media | Port fuentes BIRT fuera de GENERAL (ADMISION/HC/FACTURACION/…) | No — fuentes en `fixtures/{admision,historia_clinica,…}` |
| Media | Corregir `.rptdesign` fantasmas Retención* | No |
| Opcional | ~~EAN-13 + `CHAR_COD_BARRA`~~ | **Hecho VPN:** catálogo **0 filas** → sin GM positivo; ver `spike-golden-master-ean13` |
| Baja | `v$sql`/AWR en **producción** | Prod |
| Evitar | `nextval` en la copia por curiosidad | Mutante |

### Ya extraído en esta sesión VPN (batch 11:25 + AFIP 11:29)

- Seed secuencias + contadores + `SEC_ID_TABLA` full
- Muestras `ID_INTERNACION` + args next-id compuestos
- Ubicación + fuente top BIRT “huérfanos” de package (`birt_gap_locations.csv`)
- `empresa`: 19/19 con CUIT
- **AFIP `f_get_cod_barra`**: fixture + `AfipCodBarraEncoder` PASS
- **EAN-13**: fuentes + DDL; `CHAR_COD_BARRA` **vacía** (ORA-01403) — ver `docs/sdd/spike-golden-master-ean13/`
- Snapshot instancia: `oracle_snapshot_meta.csv` (`startup_time` aún 2026-06-05)

---

## 10. Relación con plan A

Confirma: captura en 11.2 → implementación en Quarkus/Postgres → UAT aislado → un corte.  
No abre puerta a Oracle 19 como destino por defecto (`analisis-oracle19-vs-postgres.md`).
