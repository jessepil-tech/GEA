# SDD — Piloto AGI G1 (recepción autogestión)

`phase_id:` **`sdd.hospital.piloto-agi-g1`**

Segundo vertical del piloto Fase 3: **autogestión recepción** (tótem), stack nuevo
(Quarkus + Angular + Postgres + Identity). No cablea el WAR AGI.

| Artefacto | Estado |
|-----------|--------|
| [spec.md](spec.md) | **reviewed** (Q1–Q3 cerrados) |
| [plan.md](plan.md) | **reviewed** |
| [tasks.md](tasks.md) | app-1…6 + **ops-4** hechos; **gate-done** |
| [verify-report.md](verify-report.md) | **PASS** post-implement (2026-08-13) |

Padre / CU #1: [`piloto-agi-anunciador/`](../../anunciador/piloto-agi-anunciador/) (**gate-done**).  
Inventario G1: [inventario-cu.md](../../anunciador/piloto-agi-anunciador/inventario-cu.md).  
Legacy: `AGI/.../BBIdentificacionPaciente`, `BBRecepcionarPaciente`.

## Slice v1 (hecho)

Identificar paciente seed → listar turnos seed → confirmar → ticket mock.  
Sin ValidadorWS, lectora, impresora real ni golden master (**G1-b**).

## Cómo probar

```bash
set -a && source /Volumes/External/Development/osw/grupogea/Hospital-Legacy/tools/postgres-host.env && set +a
# Identity :8080 + Hospital-Api :8081 (JDBC → localhost:5432)
bash /Volumes/External/Development/osw/grupogea/Hospital-Api/tools/smoke-piloto-agi-g1.sh

# UI → /agi/recepcion → DNI 30111222
```

Datos: DBs `hospital_identity` / `hospital_api` en Postgres del host (`postgresql_01`).

| Repo | Rol | Puerto |
|------|-----|--------|
| Hospital-Identity | JWT | 8080 |
| Hospital-Api | Feature `agi` | 8081 |
| Hospital-Web | `/agi/recepcion` | 4200 / 4210 |

## Siguiente (post gate-done)

- **G1-b** — ValidadorWS / golden master / hardware
- Feature **`catalogo`** (ABM) solo si el piloto lo exige
- Postgres compose fijo (opcional)

Display A1 API Key y limpieza store identidad en Api: **hechos**.

**Nota 2026-08-14:** el trabajo de sesión posterior fue GENERAL/VPN (oráculo + NextId + BIRT helpers).
Piloto G1 **sigue gate-done** — ver [`../estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).
