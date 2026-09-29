# Ajustes de prioridad — migración Hospital

`phase_id:` **`sdd.hospital.ajustes-prioridad-migracion`**  
Fecha: **2026-08-14** · **actualizado 2026-08-20**  
Estado: **canónico** (no sustituye al dossier; lo prioriza)

Documento de **una página**. La estrategia vive en:

- [`dossier-migracion.md`](../arquitectura/dossier-migracion.md)
- [`plan-migracion-packages-cqrs.md`](../arquitectura/plan-migracion-packages-cqrs.md)
- [`regla-waiver-paridad-legacy.md`](../canon/regla-waiver-paridad-legacy.md) — paridad funcional
- [`gobierno-migracion.md`](../canon/gobierno-migracion.md) — capas de decisión (paridad → relevamiento → SDD)
- [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md) — **DDL canónico = PG migrado** (prioridad 2026-08-20)
- [`relevamiento-modular-funcional.md`](../canon/relevamiento-modular-funcional.md) — capa 3 (config→maestros→operación)


**No** crear un `ESTRATEGIA_FUSIONADA.md` paralelo: este archivo es la columna de
ajustes; los canónicos siguen siendo la fuente de verdad.

---

## Columna vertebral (sin cambio)

1. Destino: Quarkus + Angular + PostgreSQL; Oracle 11.2 = oráculo + legacy.
2. Unidad de corte = **CU / punto de invocación**, no package completo.
3. Convivencia: **golden master + WAIVER gobernado + PG compat** (puente de firma).
4. Piloto = medidor de ritmo; **no** cronograma rígido de “21 días”.
5. GTT / dual-datasource / TMP_* = **fallback puntual de un CU**, nunca mecanismo
   transversal por defecto.

### Cierre del piloto seed (2026-08-17)

**Decisión de equipo:** el piloto AGI/ANUNCIADOR (G1…G1-d.birt + CU-A/B/B.1/C)
**demostró** el stack. **No ensanchar** más tablas/UI seed (`*_agi`, flujos solo
piloto): riesgo de **fork** paralelo al legacy.

De aquí en adelante el corte diario es **paridad con legacy** (tablas/SP/UI Oracle
→ PG + Quarkus), bajo [`regla-waiver-paridad-legacy.md`](../canon/regla-waiver-paridad-legacy.md).
El piloto queda como referencia de ritmo, no como producto a completar.

**DDL (2026-08-20):** trabajar siempre sobre el schema **`ts` del PostgreSQL
migrado**. Si falta un objeto que aún solo existe en Oracle → registrarlo en
[`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md); no inventar tablas
paralelas en `public`.

---

## Ajustes de prioridad (hacer / endurecer)

| # | Ajuste | Acción concreta | Cuándo |
|---|--------|-----------------|--------|
| **A1** | Retiro de **PG compat** | Toda función/vista compat nace con **CU de retiro** + **fecha límite** (o “retirar cuando consumidores residuales = 0”). Inventario vivo de firmas compat. | Al crear cada función |
| **A2** | Identity: validar IdP | Gate de negocio/IT: ¿producción exige IdP corporativo (AD/OIDC) o basta el emisor propio (JWT local + API Key)? Si basta → oleada A es destino. Si exige IdP → planificar puente **antes** de escalar más clientes. | **Ya** (no Fase 6) |
| **A3** | Consumidores directos de la base | Inventariar QlikView (~10.481 logins), Power Query, scripts Python y ODBC: qué leen, quién mantiene, reapunte PG vs API. | **Carril paralelo ya** (no “antes de Fase 6”) |
| **A4** | Inyección SQL (8 puntos) | Parametrizar en legacy (`SQL-NATIVO.md`); sin esperar migración. | **Ya** |
| **A5** | `v$sql` producción | Captura de **una semana** con `VerificarOracle` (usuario `ts` ya tiene lectura). | **Ya** (bloquea presupuesto) |
| **A6** | Borde lab (serie/ASTM) | Spike de **approach** (host con puerto / sidecar); no migrar LABORATORIO ahora. | **Fase 1** (definir), lab migra después |
| **A7** | Anti-default GTT/dual DS | Mantener solo como escape de un CU documentado. | Continuo |
| **A8** | Cronograma 21 días | Descartar como promesa; medir rutinas/semana, % PASS, WAIVER rate. | Continuo |

---

## Anti-patrones

| No hacer | Hacer |
|----------|--------|
| Crecer PG compat “para siempre” | Firmar retiro por función |
| Asumir que JWT local es “temporal” sin preguntar | Cerrar A2 con negocio/IT |
| Descubrir Qlik/Excel en el corte | Cerrar A3 en paralelo al piloto |
| Esperar Identity/Postgres para fijar inyección | A4 en legacy ahora |
| Dejar LABORATORIO sin approach de hardware | A6 en Fase 1 |
| Dual datasource / GTT como patrón general | Solo fallback CU |
| Prometer calendario cerrado | Calibrar con piloto |

---

## Dónde se refleja

| Tema | Documento |
|------|-----------|
| Riesgos / §11 relevamiento | [`dossier-migracion.md`](../arquitectura/dossier-migracion.md) §11 + enlace a este doc |
| PG compat + tipología | [`plan-migracion-packages-cqrs.md`](../arquitectura/plan-migracion-packages-cqrs.md) §2.5 |
| Identity sin OIdentity | [`migracion-identidad.md`](../arquitectura/migracion-identidad.md) §5 |
| Orden diario piloto | [`backlog-orden-2026-08-14.md`](backlog-orden-2026-08-14.md) |
| WAIVER vs paridad | [`regla-waiver-paridad-legacy.md`](../canon/regla-waiver-paridad-legacy.md) |
