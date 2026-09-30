# Plan — Piloto AGI + ANUNCIADOR

Estado: **reviewed** (2026-08-13).

## Enfoque

1. Bootstrap `Hospital-Api` como resource server de Identity.
2. Smoke `/piloto/ping` + IT.
3. Inventario CU → **CU #1 = Anunciador de llamados (A1+A2)**; G1 recepción = 2º vertical.
4. Implementar lectura anunciador en `application` + OpenAPI; catálogo en mismas tablas con **seed**.
5. Front satélite o ruta en `Hospital-Web`.
6. Golden master solo si un CU posterior toca PL/SQL (G1); CU #1 no lo exige.

## Decisiones

| Tema | Decisión | Evidencia |
|------|----------|-----------|
| Nombre repo local | `Hospital-Api` (evita choque con legacy `Hospital/`) | bootstrap |
| Emisor | Solo `Hospital-Identity` | F2 / oleada A |
| Runtime datos | PostgreSQL | dossier §6 |
| Remoto ADO | Diferido (local) | preferencia 2026-08-13 |
| **Primer vertical** | **A1+A2 anunciador llamados** (no G1 recepción) | [inventario-cu.md](inventario-cu.md) |
| **Core / cross** | Features compartidas **en** `Hospital-Api` (p. ej. `catalogo`); **no** microservicio Core; piloto = seed + GET | [estrategia-core.md](estrategia-core.md) |
| Llamados | Read-model tabla `llamado_paciente` (en legacy: PL/SQL `f_get_paciente_anunciar`) | V7 Flyway |
| Tiempo real | WAIVE socket.io en v1 | inventario |

## Módulos previstos en Hospital-Api

```text
features/anunciador   ← CU #1 (lectura) — hecho en app-4
features/catalogo     ← ABM sector/ambiente/… (posterior; mismos IDs)
features/agi          ← G1 recepción (2º vertical)
```

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Alcance se infla a ABM de todo el catálogo | RF-6 + estrategia-core: seed ahora, ABM después |
| “Core” malentendido como otro deploy | INV-1; mapa F2 (una solución Hospital) |
| Duplicar login en Hospital-Api | RF-3; retirar `/auth/login` de producto después |
| Paridad PL/SQL en CU #1 | No aplica; diferir a G1 + golden master |
| Seed ≠ datos reales | Aceptable para medir stack; sync Oracle en fase datos |

## Estado de implementación (2026-08-13)

| Paso | Estado |
|------|--------|
| Bootstrap + JWT + ping | Hecho (app-1…3) |
| Inventario + Core strategy | Hecho (ops-2 + estrategia-core) |
| API anunciadores/llamados | Hecho (app-4) |
| Front + smoke E2E | **Hecho** (app-5, app-6) |
| verify-report | **PASS** (ops-3, 2026-08-13) |
