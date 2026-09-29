# Contrato mínimo — API de identidad

Contrato que debe exponer el servicio de identidad del hospital (modo `local` del
starter Quarkus). Es la base del piloto AGI + ANUNCIADOR y de la futura consola de
seguridad.

Complementa [`migracion-identidad.md`](migracion-identidad.md). No usa OIDC ni OIdentity.

### Estado por oleada (2026-08-13)

| Oleada | Estado | Evidencia |
|--------|--------|-----------|
| **A** | **Hecha** (local) | Repo [`Hospital-Identity`](https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-Identity) · SDD [`docs/sdd/identidad-oleada-a/`](../cortes/plataforma/identidad-oleada-a/) · `./tools/verify-oleada-a.sh` + smoke curl PASS |
| B–E | Pendiente | Ver §9 |

Alcance A cerrado: `login` / `refresh` / `logout` / `me` / `mode` + API Key (`identity/ping`) + OpenAPI. Sin hash legacy, admin users, impersonation ni adapter Node.

---

## 1. Principios

1. **Un solo emisor de tokens** para personal, portales e integraciones (estas últimas
   con API Key).
2. Las demás APIs **validan el JWT en local** con la clave pública; no consultan este
   servicio en cada request.
3. Los paths de sesión reutilizan los del starter (`/api/v1/auth/*`) para no pelear con
   el feature `auth/` de Angular.
4. Lo que hoy hace `ANUNCIADOR/api_seguridad_nodejs` (`/autenticacion`, `/validatetoken`,
   registro, hash) se reemplaza por este contrato; se puede dejar un *adapter* temporal
   con las rutas viejas si hace falta convivencia corta.
5. La consola de administración es **cliente** de este API, no dueña de la identidad.
6. **Repo destino (F2, 2026-08-13):** **`Hospital-Identity`** (repo/deploy propio) emite
   JWT y API Keys. La solución **Hospital** (negocio) valida el Bearer en local. Fronts
   satélite consumen ambos. Oleada A bootstrappea Identity primero. Detalle:
   [`docs/analisis-identity-separado.md`](analisis-identity-separado.md),
   [`docs/sdd/identidad-oleada-a/plan.md`](../cortes/plataforma/identidad-oleada-a/plan.md).

---

## 2. Sesión (ya en el starter)

| Método | Path | Auth | Notas |
|---|---|---|---|
| `GET` | `/api/v1/auth/mode` | pública | Debe responder `identityMode=local`, `jwtEnabled=true` |
| `POST` | `/api/v1/auth/login` | pública | Body: `{ "username", "password" }`. Respuesta: access + refresh |
| `POST` | `/api/v1/auth/refresh` | Bearer | Extiende sesión |
| `POST` | `/api/v1/auth/logout` | Bearer | Invalida refresh |

Rutas `/api/v1/auth/oidc/*` del starter: **no se publican** en este proyecto.

### Extensiones de login respecto del starter

| Caso | Comportamiento |
|---|---|
| Hash legacy de portal (`legacy-sha256-thinksoft`) | Verifica `SHA-256("THINKSOFT"+lower(pass))`; si ok, reescribe a BCrypt y emite token |
| Hash legacy sin `lower` (defecto de `BBCambiarPassword`) | Segundo intento; si ok, igual refuerza |
| Personal con `mustChangePassword` | Login devuelve `403` + código `PASSWORD_RESET_REQUIRED` (o token de un solo uso acotado al cambio) |
| Usuario bloqueado / inactivo | `401` / `403` según caso, sin filtrar si el usuario existe |

---

## 3. Yo y mi sesión

| Método | Path | Auth | Propósito |
|---|---|---|---|
| `GET` | `/api/v1/auth/me` | Bearer | Perfil mínimo: id, login, tipo de sujeto, roles, permisos |
| `POST` | `/api/v1/auth/password` | Bearer | Cambio de contraseña (aplica política) |
| `POST` | `/api/v1/auth/password/reset/request` | pública | Dispara mail / ticket de reset |
| `POST` | `/api/v1/auth/password/reset/confirm` | token de reset | Define contraseña nueva |

### `GET /api/v1/auth/me` — forma sugerida

```json
{
  "id": "uuid-usuario",
  "login": "jfernandez",
  "subjectType": "PERSONAL",
  "displayName": "Fernández, Juan",
  "mustChangePassword": false,
  "roles": ["perfil:enfermeria_piso"],
  "permissions": ["menu:ENFERMERIA_INTERNADOS", "menu:HISTORIA_CLINICA"],
  "legacy": {
    "idPersonal": null
  }
}
```

En oleada A el seed usa `subjectType=PERSONAL`, `permissions` desde claims (p. ej.
`audit.read` en demo), y `legacy.idPersonal=null`. El JWT asociado lleva `sub`=login e
`id`=UUID (comportamiento
del starter Quarkus; Angular lee `sub`).

---

## 4. Integraciones (API Key)

Ya contemplado por el starter:

- Header `X-API-Key`
- Identidad `api-key-client` con roles configurados (`ApiKeyUser`, …)
- Una clave por canal (`ANUNCIADOR`, `LABORATORIO`, `SCHEDULER`, …), reemplazo de las
  cuentas `*_TS`

Administración de claves (extensión):

| Método | Path | Auth | Propósito |
|---|---|---|---|
| `GET` | `/api/v1/identity/api-keys` | rol admin | Listar (sin secretos) |
| `POST` | `/api/v1/identity/api-keys` | rol admin | Alta; devuelve el secreto una sola vez |
| `POST` | `/api/v1/identity/api-keys/{id}/rotate` | rol admin | Rotación |
| `DELETE` | `/api/v1/identity/api-keys/{id}` | rol admin | Baja |

---

## 5. Administración de usuarios

Para la consola de seguridad y operaciones. No va en el JWT de un usuario final genérico.

| Método | Path | Auth | Propósito |
|---|---|---|---|
| `GET` | `/api/v1/identity/users` | permiso `users.read` | Búsqueda / paginado |
| `GET` | `/api/v1/identity/users/{id}` | `users.read` | Detalle |
| `POST` | `/api/v1/identity/users` | `users.write` | Alta (personal o portal) |
| `PATCH` | `/api/v1/identity/users/{id}` | `users.write` | Datos, estado, tipo |
| `POST` | `/api/v1/identity/users/{id}/lock` | `users.write` | Bloqueo |
| `POST` | `/api/v1/identity/users/{id}/unlock` | `users.write` | Desbloqueo |
| `POST` | `/api/v1/identity/users/{id}/reset-password` | `users.write` | Fuerza reset / envía mail |

El alta de personal **no** crea usuario Oracle. El alta de portal puede aceptar el hash
legacy marcado o una contraseña nueva ya en BCrypt.

---

## 6. Roles y permisos (autorización)

Capa gruesa = roles (`PERFIL_ACCESO`). Capa fina = permisos de menú
(`MENU_PERFIL_ACCESO`). Ambas se reflejan en claims del JWT al emitir / refrescar.

| Método | Path | Auth | Propósito |
|---|---|---|---|
| `GET` | `/api/v1/identity/roles` | `roles.read` | Catálogo de perfiles |
| `POST` | `/api/v1/identity/roles` | `roles.write` | Alta de perfil |
| `PATCH` | `/api/v1/identity/roles/{id}` | `roles.write` | Edición |
| `GET` | `/api/v1/identity/roles/{id}/permissions` | `roles.read` | Menús del perfil |
| `PUT` | `/api/v1/identity/roles/{id}/permissions` | `roles.write` | Reemplazo del set de menús |
| `GET` | `/api/v1/identity/users/{id}/roles` | `roles.read` | Perfiles del usuario |
| `PUT` | `/api/v1/identity/users/{id}/roles` | `roles.write` | Asignación |
| `GET` | `/api/v1/identity/menus` | `roles.read` | Árbol / listado `MENU_APLICACION` |

Chequeo puntual (opcional; preferir claims en el token):

| Método | Path | Auth | Propósito |
|---|---|---|---|
| `GET` | `/api/v1/identity/permissions/check?permission=menu:X` | Bearer | `true`/`false`; **falla cerrado** |

---

## 7. Suplantación

| Método | Path | Auth | Propósito |
|---|---|---|---|
| `POST` | `/api/v1/identity/impersonations` | permiso `impersonate` | Body: `{ "targetUserId", "reason" }`. Devuelve token con `sub`=objetivo, `act`=operador |
| `DELETE` | `/api/v1/identity/impersonations/current` | Bearer del token de suplanta | Fin anticipado |
| `GET` | `/api/v1/identity/impersonations` | `impersonate` o auditoría | Historial |

Sin `ALTER USER`. Sin ventana en la que el suplantado pierda su contraseña.

---

## 8. Claims del access token

Mínimo alineado al starter + hospital:

| Claim | Origen | Uso |
|---|---|---|
| `sub` / `id` | usuario | Identidad |
| `role` / `groups` | perfiles | `@RolesAllowed` |
| `Permission` | menús / acciones | enforce fino (extensión) |
| `subjectType` | tipo de sujeto | Enrutado de reglas |
| `legacyId` | `id_personal` u homólogo | Contexto Oracle / auditoría |
| `act` | solo suplantación | Quién inició |

Las APIs de negocio no necesitan conocer la tabla `users`; con el token alcanza.

---

## 9. Orden de implementación sugerido

| Oleada | Qué | Para quién | Estado |
|---|---|---|---|
| A | `/auth/login|refresh|logout|me` + JWT local + API Key | Piloto AGI / ANUNCIADOR — SDD: [`docs/sdd/identidad-oleada-a/`](../cortes/plataforma/identidad-oleada-a/) | **Hecha** (2026-08-13) |
| B | Verificador dual de hash legacy + reset | Portales | Pendiente |
| C | Users admin + roles/permisos | Consola de seguridad (reescritura) | Pendiente |
| D | Impersonation + política de contraseñas completa | Operación / soporte | Pendiente |
| E | Adapter de rutas viejas de `api_seguridad_nodejs` (si hace falta) y apagado | Corte limpio | Pendiente |

---

## 10. Fuera de este contrato

- Lógica clínica y APIs de dominio  
- Emisión de reportes BIRT  
- Conexión por usuario a Oracle (solo el legado la conserva)  
- OIDC / OIdentity / Keycloak  
