# Trabajo en paralelo — guía de equipo

Fecha: 2026-08-14  
`phase_id:` **`sdd.hospital.trabajo-paralelo-equipo`**

## ¿Alcanza pasar solo la carpeta `docs/`?

**No como única entrega.** `docs/` orienta (dossier, backlog, SDD, contratos), pero
para **implementar** hace falta:

| Necesario | Dónde |
|-----------|--------|
| Código destino | Repos hermanos: `Hospital-Api`, `Hospital-Web`, `Hospital-Identity`, `Hospital-Reports` |
| Legacy de referencia | Monorepo `Hospital-Legacy` (pantallas, PL/SQL, `.rptdesign`) |
| Arranque / secretos | `tools/postgres-host.env` (**no** versionar; compartir por canal seguro) |
| Contratos vivos | Identity JWT, smokes `Hospital-Api/tools/smoke-*.sh` |
| Estado de slices | [`estado-piloto-vs-general.md`](../estado/estado-piloto-vs-general.md) + SDD por carpeta |

Pasar solo `docs/` sirve para **leer y proponer**; no para **cerrar un gate**.

## Qué compartir (mínimo útil)

1. Acceso git a `Hospital-Legacy` + los 4 repos destino (misma org / paths locales).
2. Puntero de lectura (en este orden):
   - [`gobierno-migracion.md`](../canon/gobierno-migracion.md) (capas; **antes** de proponer migrar un módulo)
   - [`backlog-orden-2026-08-14.md`](backlog-orden-2026-08-14.md)
   - [`estado-piloto-vs-general.md`](../estado/estado-piloto-vs-general.md)
   - [`dossier-migracion.md`](../arquitectura/dossier-migracion.md) §6–7
   - [`mapa-productos-destino.md`](../arquitectura/mapa-productos-destino.md)
   - [`plan-migracion-packages-cqrs.md`](../arquitectura/plan-migracion-packages-cqrs.md)3. Un **slice SDD con owner** (`docs/sdd/<slug>/` = spec + plan + tasks + verify).
4. Credenciales PG / usuarios smoke por canal seguro (no en el zip de docs).

## Regla: WAIVE vs paridad legacy

**Antes de marcar un RF como WAIVE**, contrastar el legacy (pantalla/bean/SP):

| Hallazgo | Qué hacer |
|----------|-----------|
| Legacy **no** tiene la función | WAIVE OK (documentar evidencia) |
| Legacy **sí** la tiene | **No WAIVE** — implementar en el slice **o** crear slug SDD de etapa posterior (ej. `…-b1-…`) y linkearlo desde verify |
| Solo hay enlace/navegación | No cuenta como paridad del acto de negocio |

Ejemplo: CU-B RF-4 → legacy `btnLlamar` en recepción HOSPITAL_2 → diferido a B.1, no waived.

Hoy hay **muchas** carpetas bajo `docs/sdd/`. Casi todas son **histórico
gate-done** (no necesitan owner activo).

| Tipo | Ejemplos | Owner |
|------|----------|--------|
| **Cerrado** | `piloto-agi-g1*`, `piloto-agi-anunciador`, `identidad-oleada-a` | Nadie (solo lectura) |
| **Spikes hechos** | `spike-golden-master-*`, `spike-next-id-tabla` | Nadie |
| **Activo / a crear** | p.ej. `catalogo-abm-convenio`, UAT R3.1 | **1 owner** mientras el slice esté abierto |
| **Índices** | `backlog-orden-*.md`, `estado-piloto-*.md`, esta guía | Quien coordina (rotar OK) |

**“Un owner por carpeta”** = un owner por **slice en curso**, no “4 personas =
4 carpetas viejas”. Cuando el verify es PASS, el owner se libera.

### Reparto sugerido (vos + 3)

| Dev | Carril | Carpeta SDD |
|-----|--------|-------------|
| **1** | CU clínico o catálogo ABM (código Api+Web) | Nueva, p.ej. `catalogo-abm-…` |
| **2** | Otro CU **distinto** o Cross/catálogo #2 (acordar Flyway) | Otra carpeta nueva |
| **3** | UAT impresora R3.1 / docs / GM helpers | Puede usar nota en `birt-runtime-destino` §4.1 o `sdd/r31-uat-impresora/` |
| **Vos** | Coordinación + desbloqueos / 3.er CU si hace falta | Índices + review |

Si solo hay **un** CU elegido, no inventen 3 SDD de implementación en Api a la
vez: 1 implementa, 1 hace UAT/docs, 1 prepara el **siguiente** SDD (spec/plan)
sin codear encima del primero.

## Recomendación práctica

- **Sí en paralelo:** A (CU clínico) + C (UAT impresora on-site) + D (docs/GM).
- **Con cuidado:** A + B en el mismo sprint (ambos tocan `Hospital-Api`) →
  branches distintas y contrato de tablas acordado en el SDD antes de codear.
- **No:** dos personas “avanzando el piloto” sin slug SDD ni verify — se pisan y
  el dossier miente.

## Onboarding de un compañero (15 min)

```text
1. Clonar Hospital-Legacy + Hospital-{Identity,Api,Web,Reports}
2. Leer backlog-orden + estado-piloto-vs-general
3. Antes de proponer migrar un módulo: gobierno-migracion.md (capa 3)
4. Tomar un slug SDD vacío o asignado (owner = él)
5. Levantar stack (Identity 8080, Api 8081, Reports 8082, Web 4210)
6. Correr smoke del slice padre antes de cambiar código
```

Refs: [`propuesta-siguiente-cu.md`](propuesta-siguiente-cu.md).
