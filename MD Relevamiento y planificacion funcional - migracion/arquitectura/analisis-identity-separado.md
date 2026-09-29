# Análisis: ¿Identity como servicio separado?

Fecha: 2026-08-13  
Contexto: tras acordar **una solución Quarkus para el negocio clínico** y **varios
fronts**, re-evaluar si **Identity** es la excepción que sí conviene desplegar aparte.

No es lo mismo que “un backend por módulo legacy”. Identity es **plataforma**, no un
feature clínico más.

---

## 1. Las dos razones que aportaste

### A) Aislar carga de login / refresh

| Hecho medido / diseño | Implicancia |
|----------------------|-------------|
| ~1.650 usuarios distintos/mes; picos de turno | Login es **burst** (entrada de guardia, cambio de turno), no carga parecida a HC/internación |
| Access TTL corto (starter: minutos) + refresh | El tráfico `/auth/refresh` es **periódico y transversal** a todas las UIs |
| Caída o latencia en auth | Tumba *todas* las apps a la vez si auth vive en el mismo proceso que el núcleo |

**Qué logra un servicio aparte**

- Escalar réplicas de Identity en horarios pico sin escalar todo el hospital.
- Un despliegue/regresión del núcleo clínico **no** reinicia el login.
- Límites de rate-limit, WAF y métricas enfocados en `/auth/*`.

**Qué *no* hace falta para eso**

- No hace falta partir recepción, HC, AGI, etc.
- A veces basta con **mismo artefacto, otro Deployment** que solo expone `/auth` (menos
  aislamiento real: mismo release, misma JVM de build). El aislamiento fuerte es
  **ciclo de vida + scaling independientes**.

### B) Herramienta reutilizable por otras aplicaciones

Esta es, en la práctica, la razón más fuerte.

| Consumidor potencial | ¿Necesita el mismo login? |
|----------------------|---------------------------|
| Shell clínico Angular | Sí |
| Tótems / AGI | Sí |
| Anunciador | Sí (hoy tiene `api_seguridad_nodejs` propio) |
| Portales (HOS-APP, pacientes) | Sí |
| Consola seguridad | Sí |
| Futuros productos GrupoGEA en el mismo hospital / grupo | Probable |

Si Identity vive *dentro* del API clínico:

- Cada app “reutiliza” acoplándose al deploy del hospital → el hospital se vuelve
  **plataforma accidental**.
- Versionar auth sin soltar el núcleo es más difícil.
- El contrato público de login se confunde con APIs de negocio.

Un Identity aparte dice: **“este es el emisor de tokens del ecosistema”**, y el hospital
es *un* cliente más (el más grande).

Eso **no** es OIdentity corporativo multi-tenant: puede seguir siendo JWT local del
starter, API propia, sin IdP externo. Solo cambia el **borde de despliegue y de producto**.

---

## 2. Por qué esto no contradice “un solo core de negocio”

```text
┌─────────────────────────────────────────┐
│  Identity (plataforma)                  │  ← login, refresh, users, API Keys,
│  deploy / scaling / release propios     │    permisos en token, consola admin auth
└─────────────────┬───────────────────────┘
                  │ JWT (clave pública) / API Key
┌─────────────────▼───────────────────────┐
│  Hospital (negocio)                     │  ← UNA solución: core + application
│  features clínicas, AGI, anunciador…    │    features, infra, presentation-api
│  N Angular (shell, tótem, anunciador)     │
└─────────────────────────────────────────┘
```

| Separar | ¿Tiene sentido de entrada? |
|---------|----------------------------|
| Identity ↔ Hospital | **Sí evaluar / sí recomendable** (plataforma vs dominio) |
| Recepción ↔ HC ↔ Internación (backends) | **No** (mismo core, features) |
| AGI/Anunciador backends vs Hospital | **No** de entrada (mismo API; front distinto) |

La complejidad de “N soluciones + librerías compartidas” aparece cuando partís **dominio**.
Entre Identity y Hospital lo que se comparte es delgado y estable:

- Contrato HTTP de auth (ya en `docs/contrato-api-identidad.md`)
- Clave pública JWT (o JWKS)
- Claims (`sub`, `id`, `role`, `Permission`, `legacy.idPersonal`)
- Opcional: tipitos/OpenAPI compartidos (paquete `hospital-auth-contracts`, no el dominio)

No necesitás compartir `application` clínica.

---

## 3. Costos de separarlo (hay que mirarlos de frente)

| Costo | Mitigación |
|-------|------------|
| Dos deploys, dos pipelines, health, config | Compose/k8s desde el piloto; Identity es chico (starter auth) |
| Login cross-origin / CORS / cookies | Bearer en header (como el starter Angular); sin cookies de sesión server |
| Rotación de claves JWT | Proceso documentado; JWKS endpoint en Identity |
| Desarrollo local | `docker compose`: identity + hospital + postgres; o Dev Services |
| Consistencia users ↔ `PERSONAL` | Ya era un problema; puente `legacy.idPersonal` / sync job, da igual 1 o 2 deploys |
| Latencia login → luego API | Irrelevante tras el login; cada request de negocio **no** llama a Identity (valida JWT en local) |

Lo crítico: **las APIs de negocio no deben preguntar a Identity en cada request**. Solo
validan firma JWT. Eso mantiene Identity fuera del camino caliente clínico.

---

## 4. Alternativas (espectro)

| Opción | Descripción | Pros | Contras |
|--------|-------------|------|---------|
| **F0** Feature en el mismo proceso que Hospital | Todo en un JAR/deploy | Mínima ops | Sin aislamiento de carga ni producto reutilizable limpio |
| **F1** Mismo repo, dos artefactos desplegables | `presentation-identity` + `presentation-api` | Un git; scaling aparte | Releases pueden acoplarse si no hay disciplina |
| **F2** Repo/servicio Identity + repo Hospital | Dos productos | Máxima claridad multi-app + scaling | Más ops (aceptable si Identity es estable) |
| **F3** OIdentity / IdP externo | Fuera de alcance del proyecto | — | Descartado |

Para tus dos objetivos (carga + otras apps), **F0 no alcanza**.  
**F1 o F2** sí. La diferencia es organización de código, no el patrón runtime.

---

## 5. Encaje con el piloto y la oleada A

Oleada A (`sdd.hospital.identidad-oleada-a`) **ya es** el contrato de un emisor de tokens.
Encaja naturalmente como **primer entregable de plataforma**:

1. Levantar **Identity** (login/refresh/me/API Key) — oleada A.
2. Shell o AGI/Anunciador consumen JWT.
3. La solución **Hospital** (negocio) se bootstrappea después validando Bearer; sin
   reimplementar auth.

Eso ordena el trabajo: plataforma primero, dominio después — sin partir el dominio.

---

## 6. Números de carga (orden de magnitud, no sizing)

No hace falta un estudio de capacidad para decidir el *borde*:

- Auth: relativamente pocos RPS medios, picos afilados, **crítico**.
- Núcleo: mucho más SQL/PL-SQL, reportes, pantallas; **pesado**.

Meter auth en el mismo pool de hilos/conexiones que reportes BIRT o lotes es mezclar
calidades de servicio distintas. Separar Identity es una forma barata de **QoS**.

---

## 7. Recomendación

**Sí: Identity como servicio (deploy) separado del API de negocio hospitalario.**

Mantener:

- **Un** core de negocio (features clínicas / AGI / anunciador en una solución).
- **Varios** fronts Angular según dispositivo.
- Identity **fuera** de ese core, como plataforma reutilizable.

Elegir entre F1 (mismo repo, dos deployables) y F2 (dos repos) es secundario:

- Preferir **F2 (`Hospital-Identity` + solución Hospital)** si querés que otras apps del
  grupo lo consuman sin “entrar al repo del hospital”.
- Preferir **F1** si el equipo es chico y querés un solo git al inicio, con dos
  `presentation-*` y dos imágenes Docker.

No volver a N backends clínicos. Identity es la **excepción justificada**.

---

## 8. Decisión — **F2 ACORDADO 2026-08-13**

| Pregunta | Decisión |
|----------|----------|
| ¿Separar deploy Identity del API Hospital? | **Sí** |
| ¿Uno o dos repos git? | **F2** — `Hospital-Identity` + solución Hospital |
| ¿Oleada A bootstrappea Identity primero? | **Sí** |

Documentado en mapa, dossier §5.7, SDD plan/tasks, contrato e identidad.
