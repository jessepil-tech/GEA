# SDD — Identidad oleada A

`phase_id:` **`sdd.hospital.identidad-oleada-a`**

Primer corte del servicio de identidad Hospital (JWT local + API Key + `/auth/me`) para
el piloto AGI / ANUNCIADOR. **Sin OIdentity / OIDC.**

| Artefacto | Estado |
|-----------|--------|
| [spec.md](spec.md) | reviewed |
| [plan.md](plan.md) | reviewed |
| [tasks.md](tasks.md) | **app + ops cerrados** (gate-done oleada A) |
| [verify-report.md](verify-report.md) | **PASS** (2026-08-13) |

Diseño padre: [`migracion-identidad.md`](../../../arquitectura/migracion-identidad.md) ·
Contrato: [`contrato-api-identidad.md`](../../../arquitectura/contrato-api-identidad.md) (oleada A = **hecha**).

Seguimiento: [tasks.md](tasks.md).

---

## Cómo levantar (desarrollador nuevo)

### Requisitos

- Java **21**, Maven 3.9+, Docker Desktop (Dev Services / tests)
- Node 20+ solo si vas a probar el front Angular

### 1) API — Hospital-Identity

Path local típico: `/Volumes/External/Development/osw/grupogea/Hospital-Identity`  
Remoto ADO: `GrupoGEA/Hospital-Identity`, rama **`dev/dev`**.

```bash
export JAVA_HOME="/opt/homebrew/opt/openjdk@21/libexec/openjdk.jdk/Contents/Home"
cd /path/to/Hospital-Identity

# Compuerta IT (NFR-4 / CA 1–5)
./tools/verify-oleada-a.sh

# API en caliente (Postgres vía Dev Services)
mvn quarkus:dev -pl presentation-api -am
```

Health: `http://localhost:8080/q/health` · OpenAPI: `/q/openapi` · Swagger: `/q/swagger-ui`

Smoke HTTP (con la API arriba):

```bash
./tools/smoke-auth-oleada-a.sh
```

### 2) Front piloto — Hospital-Web (opcional)

Path local: `/Volumes/External/Development/osw/grupogea/Hospital-Web`  
(sin remoto ADO por ahora; trabajo local).

```bash
cd /path/to/Hospital-Web
npm install
npm start   # http://localhost:4200 — authMode=local → Identity :8080
```

CORS Identity ya permite `http://localhost:4200`.

---

## Credenciales seed (solo dev)

| Recurso | Valor |
|---------|--------|
| Usuario | `admin` |
| Password | `Admin123!` |
| Rol | `admin_role` |
| `subjectType` | `PERSONAL` |
| `legacy.idPersonal` | `null` |
| API Key | header `X-API-Key: demo-api-key` |

No usar estos valores en entornos reales.

---

## Qué incluye oleada A

- Emisor JWT local (`iss=hospital-identity`, `aud=hospital-clients`)
- `POST /api/v1/auth/login|refresh|logout`, `GET /auth/me`, `GET /auth/mode`
- API Key → `GET /api/v1/identity/ping`
- Persistencia Identity en **PostgreSQL** (no Oracle 11.2)
- Cliente Angular local apuntando a Identity (`Hospital-Web`)

Runtime dual (legacy Oracle 11.2 vs código nuevo Postgres): ver dossier §6.

---

## Limitaciones (oleadas B–E)

| Fuera de A | Oleada |
|------------|--------|
| Hash legacy Thinksoft / reforzado a BCrypt | B |
| Reset / cambio de password de producto | B |
| Admin de usuarios / roles / menús | C |
| Impersonation (`act`) | D |
| Adapter rutas `api_seguridad_nodejs` | E |
| Puente `legacy.idPersonal` ↔ Oracle | post-A / piloto datos |
| OIdentity / OIDC | **no aplica** a Hospital Identity |

---

## Enlaces útiles

| Doc | Rol |
|-----|-----|
| [`contrato-api-identidad.md`](../../../arquitectura/contrato-api-identidad.md) | Contrato HTTP |
| [`dossier-migracion.md`](../../../arquitectura/dossier-migracion.md) § Fase 3 | Contexto piloto |
| [`analisis-identity-separado.md`](../../../arquitectura/analisis-identity-separado.md) | Por qué repo separado |
| `Hospital-Identity/README.md` | Detalle build / claims / CA |
