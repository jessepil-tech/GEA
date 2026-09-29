# Mapa legacy → productos destino (y la UX)

Respuesta a: *¿vamos a terminar con una pestaña por módulo?*  
**No necesariamente. Modularizar el backend no obliga a fragmentar la pantalla del usuario.**

---

## 1. Cómo está hoy (no es un solo WAR, pero sí una UX clínica integrada)

```mermaid
flowchart TB
  subgraph usuarios["Usuarios"]
    staff["Personal clínico / admin"]
    mostrador["Pantallas de espera / llamados"]
    totem["Tótems autoservicio"]
    externos["Pacientes / otros portales"]
  end

  subgraph legacy["Hospital-Legacy (un repo, varios desplegables)"]
    H2["HOSPITAL_2\napp integrada\nmenú + módulos clínicos"]
    HB["HOSPITAL-BUSINESS\nJAR compartido"]
    AFIP["AFIP JAR"]
    VAL["VALIDADORES JAR"]
    SEG["SEGURIDAD.war\nconsola admin"]
    AGI["AGI.war\n+ tótems"]
    ANU["ANUNCIADOR\nNode + Vue"]
    HOS["HOS-APP.war\nportal"]
  end

  staff --> H2
  staff -.-> SEG
  totem --> AGI
  mostrador --> ANU
  externos --> HOS
  H2 --> HB
  H2 --> AFIP
  H2 --> VAL
  AGI --> HB
  AGI --> VAL
  HOS --> HB
```

Hoy el personal clínico trabaja sobre todo en **HOSPITAL_2** (una app, un menú).  
AGI, ANUNCIADOR, HOS-APP y SEGURIDAD **ya son entradas distintas** (otro WAR/URL). La migración no inventa esa separación: ya existe para satélites.

---

## 2. Lo que *no* queremos

```mermaid
flowchart LR
  U[Usuario] --> T1[Pestaña Recepción]
  U --> T2[Pestaña HC]
  U --> T3[Pestaña Internación]
  U --> T4[Pestaña Farmacia]
  U --> T5[Pestaña Facturación]
```

Eso sería un error de producto. **Nadie pidió eso.**  
Separar APIs Quarkus por dominio ≠ abrir una URL por dominio.

---

## 3. Modelo destino — **F2 ACORDADO 2026-08-13**

- **Una solución Hospital** para el negocio clínico (features en `application`).
- **Identity en repo/deploy aparte** (plataforma: login, refresh, users, API Keys).
- **Varios Angular** según dispositivo/audiencia, todos con el mismo JWT.

No es “un backend por módulo legacy”. Solo se separa **plataforma (Identity)** de
**dominio (Hospital)**. Análisis: [`analisis-identity-separado.md`](analisis-identity-separado.md).

```mermaid
flowchart TB
  subgraph ux["Fronts Angular"]
    SHELL["Shell clínico"]
    TOTEM_UI["Tótems"]
    ANU_UI["Anunciador"]
    PORTAL_UI["Portales"]
    ADMIN_UI["Consola seguridad"]
  end

  subgraph id["Hospital-Identity — F2"]
    IDAPI["presentation-api auth\nlogin / refresh / me / API Keys"]
  end

  subgraph hosp["Solución Hospital — un core de negocio"]
    PRES["presentation-api"]
    APP["application features\nnúcleo, AGI, anunciador, afip…"]
    CORE["core + infrastructure"]
  end

  subgraph rpt["hospital-reports — JVM + BIRT 4.24"]
    RENG["ReportEngine\n(no embebido en Quarkus)"]
  end

  SHELL -->|login/refresh| IDAPI
  TOTEM_UI -->|login/refresh| IDAPI
  ANU_UI -->|login/refresh| IDAPI
  PORTAL_UI -->|login/refresh| IDAPI
  ADMIN_UI -->|login + admin users| IDAPI

  PRES -->|generar PDF/XLS| RENG
  SHELL -->|Bearer JWT| PRES
  TOTEM_UI -->|Bearer JWT| PRES
  ANU_UI -->|Bearer JWT| PRES
  PORTAL_UI -->|Bearer JWT| PRES

  PRES --> APP
  APP --> CORE
  PRES -.->|valida firma JWT local\nno llama Identity por request| IDAPI
```

### Lectura en una frase

- Login/refresh/escalado de auth → **Identity**.
- Lógica clínica / AGI / anunciador → **un** Hospital.
- Tótem u otra UI → front dedicado, **mismo** JWT y mismo API de negocio.
- Reportes PDF/XLS → **hospital-reports** (BIRT 4.24 en JVM aparte; ver [`birt-runtime-destino.md`](birt-runtime-destino.md)).

### Por qué las apps legacy separadas no dictan N backends clínicos

Muchas salieron del monolito por deuda tecnológica o por dispositivo. Eso se resuelve con
**features + fronts**, no con un microservicio por carpeta. Identity es la excepción
porque es plataforma reutilizable y carga de distinta calidad de servicio.

---

## 4. Mapa de lo que pediste migrar

| Origen legacy | Destino UX | Destino técnico |
|---------------|------------|-----------------|
| `HOSPITAL_2` / núcleo | Shell único | Features en **Hospital** |
| `HOSPITAL-BUSINESS`, `AFIP` | — | Absorbidos en Hospital |
| `AGI` + tótems | Angular tótem | Features en **Hospital**; auth → Identity |
| `ANUNCIADOR` | Angular anunciador | Features en **Hospital**; auth → Identity (reemplaza Node) |
| `HOS-APP` | Portal | Features en Hospital (cuando toque); auth → Identity |
| `SEGURIDAD` | Consola | UI cliente de **Identity** |
| BIRT (`.rptdesign` en WARs) | Download/impresión desde shell | **hospital-reports** (BIRT 4.24, proceso aparte) |
| Login Oracle / Node | Login único | **Hospital-Identity** |

## 5. Dos formas de armar el shell (elegir después; ambas evitan multi-pestaña)

| Opción | Idea | Cuándo |
|--------|------|--------|
| **A — Modular monolit front** | Un Angular, lazy routes por dominio (`/recepcion`, `/hc`, …), varias APIs detrás | Preferible al inicio: UX idéntica a HOSPITAL_2 |
| **B — Microfrontends** | Varios bundles montados en un shell con menú común | Solo si equipos/releases lo exigen; más complejo |

En ambos casos el usuario ve **una aplicación**. Identity solo autentica; no es otra pestaña de trabajo diario.

```mermaid
flowchart LR
  subgraph shell["Shell Angular (una URL)"]
    MENU[Menú lateral]
    R1[Recepción]
    R2[Historia clínica]
    R3[Internación]
    R4[…]
  end
  MENU --> R1
  MENU --> R2
  MENU --> R3
  MENU --> R4
  R1 --> API1[API núcleo]
  R2 --> API1
  R3 --> API1
```

---

## 6. Qué implica para el piloto (AGI + ANUNCIADOR)

El piloto **sí** puede ser apps separadas sin romper al personal clínico:

- Hoy el tótem y el anunciador **no viven dentro del menú de HOSPITAL_2**.  
- Migrarlos aparte no saca al médico de su app integrada.  
- Sirven para estrenar Quarkus + Angular + Identity sin tocar todavía el monolito clínico.

Cuando migremos el núcleo (`HOSPITAL_2`), la regla de producto es:

> **Paridad UX:** el personal sigue entrando a *un* hospital digital, no a una suite de módulos sueltos.

**Look / acercamiento a PrimeFaces (California-Thinksoft):** no cerrado en F2 —
SDD [`docs/sdd/ux-shell-primefaces/`](../cortes/plataforma/ux-shell-primefaces/) (complejidad L2–L3;
no pixel xhtml).

---

## 7. Decisión de diseño — **ACORDADA 2026-08-13 (F2)**

1. **UX personal clínico:** un shell Angular.
2. **Negocio:** una solución Quarkus Hospital (features; no N APIs clínicas).
3. **Identity (F2):** repo y deploy **`Hospital-Identity`**, separado, reutilizable.
4. **Fronts satélite** (tótem, anunciador, portales, consola) según dispositivo; JWT de Identity; APIs de negocio en Hospital.
5. **No** un backend por carpeta legacy.
6. Más particiones de backend clínico = solo con evidencia posterior.

Dossier §5.7 · SDD plan § Repo destino · [`analisis-identity-separado.md`](analisis-identity-separado.md).

### Runtime de datos (acordado con dossier §6, 2026-08-13)

| Producto | Base |
|----------|------|
| `Hospital-Legacy` (JSF/PLSQL en prod hasta el corte) | Oracle **11.2** |
| `Hospital-Identity` / solución `Hospital` / fronts nuevos | **PostgreSQL** |
| Validación PL/SQL → Quarkus | Golden master en 11.2 (copia) → replay en Postgres |
| Go-live | UAT del nuevo **aislado** → **un corte** (sin unir al viejo) |

No se usa Oracle 19 como puente. No se apunta Hibernate Quarkus 3 al 11.2.
Análisis A/B/C (Postgres + corte vs Quarkus→19 vs solo upgrade legacy):
[`analisis-oracle19-vs-postgres.md`](analisis-oracle19-vs-postgres.md).
