# Gaps packages / TMP — HistoriaClinica (F2)

Inventario de dependencias Oracle embebidas en `HistoriaClinica.rptdesign` vs PostgreSQL.
**Actualizado 2026-08-26:** ports en `Hospital-Reports/sql/oracle-to-pg/` (ver `CHECKLIST-CALLABLES.md`).

| Dependencia | Tipo | Usado en dataset | Estado PG | Notas |
|-------------|------|------------------|-----------|-------|
| `ts.personas.f_get_persona_full` | function | PACIENTE, HISTORIA_CLINICA | **Resuelto** | Reports `02_personas_helpers.sql` (+ V11 API) |
| `ts.general.f_get_edad_string` | function | PACIENTE | **Resuelto** | Reports `03_general_edad.sql` (+ V11) |
| `ts.historia_clinica.p_get_balance_hidrico_pac_ref` | SP → fn | BALANCE_HIDRICO | **Resuelto (Reports)** | `05_balance_hidrico.sql` (pivote ATENCION) |
| `ts.historia_clinica.p_get_valor_det_form_col` | function | ANTECEDENTES* | **Resuelto (Reports)** | `04_historia_clinica_form.sql` |
| `ts.historia_clinica.f_get_valor_det_form_defecto` | function | ANTECEDENTES* | **Resuelto (Reports)** | idem |
| `ts.personas.f_get_personal_matricula` (+ matricula/especialidad/rol) | function | HC / Epicrisis / generate | **Resuelto (Reports)** | `02_personas_helpers.sql` |
| `ts.tmp_rpt_historia_clinica` | table | PROBLEMAS_*/TMP_RPT_* | **Resuelto (Reports)** | `06_tmp_tables.sql` |
| `Pacientes.generateEventosHC` / `f_generate_hc_diaria_pac` | off-report | carga TMP | **Resuelto (Reports)** | FULL `07_f_generate_hc_diaria_pac.sql`; caller pasa `usuario` |
| schema `historia_clinica` / `enfermeria` (PHP) | package | BALANCE / forms / generate | **Resuelto (Reports)** | `enfermeria.f_get_php_internacion` reconstruido (body Oracle ENFERMERIA no dumpado) |
| `NVL` / `ROWNUM` | dialect | HISTORIA_CLINICA, ANTECEDENTES* | **Convertido** | `COALESCE` / `ROW_NUMBER()` |
| ODA `odaDriverClass` | datasource | HOSPITAL | **Convertido** | `org.postgresql.Driver` |
| Schema `ts.*` tablas dominio | DDL | casi todos | **Canónico `ts.<tabla>`** | No `search_path` como qualify. Callables: `personas.f_*` (no `ts.personas.f_*`). Paths `02_*.sql` abajo = histórico; hoy `sql/packages-pg/` |

## Criterio

Callables de render HC/Epicrisis + pipeline generate: **cerrados** en Reports (`CHECKLIST-CALLABLES.md`).
Pendiente operativo: promover `sql/oracle-to-pg/pg/` a Flyway del PG canónico y cablear API → `f_generate_hc_diaria_pac` antes del PDF.
