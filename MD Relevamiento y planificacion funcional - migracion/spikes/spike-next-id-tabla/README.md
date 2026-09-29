# Spike — Diseño `f_next_id_tabla` (IDs)

`phase_id:` **`sdd.hospital.spike-next-id-tabla`**

Fecha: 2026-08-13/14  
Estado: **rama genérica + InternacionIdService hechos** (2026-08-14)

Índice: [`hallazgos-golden-master-vpn-2026-08-14.md`](../../arquitectura/hallazgos-golden-master-vpn-2026-08-14.md) §7 ·
plan capas: [`plan-migracion-packages-cqrs.md`](../../arquitectura/plan-migracion-packages-cqrs.md).

## Entregado

| Pieza | Ubicación |
|-------|-----------|
| Registro tabla→secuencia | `Hospital-Api/.../general/next_id_registry.csv` |
| `NextIdService` + registry | secuencias V10 + **fallback SEC_ID_TABLA** |
| `SecIdTablaPort` / JDBC | `UPDATE … RETURNING` sobre `sec_id_tabla` (348 filas) |
| `InternacionIdFormatter` | formato compuesto |
| `InternacionIdService` | centro×tipo + bebé vía `INTERNACION_BEBE` |
| Flyway | **V10** secuencias · **V13** `sec_id_tabla` + `sec_id_internacion` + refs |
| Tests | `NextIdServiceTest` + `NextIdServicesIT` **PASS** |

### Decisiones

1. Contadores dedicados `FOR UPDATE` → secuencia PG (V10).
2. Rama genérica 348 → **tabla `sec_id_tabla`** (no 348 sequences); corte = `UPDATE prox_id` desde CSV.
3. `AUD%` → `sec_id_aud`.
4. `INTERNACION` → `InternacionIdService` (no `nextId("INTERNACION")`).
5. `E_PAGO` → **DEAD**.
6. `CONVENIO9` → alias `CONVENIO` en SEC_ID_TABLA.
7. `setval` / prox reales VPN → **solo en corte** (local `prox_id = 1`).
8. Centro `654`: sin `nro_centro` en fixtures → falla explícita hasta seed de corte.

## Pendiente

1. Más CUs: declarar clave `nextId("TABLA")` en cada insert con PK opaca legacy.
2. Re-capturar + aplicar `prox_id` / `setval` en go-live.
3. Completar `centro_atencion_ref` (p. ej. 654) desde Oracle en corte.

## Consumo (plan CQRS §4.3)

Piloto G1: `JdbcAgiAdapter.confirmarRecepcion` → `nextId("COLA_ESPERA_RECEP")` →
`recepcion_agi.nro_espera`. **No** se porta `CustomIdGenerator`.


## Artefactos VPN

`tools/golden-master/fixtures/next-id/`
