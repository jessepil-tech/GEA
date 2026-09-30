---
title: SDD — Especificación Oleada A identidad
description: |
  Primer corte del servicio de identidad Hospital: JWT local + API Key + /auth/me,
  consumible por el piloto AGI / ANUNCIADOR. Sin OIdentity/OIDC.
version: 0.1.0
status: reviewed
owner: grupogea
last_updated: 2026-08-13
phase_id: sdd.hospital.identidad-oleada-a
scope_project: grupogea
scope_repository: hospital-legacy
---

# SDD — Especificación: `sdd.hospital.identidad-oleada-a`

## Convenciones de IDs — OBLIGATORIO

| Campo | Valor |
|-------|--------|
| **Namespace** | `sdd.hospital.identidad-oleada-a` |
| **IDs locales** | `RF-n`, `NFR-n`, `INV-n` |
| **IDs globales** | `sdd.hospital.identidad-oleada-a::RF-1` |
| **Tareas** | `TSK-app-n`, `TSK-ops-n` |

Entradas de diseño ya cerradas:

- [`docs/migracion-identidad.md`](../../../arquitectura/migracion-identidad.md)
- [`docs/contrato-api-identidad.md`](../../../arquitectura/contrato-api-identidad.md)

---

## Resumen

### Problema

El login del hospital está fragmentado: personal contra usuarios Oracle, portales contra
`api_seguridad_nodejs` / hashes en tablas, integraciones con cuentas `*_TS`. El piloto
AGI + ANUNCIADOR en Quarkus + Angular necesita **un solo emisor de tokens** sin IdP
externo (sin OIdentity).

### Resultado deseado

Una API Quarkus basada en el starter corporativo (modo `local` + API Key) que:

1. Emita y renueve JWT para humanos.
2. Exponga `/api/v1/auth/me` con identidad mínima y puente legacy (`idPersonal`).
3. Acepte API Keys para integraciones del piloto.
4. No publique rutas OIDC.
5. Pueda validarse en local por AGI/ANUNCIADOR (u un cliente de prueba) sin Oracle como
   directorio de identidad.

Fuera de alcance de esta oleada: admin de users/roles, hash legacy de portales, reset
masivo de personal, impersonation, consola `seguridad/`, absorción completa de
`api_seguridad_nodejs` (queda como oleada E / adapter).

## Usuarios / actores

| Actor | Necesidad |
|-------|-----------|
| App Angular piloto (AGI/ANUNCIADOR) | Login local, refresh, logout, conocer al usuario |
| Servicio/integración del piloto | Autenticarse con `X-API-Key` |
| Desarrollador | Levantar la API con perfil `local-jwt` y claves demo |

## Requisitos funcionales

| Id | Requisito |
|----|-----------|
| RF-1 | `POST /api/v1/auth/login` con username/password valida contra tablas `users` del starter (BCrypt) y devuelve access + refresh token |
| RF-2 | `POST /api/v1/auth/refresh` renueva el par de tokens con refresh válido |
| RF-3 | `POST /api/v1/auth/logout` invalida el refresh (sesión) |
| RF-4 | `GET /api/v1/auth/me` con Bearer devuelve id, login, **subjectType** (obligatorio; seed = `PERSONAL`), roles, permissions (lista vacía permitida) y **`legacy.idPersonal` nullable** (seed = `null`; sin leer Oracle) |
| RF-5 | `GET /api/v1/auth/mode` responde `identityMode=local`, `jwtEnabled=true`; sin exponer OIDC activo |
| RF-6 | Request con `X-API-Key` válida obtiene identidad de integración con rol `ApiKeyUser` (u homólogo configurado) |
| RF-7 | Request con `X-API-Key` inválida responde 401 y no cae al Bearer |
| RF-8 | Rutas `/api/v1/auth/oidc/*` no están habilitadas / no forman parte del contrato publicado |
| RF-9 | Seed de al menos un usuario humano de prueba y una API Key de prueba para el piloto |
| RF-10 | JWT local del starter: **`sub` = login**, **`id` = UUID**; `role`/`groups` alimentan `@RolesAllowed`. No hace falta duplicar claims ni adaptar Angular para el smoke |

## Requisitos no funcionales

| Id | Requisito |
|----|-----------|
| NFR-1 | Partir del starter Quarkus corporativo (`features.identity-mode=local`), sin inventar otro stack de auth |
| NFR-2 | Access token TTL configurable; valor inicial acotado (como el starter) |
| NFR-3 | Claves JWT y secretos de API Key no se commitean; demo solo en perfil local/dev |
| NFR-4 | Tests de integración (`@QuarkusTest`) cubren login feliz, credencial inválida, refresh, me, API Key válida/inválida |
| NFR-5 | OpenAPI documenta los endpoints de sesión y `/me` |

## Invariantes

| Id | Invariante |
|----|------------|
| INV-1 | No hay dependencia de OIdentity ni de Keycloak en runtime de esta oleada |
| INV-2 | Oracle no es directorio de identidad para el piloto A |
| INV-3 | Un solo emisor de access tokens humanos en el perímetro del piloto |

## Criterios de aceptación (Given / When / Then)

1. **Dado** un usuario seed con password BCrypt, **cuando** hace `POST /auth/login`, **entonces** recibe 200 con access y refresh JWT firmados por la app.
2. **Dado** un access token válido, **cuando** llama `GET /auth/me`, **entonces** recibe su identidad y no 401.
3. **Dado** un refresh válido, **cuando** llama `POST /auth/refresh`, **entonces** recibe un nuevo par; tras `logout`, el refresh anterior no sirve.
4. **Dado** `X-API-Key` configurada, **cuando** llama un endpoint `@RolesAllowed("ApiKeyUser")`, **entonces** 200; con clave mala, 401.
5. **Dado** el perfil de la app, **cuando** se consulta `/auth/mode`, **entonces** `identityMode` es `local` y no hay flujo OIDC usable.
6. **Dado** el cliente Angular del starter en `authMode=local`, **cuando** apunta al base URL de esta API, **entonces** puede completar sign-in y guardar tokens (smoke).

## Supuestos y dependencias

- Existe (o se crea) un repo/app Quarkus a partir del starter; este SDD vive en
  `hospital-legacy` como origen de verdad del alcance.
- PostgreSQL Dev Services / local para tablas Identity del starter.
- No se requiere VPN ni Oracle para cerrar la oleada A.
- Oleadas B–D dependen de que A esté verde.

## Compuertas SDD (`sdd.hospital.identidad-oleada-a`)

| Gate | TSK / criterio |
|------|----------------|
| `…gate-spec` | Spec revisada (este documento) |
| `…gate-plan` | [plan.md](plan.md) revisado |
| `…gate-tasks` | [tasks.md](tasks.md) con IDs `TSK-*` |
| `…gate-verify` | [verify-report.md](verify-report.md) PASS/WAIVED antes de implementación masiva |
| `…gate-done` | CA 1–6 evidenciados + doc actualizada |

## Dudas abiertas

- ~~¿Repo destino?~~ **Cerrado F2 2026-08-13:** repo `Hospital-Identity` + solución Hospital ([plan.md](plan.md)).
- ~~¿`subjectType` / `legacy.idPersonal` en A?~~ **Cerrado 2026-08-13:** sí en contrato
  `/me` (subjectType obligatorio; legacy nullable/null en seed). Sin Oracle. Detalle en
  plan § TSK-ops-2.
- ~~¿Claims `id` vs `sub`?~~ **Cerrado 2026-08-13:** el starter ya emite ambos
  (`sub`=login, `id`=UUID); Angular lee `sub`. Sin cambio de contrato.
