# Retiro de tablas piloto (`public.*` / `*_agi`) — plan B

`phase_id:` **`sdd.hospital.retiro-tablas-piloto-public`**  
Fecha: **2026-08-21**  
Estado: **vivo** (inventario + criterios; **sin drops de código** en esta entrega)

Regla madre: [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md)  
Cutover Api: [`plan-cutover-api-schema-ts.md`](../planificacion/plan-cutover-api-schema-ts.md)  
Canónico DDL: PostgreSQL migrado schema **`ts`** (paridad Oracle).

Este anexo cumple el **nivel B** de documentación: cada shape piloto en
`hospital_api.public` (y seeds `*_agi`) queda clasificado con destino de retiro.
No sustituye el inventario Oracle↔PG (`inventario-ddl-oracle-pg/`).

---

## Principio de retiro

| Clase | Tratamiento |
|-------|-------------|
| **Dominio espejo** (simplificado / UUID / columnas reducidas) | Cutover app → `ts.*` equivalente; dejar de escribir; drop cuando no haya lectores |
| **Seed `*_agi`** | No es destino final; no ensanchar; retirar al cerrar medidor o al reemplazar por `ts` |
| **Paridad parcial** (mismo nombre, shape incompleto en `public`) | Alinear JDBC a `ts.<mismo_nombre>`; retirar copia `public` |
| **Técnica app / starter** | **Conservar** (no es modelo Oracle) |
| **Identity** (si compartiera BD) | Fuera de este anexo; vive en Identity |

**Prohibido:** crear nuevas tablas `public` de dominio legacy o nuevos `*_agi` “porque el piloto funciona”.

---

## Inventario (`hospital_api` / Flyway Api — 2026-08-21)

Fuente: tablas `public` en entorno local Api + migraciones `V1`…`V26`.

### 1. Dominio espejo / anunciador (prioridad cutover)

| Tabla `public` | Origen Flyway | Equivalente `ts` | Estado | Plan de retiro |
|----------------|---------------|------------------|--------|----------------|
| `llamado_paciente` | V7 | `ts.llamado_anunciador` | **En uso runtime** (display / llamar) | Fase 2–3 cutover JDBC → `ts`; C1 plan-cutover; drop tras smoke TV |
| `anunciador` | V7 (+ V22–V26 assets) | `ts.anunciador` | En uso (UUID vs NUMERIC) | Mapear IDs; assets: P-ORA-006 (`BLOB`→`TEXT` en `ts`) |
| `sector` | V7 | `ts.sector_amb_centro_ate` (u homólogo catálogo) | Seed piloto | No ensanchar; catálogo real desde `ts` |
| `ambiente` | V7 | `ts.ambiente_amb` | Seed piloto | Idem |
| `anunciador_ambiente` | V21 | `ts.anunciador_ambiente_amb` | En uso ocupación | Cutover con N1; drop `public` |
| `ocupacion_ambiente_actual` | V21 | (modelo en `ts` / derivado) | En uso N1 | Verificar objeto canónico en `ts`; si solo piloto, recrear lectura sobre `ts` |
| `diccionario_anunciador` | V23 | `ts.diccionario_anunciador` | En uso N3 | JDBC → `ts`; drop `public` |

### 2. Paridad recepción / cola (mismo nombre lógico, shape `public`)

| Tabla `public` | Origen | Equivalente `ts` | Estado | Plan de retiro |
|----------------|--------|------------------|--------|----------------|
| `centro_ate` | V18 | `ts.centro_atencion` | En uso M1+ | Apuntar a `ts`; no mantener duplicado |
| `recepcion` | V18 | `ts.recepcion` | En uso | Idem |
| `puesto_recepcion` | V18 | `ts.puesto_recepcion` | En uso | Idem |
| `cola_espera_recep` | V18 | `ts.cola_espera_recep` | En uso M1+ | Idem; sync datos = UAT (no redefinir DDL) |
| `cola_espera_serv_amb` | V20 | `ts.cola_espera_serv_amb` | En uso Cola B | Idem |

### 3. Seeds piloto `*_agi` / AGI (no destino)

| Tabla `public` | Origen | Equivalente `ts` | Estado | Plan de retiro |
|----------------|--------|------------------|--------|----------------|
| `terminal_ag` | V8 | `ts.terminal_ag` (si aplica) | Seed G1 | No ensanchar; leer `ts` cuando CU real |
| `paciente_agi` | V8 | `ts.paciente` / persona | Seed | Retirar al dejar seed-only; **no** modelo clínico |
| `turno_agi` | V8 | `ts.turno` | Seed | Idem |
| `recepcion_agi` | V8 | — (agregado piloto) | Seed | Drop con cierre medidor; paridad usa `recepcion`/`cola_*` |
| `convenio_agi` | V12 | `ts` convenios reales | Seed G1-b | Retirar; catálogo CU-A hacia `ts` |
| `elegibilidad_seed` | V12 | — | Seed | Drop cuando elegibilidad lea reglas/`ts` |
| `servicio_agi` | V17 | `ts.servicio` | Seed CU-C | No ensanchar; cutover a `ts.servicio` |

### 4. Helpers IDs / refs (puente GENERAL)

| Tabla `public` | Origen | Equivalente `ts` | Estado | Plan de retiro |
|----------------|--------|------------------|--------|----------------|
| `sec_id_tabla` | V13 | `ts.sec_id_tabla` (+ seq PG) | En uso NextId | Preferir `ts` / sequences migradas (117 en PG) |
| `sec_id_internacion` | V13 | homólogo `ts` | En uso | Idem |
| `centro_atencion_ref` | V13 | subset de `ts.centro_atencion` | Ref piloto | Absorber en `ts` |
| `tipo_admision_ref` | V13 | `ts` tipos admisión | Ref piloto | Idem |

### 5. Técnicas app / starter — **conservar**

| Tabla `public` | Origen | Notas |
|----------------|--------|-------|
| `flyway_schema_history` | Flyway | Metadatos migración |
| `audit_logs` | V1 | Auditoría app (≠ `AUD_*` Oracle) |
| `file_metadata` | V3 | Storage app |
| `table_a` | V1 | Scaffold starter; candidata a drop limpio si no se usa |
| `users` / `sessions` / `roles` / `user_roles` / `user_claims` / `role_claims` | V1–V5 | Si aún existen en Api: deuda Identity — **no** son `ts`; migrar/retirar según oleada Identity, no por regla DDL Oracle |

`personas.persona` (V11 helpers BIRT): schema `personas`, no `ts` — compat BIRT mínimo; alinear a `ts.persona` / helper PG documentado; no ensanchar.

---

## Criterios de cierre por fila

Una fila del inventario se marca **retirada** cuando:

1. Ningún adapter JDBC / SQL del Api escribe ni lee la tabla `public`.
2. Smoke del CU (TV, cola, ticket) pasa contra `ts`.
3. Migración Flyway de drop (o baseline nuevo) aplicada en entornos que ya no necesitan el puente.
4. SDD del slice actualizado (sin presentar `public` como canónico).

---

## Orden sugerido de retiro (alineado al cutover)

1. **Anunciador display:** `llamado_paciente` → `ts.llamado_anunciador` (+ `anunciador`, diccionario, ocupación).
2. **Colas recepción:** `cola_espera_*` / `recepcion` / `puesto_*` → `ts` homónimos.
3. **Seeds `*_agi`:** dejar de usar en runtime; drop cuando no haya tests seed-only.
4. **sec_id_***: unificar con sequences/`ts` del PG migrado.
5. **Limpieza starter** (`table_a`, etc.) cuando no confunda.

Detalle de fases de implementación: [`plan-cutover-api-schema-ts.md`](../planificacion/plan-cutover-api-schema-ts.md).

---

## Relación con pendientes solo-Oracle

Retirar `public` **no** crea views/triggers/packages en PG. Gaps no-tabla siguen en
[`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md) (P-ORA-001…).
