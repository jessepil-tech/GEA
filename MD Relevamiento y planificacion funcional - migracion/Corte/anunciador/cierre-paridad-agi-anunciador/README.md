# Cierre paridad — AGI (tótem) + Anunciador (sala)

`phase_id:` **`sdd.hospital.cierre-paridad-agi-anunciador`**  
Fecha: **2026-08-27**  
Estado: **active** — P0 núcleo TV **cobrado**; restan P1–P5

Consolida qué falta para declarar **paridad operativa** del perímetro tótem + TV
anunciador respecto del legacy (HOSPITAL_2 / AGI / Node Vue), **después** del
cutover a schema `ts` (2026-08-25).

No sustituye los SDD hijos: los **ordena** y fija criterios de “cerrado”.

---

## Ya cobrado (no reabrir)

| Capacidad | SDD / evidencia | Estado |
|-----------|-----------------|--------|
| DDL canónico `ts` (anunciador, cola, AGI, catálogo) | [`plan-cutover-api-schema-ts.md`](../../../planificacion/plan-cutover-api-schema-ts.md) V27–V33 | ✅ |
| Retiro piloto `public.*` dominio | V33 · [`retiro-tablas-piloto-public.md`](../../../arquitectura/retiro-tablas-piloto-public.md) | ✅ |
| Display TV + WS `nuevos-llamados` | [`piloto-agi-anunciador/`](../piloto-agi-anunciador/) · [`anunciador-ws/`](../anunciador-ws/) | ✅ gate-done |
| Ocupación / logos / diccionario (N1–N3) | [`apagar-anunciador-node/`](../apagar-anunciador-node/) | ✅ DEV |
| Vertical G1 recepción → ticket PDF | [`piloto-agi-g1/`](../../recepcion/piloto-agi-g1/) … [`piloto-agi-g1-d-birt/`](../../recepcion/piloto-agi-g1-d-birt/) | ✅ (UAT impresora aparte) |
| Llamar recepción → TV | [`cu-clinico-b1-llamar-recepcion/`](../../recepcion/cu-clinico-b1-llamar-recepcion/) | ✅ |
| Cola A + Cola B read/UI (M1–M4) | [`paridad-recepcion-cola/`](../../recepcion/paridad-recepcion-cola/) | ✅ |
| Ciclo `LLAMAR` núcleo TV (consume, ventana, 1h, quitar*, WS, smoke) | [`ciclo-vida-llamado-anunciador/`](../ciclo-vida-llamado-anunciador/) | ✅ **gate-done** 2026-08-26 |


Reglas madre: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md) ·
[`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md) ·
[`regla-waiver-paridad-legacy.md`](../../../canon/regla-waiver-paridad-legacy.md) ·
[`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md).

---

## Checklist de cierre (orden recomendado)

Marcar `[x]` solo con evidencia (IT / smoke / UAT / WAIVER de negocio).

### P0 — Ciclo de vida en TV (núcleo display)

- [x] **C2.1** — Puerto `quitarPorPacienteServicio` / `quitarPorColaEsperaRecep` + IT  
      → [`ciclo-vida-llamado-anunciador/`](../ciclo-vida-llamado-anunciador/) gate-done  
      Enganche anulación/atención (UI/CU que llama al puerto) → **diferido** [`cu-llamar-atencion-medica/`](../../recepcion/cu-llamar-atencion-medica/) (Fase 4)
- [x] **C3** — Smoke E2E núcleo TV: insertar → rojo → consume `S→N` → re-call / ventana / WS  
      → verify-report del mismo SDD (**PASS** 2026-08-26)

### P1 — Recepción / Cola (bloquea E2E mostrador permanente)

Canon: [`criterio-avance-e2e-datos.md`](../../../canon/criterio-avance-e2e-datos.md).

- [ ] **Bootstrap padres** Oracle→PG (paciente/centro/servicio si esta oleada no los ABMea)  
      → desbloquea FKs. **No** es prueba de ABM ni de T4/T5. Canon: [`criterio-avance-e2e-datos.md`](../../../canon/criterio-avance-e2e-datos.md).
- [ ] **Acciones menú Cola B** aún no portadas (legacy `colaEspera` / menú contextual)  
      → slug hijo bajo [`paridad-recepcion-cola/`](../../recepcion/paridad-recepcion-cola/) o SDD nuevo linkeado

### P2 — Tótem / ticket (bloquea “corte impresión AGI”)

- [ ] **R3.1 UAT impresora térmica** on-site (ticket legible en impresora del tótem)  
      → [`birt-runtime-destino.md`](../../../arquitectura/birt-runtime-destino.md) §4.1  
      → o **WAIVER** firmado por negocio si el sitio no tiene impresora aún

### P3 — Configuración, maestros y permisos

Misma exigencia que la capa 3
([`gobierno-migracion.md`](../../../canon/gobierno-migracion.md) /
[`relevamiento-modular-funcional.md`](../../../canon/relevamiento-modular-funcional.md)).
No declarar perímetro cerrado solo con operación + seed.

Evidencia inventariada: [`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/)
(`pipeline.md`, `maestros.md`, `inventario.md`, `cortes.md`).

- [x] Inventario: alta/edición anunciador, vínculo ambiente, terminal AG / opciones, diccionario  
      → documentado en maestros/inventario (**estado: diferido**, no implementado)
- [x] Permisos / perfiles que abren sala y config (vs Identity) — **parcial** documentado
- [x] Cada ítem: **done** / **diferido(slug)** / **WAIVE** — slug **`anunciador-agi-config-abm`**
- [x] Ampliar relevamiento Node con `pipeline.md` + `maestros.md` — **hecho 2026-08-26**
- [ ] Implementar [`anunciador-agi-config-abm/`](../anunciador-agi-config-abm/) (Clarify: fila 4 FIRME 2026-09-08; resto propuesto) **o** firmar WAIVE/diferido de producto antes de P5
      Hijo serv/triage: [`anunciador-config-avanzada/`](../anunciador-config-avanzada/) — P5 no cierra mientras esté abierto

### P4 — Integridad `ts` (no bloquea demo; sí bloquea joins avanzados)

- [ ] Revisar **FKs diferidas** (P-ORA-009) cuando un CU las necesite  
      → [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md)
- [ ] Sustituir / acotar `elegibilidad_seed` si UAT exige validador real (P-ORA-010 si aplica)

### P5 — Gate de producto “perímetro cerrado”

- [ ] Matriz de capacidades legacy vs nuevo **sin filas abiertas** (o con SDD hijo / WAIVER)  
      → usar plantilla de [`proceso-sdd-paridad-completa.md`](../../../canon/proceso-sdd-paridad-completa.md)
- [ ] Apagar Node en **UAT/sitio** (hoy solo DEV) si aún hay Vue en sala  
      → [`apagar-anunciador-node/`](../apagar-anunciador-node/)
- [ ] Verify-report de **este** slug en PASS (o lista explícita de diferidos linkeados)

---

## Criterio “paridad AGI+Anunciador = cerrada”

Se declara **cerrada** cuando:

1. **P0** núcleo TV **gate-done** (ciclo `LLAMAR` + smoke). Enganche anulación clínica = SDD hijo, no silencio.  
2. P1: o acciones Cola B implementadas, o SDD hijo + fecha/owner (no silencio).  
3. P2: UAT impresora PASS o WAIVER de negocio.  
4. P4: matriz anti-gap sin huecos sin traza.  
5. Runtime 100% sobre `ts.*` para dominio (ya cumplido post-V33).

**Fuera de este cierre** (otros frentes): R5 reportes clínicos, GENERAL completo,
packages enteros, big-bang datos prod.

---

## Flujo E2E de referencia (demo / UAT)

```text
Tótem AGI → recepción/cola → Llamar → TV anunciador (WS)
                ↓
         ticket PDF (Reports) → [UAT impresora]
```

Guía operativa adicional: [`PRUEBAS.md`](../../../arquitectura/PRUEBAS.md) (si aplica en el entorno).

IDs demo típicos (seed): ver [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).

---

## Enlaces rápidos

| Tema | Doc |
|------|-----|
| Orden diario | [`backlog-orden-2026-08-14.md`](../../../planificacion/backlog-orden-2026-08-14.md) |
| Snapshot | [`estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md) |
| Relevamiento Node | [`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/) |
| Cutover `ts` | [`plan-cutover-api-schema-ts.md`](../../../planificacion/plan-cutover-api-schema-ts.md) |
