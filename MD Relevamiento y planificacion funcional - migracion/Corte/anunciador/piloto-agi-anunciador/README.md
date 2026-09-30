# SDD — Piloto AGI + ANUNCIADOR

`phase_id:` **`sdd.hospital.piloto-agi-anunciador`**

Primer corte de **negocio** en el stack nuevo (Quarkus + Angular + Postgres + Identity).
No cablea el legacy AGI/ANUNCIADOR a Identity: **construye el piloto nuevo**.

| Artefacto | Estado |
|-----------|--------|
| [spec.md](spec.md) | **reviewed** (RF-6 Core/seed, CU #1) |
| [plan.md](plan.md) | **reviewed** |
| [tasks.md](tasks.md) | app-1…6 + **ops-3** hechos; `gate-done` CU #1 |
| [inventario-cu.md](inventario-cu.md) | **Hecho** — CU #1 = A1+A2 |
| [estrategia-core.md](estrategia-core.md) | Core/cross = features en Hospital-Api |
| [verify-report.md](verify-report.md) | **PASS** (2026-08-13) |

Inventario post-CU #1 (Node → Quarkus, 1:1 Oracle):  
[`relevamiento-node-anunciador/`](../../../relevamiento/relevamiento-node-anunciador/).  
**Apagar Node** (programa de corte): [`apagar-anunciador-node/`](../apagar-anunciador-node/).

Padre: [`dossier-migracion.md`](../../../arquitectura/dossier-migracion.md) §6.4 / Fase 3 ·
[`mapa-productos-destino.md`](../../../arquitectura/mapa-productos-destino.md) §6 ·
Identity oleada A: [`identidad-oleada-a/`](../../plataforma/identidad-oleada-a/).

## Repos (local)

| Repo | Rol | Puerto |
|------|-----|--------|
| `Hospital-Identity` | Emisor JWT / API Key | 8080 |
| **`Hospital-Api`** | Negocio (valida Bearer) | **8081** |
| `Hospital-Web` | Shell Angular (auth→Identity) | 4200 (o 4210 si ocupado) |

## Cómo probar el cimiento (ya)

```bash
# Identity :8080 + Hospital-Api :8081 en quarkus:dev
TOKEN=$(curl -s -X POST http://localhost:8080/api/v1/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"username":"admin","password":"Admin123!"}' | jq -r .accessToken)

curl -s http://localhost:8081/api/v1/piloto/ping -H "Authorization: Bearer $TOKEN"
curl -s http://localhost:8081/api/v1/anunciadores -H "Authorization: Bearer $TOKEN"
# id seed demo: 33333333-3333-3333-3333-333333333301
curl -s http://localhost:8081/api/v1/anunciadores/33333333-3333-3333-3333-333333333301/llamados \
  -H "Authorization: Bearer $TOKEN"
```

## Pantalla (solo lista de llamados)

| Vista | Ruta | Uso |
|-------|------|-----|
| **Display = A1 (sala/TV)** | `/display/anunciadores/{id}` | Full-bleed, sin menú, poll 5s — **sin login** (API Key) |
| Operación = A2 | `/anunciadores` (+ `.../llamados`) | Shell con sidebar + JWT |
| Legacy Vue | `AnunciadorSimple` / `AnunciadorCompuesto` | Producto actual en `ANUNCIADOR/anunciadorVue` |

Demo display (**sin** sign-in):  
`http://127.0.0.1:4210/display/anunciadores/33333333-3333-3333-3333-333333333301`

Auth TV: `hospitalDisplayApiKey` en `Hospital-Web/public/appsettings.json` → header `X-API-Key`  
(solo en `listLlamadosForDisplay`). Smoke: `Hospital-Api/tools/smoke-display-anunciador.sh`.

## Datos locales (Postgres del host)

**No** usamos Dev Services en `%dev`. Ambos apuntan al Postgres ya levantado
(`postgresql_01` → **localhost:5432**), con **dos databases**:

| App | Database | Contenido |
|-----|----------|-----------|
| Hospital-Identity | `hospital_identity` | users, roles, claims, sessions (IdP) |
| Hospital-Api | `hospital_api` | negocio (anunciador, agi); **sin** store de identidad (V9) |

Credenciales: `tools/postgres-host.env` (excluido de git; ver
`tools/postgres-host.env.example`). Exportar antes de `quarkus:dev`:

```bash
set -a && source tools/postgres-host.env && set +a
```

`%test` sigue con Dev Services (ITs aislados). Oracle 11.2 no entra aquí.

## Siguiente (post CU #1)

- **G1** recepción / autogestión — SDD [`../piloto-agi-g1/`](../../recepcion/piloto-agi-g1/) (**gate-done**)
- Feature **`catalogo`** (ABM) solo si G1 lo exige; no bloquea el slice G1

**Nota 2026-08-14:** tras gate-done, el foco de sesión pasó a GENERAL/BIRT/IDs (VPN).
El piloto **no se canceló** — ver [`../estado-piloto-vs-general.md`](../../../estado/estado-piloto-vs-general.md).

Smoke API: `Hospital-Api/tools/smoke-piloto-anunciador.sh`  
UI: Identity + Hospital-Api + `npm start` → `/anunciadores`.
