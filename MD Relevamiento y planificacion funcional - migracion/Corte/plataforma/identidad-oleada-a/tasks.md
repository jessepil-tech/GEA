---
title: SDD — Tasks Oleada A identidad
description: |
  Checklist ordenada para implementar sdd.hospital.identidad-oleada-a.
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-12
plan: ./plan.md
phase_id: sdd.hospital.identidad-oleada-a
---

# Tasks — `sdd.hospital.identidad-oleada-a`

## Convenciones

- **`phase_id`:** `sdd.hospital.identidad-oleada-a`
- **IDs globales:** `sdd.hospital.identidad-oleada-a::TSK-app-1`
- Destinos: `ops` (decisiones/docs), `app` (Quarkus/Angular)
- Marcar `[x]` al cerrar. No implementar código de app en masa sin
  [verify-report.md](verify-report.md) **PASS** o **WAIVED**.
- Verificación por tarea de código: línea **Verificación:**

---

## `sdd.hospital.identidad-oleada-a.phase-1` — Cierre de alcance · destino `ops`

1. [x] **TSK-ops-1** Cerrar duda de repo destino.
   - **F2 acordado 2026-08-13:** repo/deploy **`Hospital-Identity`** (plataforma) +
     solución **Hospital** (negocio, un core). Ver plan § Repo destino y
     [`analisis-identity-separado.md`](../../../arquitectura/analisis-identity-separado.md).
   - Verificación: decisión fechada; INV-3 (un emisor) cumplido por Identity.

2. [x] **TSK-ops-2** Revisar [spec.md](spec.md) + [plan.md](plan.md) (gate-spec /
   gate-plan). Resolver o aplazar explícitamente las dudas de `subjectType` /
   `legacy.idPersonal` y claims `id`/`sub`.
   - **Hecho 2026-08-13:** `sub`=login + `id`=UUID (starter tal cual); `subjectType`
     obligatorio en `/me` (seed `PERSONAL`); `legacy.idPersonal` nullable (seed `null`).
     Ver plan § Decisiones TSK-ops-2.
   - Verificación: dudas abiertas actualizadas (cerradas o “aplazado a oleada B”).

3. [x] **TSK-ops-3** Emitir [verify-report.md](verify-report.md) **PASS** o **WAIVED**
   (gate-verify) antes de TSK-app-3+.
   - **Hecho 2026-08-13:** veredicto **PASS** en verify-report.md.
   - Verificación: archivo presente con veredicto y fecha.

---

## `sdd.hospital.identidad-oleada-a.phase-2` — Bootstrap Quarkus · destino `app`

4. [x] **TSK-app-1** Bootstrap del repo **`Hospital-Identity`** desde el starter Quarkus
   (perfil `local-jwt`, API Key on, OIDC off). Primer entregable de plataforma (oleada A).
   - **Hecho 2026-08-13:** copia en
     `/Volumes/External/Development/osw/grupogea/Hospital-Identity` —
     `groupId` `com.grupogea.hospital.identity`, JWT+API Key on, OIDC off;
     `mvn verify` OK; `/q/health` OK en `quarkus:dev`.
   - **Remoto ADO:** `https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-Identity`
     — rama principal **`dev/dev`**. Sin co-autor Cursor; sin carpetas de tooling de agente en el remoto.
   - Verificación: `mvn -q test` / `quarkus:dev`; `/q/health` OK.
   - RF/NFR: NFR-1

5. [x] **TSK-app-2** Spike de claims: confirmar en el JWT real que `sub`=login e
   `id`=UUID. Anexar ejemplo al README de `Hospital-Identity`.
   - **Hecho 2026-08-13:** login `admin` → `sub=admin`,
     `id=f6bde12b-bac8-4b3a-9ec0-99ac09e93c3e`; documentado en README Identity.
   - Verificación: nota + JWT decodificado.
   - RF: RF-10

6. [x] **TSK-app-3** Configurar auth del hospital: `identity-mode=local`, JWT on, API Key
   on, `quarkus.oidc.enabled=false`. Quitar o no registrar rutas OIDC en el artefacto
   publicado (RF-5, RF-8, INV-1).
   - **Hecho 2026-08-13:** defaults en `application.properties`; `/auth/mode` → local +
     jwt/apiKey true; `/auth/oidc/*` → **404** en modo local; códigos auth → HTTP 401
     (ajuste Identity vs 409 del starter).
   - Verificación: `GET /api/v1/auth/mode` → `local` + jwt true; `GET/POST` oidc → 404 o
     deshabilitado documentado.
   - RF: RF-5, RF-8

7. [x] **TSK-app-4** Seed Flyway: usuario humano piloto (BCrypt) + rol mínimo; documentar
   usuario/password solo para dev. API Key de prueba en properties/env local (NFR-3, RF-9).
   - **Hecho 2026-08-13:** seed `admin`/`Admin123!` + `admin_role`; `V6` añade
     `subject_type=PERSONAL` y `legacy_id_personal=null`; API Key `demo-api-key`
     (placeholder); documentado en README Identity.
   - Verificación: login del seed funciona en test/IT; secreto de API Key no está en git
     (o es placeholder obvio de demo como el starter).

---

## `sdd.hospital.identidad-oleada-a.phase-3` — Contrato sesión · destino `app`

8. [x] **TSK-app-5** Asegurar `POST /api/v1/auth/login|refresh|logout` operativos en modo
   local (reuso starter; ajustar solo si el bootstrap los rompió).
   - **Hecho 2026-08-13:** `AuthResourceIT` (login OK/401, refresh, logout invalida) verde.
   - Verificación: IT o RestAssured — login OK, login bad → 401/400, refresh OK, logout
     invalida refresh.
   - RF: RF-1, RF-2, RF-3 · NFR: NFR-4

9. [x] **TSK-app-6** Implementar `GET /api/v1/auth/me` (Bearer): id, login, subjectType,
   roles, permissions (lista vacía permitida), `legacy.idPersonal` nullable según lo
   cerrado en TSK-ops-2.
   - **Hecho 2026-08-13:** `GetMeQuery` + endpoint; IT seed → PERSONAL / idPersonal null;
     sin Bearer → 401.
   - Verificación: IT con token de login seed; sin token → 401.
   - RF: RF-4

10. [x] **TSK-app-7** Endpoint smoke `@RolesAllowed("ApiKeyUser")` + configuración API Key
    (RF-6, RF-7).
    - **Hecho 2026-08-13:** `GET /api/v1/identity/ping` + `IdentityPingIT` (válida 200,
      inválida/ausente 401); también `test/testb` del starter.
    - Verificación: IT clave válida → 200; inválida → 401; sin header Bearer válido no
      sustituye si la clave es mala.

11. [x] **TSK-app-8** OpenAPI: documentar `/auth/*` de sesión y `/auth/me` (NFR-5).
    - **Hecho 2026-08-13:** anotaciones `@Operation` / `@SecurityScheme` en `AuthResource`;
      `OpenApiAuthIT` comprueba paths en `/q/openapi`.
    - Verificación: `/q/openapi` incluye los paths; revisión visual rápida.

---

## `sdd.hospital.identidad-oleada-a.phase-4` — Cliente piloto · destino `app`

12. [x] **TSK-app-9** App Angular desde starter con `authMode=local` apuntando al base URL
    de la API de identidad. Sin flujos OIDC en UI.
    - **Hecho 2026-08-13:** repo local `Hospital-Web` (`authMode=local`,
      `apiUrl=http://localhost:8080/api`, login mapea a `username`, CORS Identity
      para `:4200`). Build OK. Smoke manual: sign-in `admin`/`Admin123!` → dashboard.
    - Verificación: smoke manual o Playwright — sign-in guarda token y entra a ruta
      protegida.
    - CA: 6

13. [x] **TSK-app-10** Script `curl` o HTTP file (Bruno/IntelliJ) con la secuencia login →
    me → refresh → logout → API Key ping, versionado bajo `docs/sdd/identidad-oleada-a/`
    o `tools/` del repo destino.
    - **Hecho 2026-08-13:** `Hospital-Identity/tools/smoke-auth-oleada-a.sh` — PASS contra
      `localhost:8080` (mode → login → me → refresh → logout → refresh-401 → ping → ping-401).
    - Verificación: script documentado en README del SDD; corre contra API local.

---

## `sdd.hospital.identidad-oleada-a.phase-5` — Calidad y cierre · destino `app` / `ops`

14. [x] **TSK-app-11** Suite IT completa de NFR-4 / CA 1–5 en CI local del repo destino.
    - **Hecho 2026-08-13:** Surefire ahora incluye `*IT.java`; excluye `@Tag(e2e|oidc)`.
      Compuerta: `Hospital-Identity/tools/verify-oleada-a.sh` (PASS). CA 1–5 →
      `AuthResourceIT` + `IdentityPingIT` (+ OpenAPI).
    - Verificación: tests verdes en un comando documentado.

15. [x] **TSK-ops-4** Actualizar [`docs/contrato-api-identidad.md`](../../../arquitectura/contrato-api-identidad.md)
    marcando oleada A como “en implementación / hecha” según estado, y enlace a este SDD
    desde el dossier Fase 3.
    - **Hecho 2026-08-13:** contrato § estado + §9 A=**Hecha**; dossier Fase 3 enlaza SDD +
      Hospital-Identity + verify.
    - Verificación: enlaces rotos = 0; dossier menciona `docs/sdd/identidad-oleada-a/`.

16. [x] **TSK-ops-5** Documentación tras verificar: README corto en esta carpeta SDD
    (cómo levantar, usuario seed, limitaciones vs oleadas B–E).
    - **Hecho 2026-08-13:** [README.md](README.md) onboarding (Identity + Web local, seed,
      límites B–E).
    - Verificación: un desarrollador nuevo puede seguir el README sin pedir contexto.

---

## Orden sugerido (dependencias)

```text
TSK-ops-1 → TSK-ops-2 → TSK-ops-3
                ↓
         TSK-app-1 → TSK-app-2 → TSK-app-3 → TSK-app-4
                ↓
         TSK-app-5 → TSK-app-6 → TSK-app-7 → TSK-app-8
                ↓
         TSK-app-9 → TSK-app-10 → TSK-app-11
                ↓
         TSK-ops-4 → TSK-ops-5
```

## Bloqueos

| ID tarea | Bloqueo | Acción |
|----------|---------|--------|
| TSK-app-1+ | Repo destino no elegido | **Resuelto F2:** `Hospital-Identity` + Hospital (TSK-ops-1) |
| TSK-app-6 | Forma de `legacy.idPersonal` | **Resuelto:** nullable en `/me`; seed null (TSK-ops-2) |
| TSK-app-3+ | verify-report ausente | **Resuelto:** PASS (TSK-ops-3) |

## Mapa RF → tareas

| RF / NFR | Tareas |
|----------|--------|
| RF-1 RF-2 RF-3 | TSK-app-5 |
| RF-4 | TSK-app-6 |
| RF-5 RF-8 | TSK-app-3 |
| RF-6 RF-7 | TSK-app-7 |
| RF-9 | TSK-app-4 |
| RF-10 | TSK-app-2 |
| NFR-1 | TSK-app-1 |
| NFR-3 | TSK-app-4 |
| NFR-4 | TSK-app-5, TSK-app-11 |
| NFR-5 | TSK-app-8 |
| CA-6 | TSK-app-9 |
