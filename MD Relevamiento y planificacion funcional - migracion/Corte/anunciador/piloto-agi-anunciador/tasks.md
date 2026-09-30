# Tasks — Piloto AGI + ANUNCIADOR

## `sdd.hospital.piloto-agi-anunciador.phase-1` — Cimiento API

1. [x] **TSK-app-1** Bootstrap local `Hospital-Api` desde Identity/starter sano
   (`com.grupogea.hospital`), git `dev/dev`, exclusiones locales de tooling en `.git/info/exclude`.
2. [x] **TSK-app-2** Config resource server: `iss=hospital-identity`,
   `aud=hospital-clients`, claves alineadas, puerto **8081**, CORS `:4200`.
3. [x] **TSK-app-3** `GET /api/v1/piloto/ping` `@Authenticated` + `PilotoPingIT`.
4. [x] **TSK-ops-1** SDD mínimo (spec/plan/tasks/README) en Legacy
   `docs/sdd/piloto-agi-anunciador/`.

## `sdd.hospital.piloto-agi-anunciador.phase-2` — Primer vertical

5. [x] **TSK-ops-2** Inventario CU mínimos AGI + ANUNCIADOR (lista priorizada, 1 página).
   - **Hecho 2026-08-13:** [inventario-cu.md](inventario-cu.md) — **CU #1 = A1+A2
     Anunciador de llamados**; G1 recepción = segundo vertical.
6. [x] **TSK-app-4** Implementar el CU #1 acordado en `Hospital-Api` (Postgres).
   - **Hecho 2026-08-13:** Flyway V7 seed sector/ambiente/anunciador/llamados;
     `GET /api/v1/anunciadores` + `.../{id}/llamados`; `AnunciadoresResourceIT` PASS.
     Catálogo = mismas tablas, ABM diferido ([estrategia-core.md](estrategia-core.md)).
7. [x] **TSK-app-5** Front satélite o ruta en `Hospital-Web` contra Identity + Api.
   - **Hecho 2026-08-13:** `hospitalApiUrl` (:8081); rutas `/anunciadores` y
     `/anunciadores/:id/llamados`; menú + build OK.
8. [x] **TSK-app-6** Smoke E2E documentado (login Identity → llamada negocio).
   - **Hecho 2026-08-13:** `Hospital-Api/tools/smoke-piloto-anunciador.sh` + smoke UI
     manual (sign-in → Anunciadores). Requiere Identity:8080 + Api:8081.
9. [x] **TSK-ops-3** verify-report PASS / WAIVED.
   - **Hecho 2026-08-13:** [verify-report.md](verify-report.md) **PASS** + `gate-done`
     (smoke Identity→Api PASS; UI anunciadores OK; CORS :4210 documentado).
10. [x] **TSK-app-7** Display A1 sin login (API Key).
   - **Hecho 2026-08-13:** ruta anónima `/display/anunciadores/:id`;
     `hospitalDisplayApiKey` → `X-API-Key`; smoke `tools/smoke-display-anunciador.sh`.

## Orden

```text
TSK-app-1 → app-2 → app-3 → ops-1 → ops-2 → app-4 → app-5 → app-6 → ops-3
```

## Mapa RF → TSK

| RF / INV | Tarea |
|----------|--------|
| RF-1 RF-2 RF-3 NFR-2 | TSK-app-1…3 |
| RF-4 INV-1 INV-2 RF-6 | TSK-ops-2, TSK-app-4, estrategia-core |
| RF-5 | TSK-app-5, TSK-app-6 |
| CA verify | TSK-ops-3 |
