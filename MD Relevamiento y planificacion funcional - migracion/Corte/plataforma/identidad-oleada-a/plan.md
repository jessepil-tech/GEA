---
title: SDD — Plan técnico Oleada A identidad
description: |
  Cómo implementar la oleada A sobre el starter Quarkus (modo local + API Key).
version: 0.1.0
status: reviewed
owner: grupogea
last_updated: 2026-08-13
phase_id: sdd.hospital.identidad-oleada-a
spec: ./spec.md
---

# SDD — Plan técnico: `sdd.hospital.identidad-oleada-a`

## Resumen ejecutivo

Reusar el starter Quarkus en modo `local-jwt` + API Key, agregar `/auth/me` con el puente
legacy mínimo, desactivar OIDC en el artefacto del hospital, sembrar usuario y API Key de
piloto, y demostrar consumo desde Angular `authMode=local`. Sin Oracle y sin admin de
permisos.

## Enfoque propuesto

1. **Bootstrap** de una app Quarkus desde
   `/Volumes/External/Development/starters/quarkus` (copia/adaptación de paquete base
   `com.ost…` → namespace del hospital acordado).
2. **Perfil fijo** `local-jwt`: `features.identity-mode=local`,
   `features.enable-jwt-authentication=true`, `features.enable-api-key-authentication=true`,
   `quarkus.oidc.enabled=false`.
3. **Extensión mínima** sobre `AuthResource`: `GET /api/v1/auth/me` leyendo `JsonWebToken` +
   datos de `users` / claims. Incluir `subjectType` (default `PERSONAL` o `SERVICIO`) y
   `legacy.idPersonal` nullable (columna o claim stub).
4. **Claims**: emitir `id` como hoy el starter; además copiar el mismo valor a `sub` si
   hace falta para Angular, o documentar mapeo en el cliente (`getUserName`).
5. **Endpoint smoke** `@RolesAllowed("ApiKeyUser")` (puede ser el `TestResource` del
   starter o uno `GET /api/v1/identity/ping`) para validar API Key.
6. **Seed Flyway**: usuario piloto + rol; API Key solo en `application-local-jwt.properties`
   / env, no en SQL con secreto real.
7. **Angular**: app desde starter con `authMode=local`, `apiUrl` apuntando a la API; smoke
   sign-in.

### Repo destino — **F2 ACORDADO 2026-08-13**

Dos productos de plataforma/negocio (no N backends clínicos):

| Repo / artefacto | Rol |
|------------------|-----|
| **`Hospital-Identity`** | Emisor de JWT / API Keys / users / permisos en token. Oleada A del SDD. Deploy y scaling propios. |
| **Solución Hospital** (nombre a acordar, p. ej. `Hospital`) | Una solución Quarkus: `core` + `application` (features clínicas, AGI, anunciador…) + `infrastructure` + `presentation-api`. **Valida** JWT (clave pública / JWKS de Identity); no reimplementa login. |
| **`Hospital-Legacy`** | Referencia + SDD + relevamiento. No hospeda las APIs nuevas. |

Fronts Angular (shell, tótem, anunciador, portales, consola) → login/refresh contra
**Identity**; APIs de negocio contra **Hospital** con el mismo Bearer.

| Evolución de la decisión | Contenido |
|--------------------------|-----------|
| TSK-ops-1 (12 ago) | Repo Identity dedicado |
| Refinado (13 ago mañana) | Feature identity dentro del Hospital (un solo backend) |
| **F2 (13 ago)** | Identity **sí** separado (plataforma); Hospital **un** core de negocio |

Análisis: [`docs/analisis-identity-separado.md`](../../../arquitectura/analisis-identity-separado.md).

Namespace sugerido Identity: `com.grupogea.hospital.identity`.  
Namespace sugerido Hospital: `com.grupogea.hospital` (ajustable en bootstrap).

Extraer *otros* bounded contexts del Hospital sigue siendo opción posterior con evidencia.
No se parten recepción/HC/AGI en APIs distintas de entrada.

### Decisiones TSK-ops-2 — **CERRADAS 2026-08-13**

#### 1) Claims `id` vs `sub`

**Decisión: no cambiar el contrato del starter Quarkus.**

Evidencia en el starter:

- `SmallRyeJwtTokenService.generateAccessToken` ya hace `.subject(username)` → claim
  **`sub` = login**, y `.claim("id", userId)` → **`id` = UUID** del usuario.
- Angular `AuthSessionService.getUserName()` lee `sub ?? email`. Con el JWT local del
  starter, el smoke de sign-in **ya funciona** sin adaptar el cliente.

| Claim | Significado en Hospital-Identity |
|-------|----------------------------------|
| `sub` | Login (`username`) |
| `id` | Identificador estable del usuario (UUID en tablas Identity) |
| `role` / `groups` | Roles para `@RolesAllowed` |

**TSK-app-2** queda como verificación en el bootstrap (decodificar un JWT real y anexar
ejemplo), no como rediseño. Si más adelante el display name debe ser “Apellido, Nombre”,
eso va por `/auth/me` o claims opcionales `firstName`/`lastName`, no tocando `sub`.

#### 2) `subjectType` y `legacy.idPersonal` en oleada A

**Decisión: modelarlos ya en A, con valores stub / nullable. Sin Oracle.**

| Campo | En oleada A | Fuera de A |
|-------|-------------|------------|
| `subjectType` | Obligatorio en `GET /auth/me`. Seed humano → `PERSONAL`. Integraciones no usan `/me`. Claim JWT opcional `subjectType` si es barato de emitir | Catálogo completo de tipos y reglas por tipo (oleadas B+) |
| `legacy.idPersonal` | Campo nullable en respuesta `/me` (`legacy.idPersonal`). Columna opcional en `users` o tabla puente mínima. Seed del piloto → `null` | Carga masiva desde `PERSONAL`, uso en `p_set_usuario` (convivencia Oracle) |

Motivo: AGI/ANUNCIADOR pueden tipar el contrato desde el día uno; INV-2 se respeta (Oracle
no es directorio). Rellenar IDs reales es trabajo de datos, no de contrato de sesión.

#### 3) Gate spec / plan

Alcance de A confirmado: sesión local + `/me` + API Key + seed + Angular smoke. **Fuera:**
admin users/roles, hash legacy, reset masivo, impersonation, OIDC, absorber Node (E).

Veredicto formal: [verify-report.md](verify-report.md).

---

## Componentes o módulos tocados

| Área | Cambio previsto |
|------|-----------------|
| Starter → app Hospital | Bootstrap repo/app, renombre paquetes, perfiles |
| `presentation-api` AuthResource | `/me`; ocultar/deshabilitar OIDC |
| `infrastructure` JWT / claims | Claims `sub`/`subjectType`/`legacyId` mínimos |
| Flyway Identity | Seed usuario piloto; opcional columna `legacy_id_personal` |
| `application-*.properties` | local-jwt + API Key; OIDC off |
| Tests `AuthResourceIT` | Extender con `/me` y mode local-only |
| Angular starter piloto | `authMode=local`, URL, smoke |
| Docs hospital-legacy | Enlazar SDD desde contrato / dossier |

## Alternativas descartadas

| Opción | Motivo del descarte |
|--------|---------------------|
| OIdentity / `oidc-oidentity` | Decisión de proyecto: no IdP externo |
| Migrar WAR `seguridad/` como login | Es consola admin, no emisor de tokens |
| Auth distinta embebida en AGI y en ANUNCIADOR | Fragmenta el piloto; viola INV-3 |
| Identidad como feature embebida en el API Hospital (F0) | No aísla carga login/refresh ni deja un producto reutilizable limpio; descartado al elegir F2 |
| F1 mismo repo dos deployables | Válido; el equipo prefirió F2 para consumo por otras apps sin entrar al repo Hospital |
| Incluir hash legacy (oleada B) en A | Agranda el corte; A debe cerrar el camino feliz BCrypt |

## Riesgos y mitigación

| Riesgo | Mitigación |
|--------|------------|
| Desalineación claims Quarkus vs Angular | Spike corto en TSK-app-2; emitir `sub` + `id` |
| Scope creep hacia admin/permisos | Gate de verify: fuera de RF-1…10 → otra oleada |
| Secretos en repo | Solo placeholders; secretos en env / local no versionado |
| Elegir mal el repo destino | **Cerrado:** `Hospital-Identity` (TSK-ops-1, 2026-08-12) |

## Verificación prevista

- Tests: `@QuarkusTest` login / refresh / logout / me / mode / API Key (NFR-4).
- Manual: curl script + sign-in Angular contra API local.
- Gate: [verify-report.md](verify-report.md) PASS antes de implementar en masa.

## Dependencias de la spec

- Spec: [spec.md](spec.md) — `sdd.hospital.identidad-oleada-a`
- Diseño: [`docs/migracion-identidad.md`](../../../arquitectura/migracion-identidad.md) §5.11
- Contrato: [`docs/contrato-api-identidad.md`](../../../arquitectura/contrato-api-identidad.md) §2–4, oleada A
