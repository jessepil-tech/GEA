---
title: Árbol de mapeo menú Legacy ↔ Hospital-Web
version: 1.1.0
status: active
owner: grupogea
last_updated: 2026-08-19
phase_id: sdd.hospital.arbol-mapeo-menu-legacy-web
---

# Árbol de mapeo — menú Legacy ↔ Hospital-Web

**Pregunta de producto:** ¿se puede replicar el menú (e iconos) del HOSPITAL_2 en Hospital-Web para que un usuario legacy no se pierda?

**Respuesta corta:** **Sí, es viable** — y además es el modelo destino ya acordado (shell único + menú por `MENU_APLICACION`). Hoy el sidebar de Web es un **piloto plano** (pocas rutas); **no** es todavía el menú de producción. Este documento es el mapa de orientación + plan de paridad.

**Ops 2026-08-19:** home grilla **+** sidebar expandible; hojas sin Angular **disabled**; admin DEV ve árbol completo; permisos por usuario después (M2).

**M1 (UI):** implementado en Hospital-Web — catálogo `hospital-menu.catalog.ts`, iconos en `/images/menu-modulos/`, dashboard grilla.

Complementa: [`mapa-menu-hospital-web.md`](mapa-menu-hospital-web.md) · [`mapa-productos-destino.md`](../arquitectura/mapa-productos-destino.md) · [`contrato-api-identidad.md`](../arquitectura/contrato-api-identidad.md) §6 (`GET /identity/menus`).

---

## 1. Viabilidad

| Capacidad | ¿Viable? | Cómo |
|-----------|----------|------|
| Árbol módulos → submenús → pantallas | **Sí** | Misma fuente: `TS.MENU_APLICACION` + perfil (`MENU_PERFIL_ACCESO`). Identity ya prevé `GET /api/v1/identity/menus`. |
| Labels iguales al legacy | **Sí** | Clave `DESCRIPCION` → i18n (`Resources.properties` / catálogo). |
| Iconos de **módulos** (grilla inicio) | **Sí** | PNG en `HOSPITAL_2/WebRoot/resources/imagenes/iconos/{camelCase(DESCRIPCION)}.png` (~80). Portar a `Hospital-Web/public/images/menu-modulos/`. |
| Iconos en **cada ítem** del menú lateral | **Parcial / no 1:1** | En legacy el lateral Verona **no lleva icono por hoja** (solo texto). El Web actual inventó iconos genéricos (`home`, `clock`, …). Para paridad UX: iconos **solo en módulos**; hojas = texto (como Verona). |
| Rutas 1:1 `.faces` → Angular | **Por oleada** | Cada `ACCION` (`/pages/...`) mapea a una route Angular cuando exista el CU; mientras tanto: entrada **visible deshabilitada** o “Próximamente” + deep-link a legacy si política lo permite. |
| Menú filtrado por perfil | **Sí** | Claims JWT / `Permission` desde `MENU_PERFIL_ACCESO` (oleadas Identity B+). |

### Qué **no** hay que copiar a ciegas

1. El sidebar actual de Hospital-Web mezcla **piloto AGI/ANUNCIADOR** (apps que en legacy ya eran **otra URL**) con pantallas de recepción HOSPITAL_2. Eso confunde: hay que **agrupar** (módulo RECEPCIÓN vs satélite AGI/TV).
2. ~1.200 ítems de menú: no se “hardcodean” en `sidebar-nav.config.ts`. Se **leen de BD/API**.
3. Sidebars clínicos estáticos (`menuLateralRecepcion.xhtml`, `menuLateralDemandaEspontanea.xhtml`, etc.) son **menú de contexto de atención**, no el menú de módulos.

### Modelo UX recomendado (paridad mental)

```text
Login → Home módulos (grilla + PNG)     ← como inicio.xhtml / BBModulos
      → Entrar a módulo RECEPCIÓN
      → Sidebar árbol del módulo       ← como Verona pv:menu / MenuBuilder
      → Pantalla (Angular o “pendiente”)
```

Satélites (como hoy):

| Satélite | Legacy | Destino Web |
|----------|--------|-------------|
| Tótem AGI | AGI.war | `/agi/recepcion` (ops) / tótem físico aparte |
| TV anunciador | anunciadorVue | `/display/anunciadores/:id` (sin shell) |
| Consola seguridad | SEGURIDAD.war | Admin Identity (futuro) |

---

## 2. Cómo se arma el menú en legacy (fuente de verdad)

```text
TS.MENU_APLICACION
  ID_MENU_APLICACION, DESCRIPCION, ID_MENU_APLICACION_PADRE,
  NRO_ORDEN, ACCION, ID_APLICACION (=2 HOSPITAL)
        │
        ├─ filtrado por perfil → MENU_PERFIL_ACCESO / PERSONAL_PERFIL_ACCESO
        │
        ├─ SP: SEGURIDAD.f_get_menu_acceso / f_get_menu_acceso_all
        │
        ├─ Grilla módulos: BBModulos + PNG iconos/
        └─ Lateral: MenuBuilder → pv:menu (Verona)
```

- `ACCION` null → carpeta (submenu).
- `ACCION` = path `/pages/...` → navega a `.faces`.
- `ACCION` = `-` → separador.
- Código: `HOSPITAL_2/.../MenuBuilder.java`, `BBModulos.java`.
- Layout: `contracts/verona-layout/menu.xhtml`.

**Dump completo del árbol:** solo con Oracle (o export Identity). Los DML en `RDBMS/` son incrementales, no el catálogo entero.

---

## 3. Estado actual Hospital-Web (piloto) ↔ legacy

Leyenda estado ruta nueva: **listo** · **parcial** · **pendiente** · **starter (ocultar)** · **fuera de shell**.

### 3.1 Árbol de orientación (lo que ve hoy el usuario de Web)

```text
Hospital-Web (sidebar plano hoy)
├── Dashboard                         → (nuevo) home piloto — NO es grilla de módulos
├── Anunciadores                      → ops anunciador (Node/Vue list) + config HOSPITAL
│   └── :id/llamados                  → anunciadorVue Llamados
├── Cola recepción                    → HOSPITAL_2 recepción / cola puesto
├── Espera ambulatoria                → HOSPITAL_2 recepción / cola espera serv. amb.
├── Recepción AGI                     → AGI tótem (parcial) — otra app en legacy
├── Espera AGI                        → post-ticket AGI + Llamar (puesto)
├── Demanda espontánea                → módulo DEMANDA_ESPONTANEA (slice CU-C)
├── Convenios                         → CONFIGURACION / catálogo convenio (slice CU-A)
├── Products                          → STARTER — no hospital
└── Showcase*                         → STARTER UI — no hospital

* Display TV /display/anunciadores/:id  → anunciadorVue sala (sin ítem de menú)
```

### 3.2 Tabla detallada (rutas + pantallas legacy)

| # | Menú Web | Route Angular | Pantalla / path legacy | App legacy | Icono módulo legacy | Estado |
|---|----------|---------------|------------------------|------------|---------------------|--------|
| 1 | Dashboard | `/dashboard` | — (equivalente futuro: `pages/inicio.xhtml` grilla módulos) | HOSPITAL_2 | — | parcial (home distinto) |
| 2 | Anunciadores | `/anunciadores` | Config anunciador + listado Node; PNG `anunciador.png` | HOSPITAL_2 + ANUNCIADOR | `anunciador.png` | parcial |
| 2b | Llamados (detalle) | `/anunciadores/:id/llamados` | Vue `Llamados.vue` / API Node pacientes | ANUNCIADOR | — | parcial |
| 2c | Display TV | `/display/anunciadores/:id` | `anunciadorVue` Simple/Compuesto | ANUNCIADOR | — | listo DEV (fuera shell) |
| 3 | Cola recepción | `/recepcion/cola` | `pages/recepcion/recepcionPaciente/cabeceraRecepcion.xhtml` (+ popups cola/últimos en `recepcionPaciente.xhtml`); bean `bbRecepcionColaEsperaRecep` / `actLlamar` | HOSPITAL_2 | bajo módulo `recepcion.png` | listo M1–M3 |
| 4 | Espera ambulatoria | `/recepcion/espera-amb` | `pages/recepcion/recepcionPaciente/colaEspera.xhtml` (`bbConsultaColaEsperaRecepcion`) | HOSPITAL_2 | `recepcion.png` | listo M4 (acciones menú pendientes) |
| 5 | Recepción AGI | `/agi/recepcion` | AGI `pages/termAutoGestCentro/inicio.faces` (+ terminal) | AGI | (app aparte) | parcial piloto G1 |
| 6 | Espera AGI | `/agi/espera` | Post-recepción AGI + llamar a TV (paridad `btnLlamar` / cola puesto, no tótem) | AGI + HOSPITAL_2 recepción | — | listo CU-B/B.1 |
| 7 | Demanda espontánea | `/agi/demanda-espontanea` | Módulo `DEMANDA_ESPONTANEA` → p.ej. `pages/ambulatoria/demandaEspontanea/listaEsperaDemandaEspontanea.xhtml`, `demandaEspontanea.xhtml`, `inicioServicioCentro.xhtml` | HOSPITAL_2 | `demandaEspontanea.png` | parcial (medidor CU-C) |
| 8 | Convenios | `/catalogo/convenios` | `pages/configuracion/convenio/convenio.xhtml` (+ hijos plan/cobertura en misma carpeta) | HOSPITAL_2 | suele vivir bajo **CONFIGURACION** (`configuracion.png`) | parcial CU-A |
| 9 | Products | `/products` | — | — | — | starter → **ocultar** |
| 10 | Showcase | `/showcase/*` | — | — | — | starter → off en prod |
| 11 | Profile | `/profile` | — / Identity | Identity | — | parcial oleada A |
| — | Login | `/auth/signin` | Login HOSPITAL / Identity | Identity | — | oleada A |

### 3.3 Dónde “encajarían” en el menú mental legacy

```text
[Inicio módulos HOSPITAL_2]
│
├── RECEPCIÓN (recepcion.png)
│   ├── … (decenas de ACCION bajo /pages/recepcion/…)     → la mayoría PENDIENTE
│   ├── Cola puesto / Llamar  ≈  cabeceraRecepcion         → /recepcion/cola        ✅
│   ├── Cola espera amb.      ≈  colaEspera.xhtml          → /recepcion/espera-amb  ✅
│   └── (flujo recepción paciente completo)               → pendiente
│
├── DEMANDA ESPONTANEA (demandaEspontanea.png)
│   └── lista / atención / …                              → /agi/demanda-espontanea 🟡
│
├── CONFIGURACION (configuracion.png)
│   └── Convenios / planes / …                            → /catalogo/convenios     🟡
│
├── (otros ~35 módulos: HC, INTERNACION, FARMACIA, …)     → pendiente
│
└── [Fuera de grilla HOSPITAL — otras URLs legacy]
    ├── AGI tótem                                         → /agi/recepcion          🟡
    ├── Post-AGI + Llamar                                 → /agi/espera             ✅
    └── Anunciador TV / ops                               → /anunciadores + display ✅/🟡
```

---

## 4. Inventario de módulos de primer nivel (legacy)

Nombres de usuario (i18n). El set exacto depende del **perfil**. Icono = PNG homónimo en camelCase.

| Módulo (label) | Clave típica | Icono PNG | En Web hoy |
|----------------|--------------|-----------|------------|
| RECEPCIÓN | RECEPCION | `recepcion.png` | 2 pantallas parciales |
| HISTORIA CLINICA | HISTORIA_CLINICA | `historiaClinica.png` | — |
| INTERNACION | INTERNACION | `internacion.png` | — |
| ENFERMERIA INTERNADOS | ENFERMERIA_INTERNADOS | `enfermeriaInternados.png` | — |
| DEMANDA ESPONTANEA | DEMANDA_ESPONTANEA | `demandaEspontanea.png` | slice |
| TURNOS | ATENCION_TURNOS | `atencionTurnos.png` | — |
| ATENCION MEDICA | ATENCION_MEDICA | `atencionMedica.png` | `/ambulatoria/espera-atencion` |
| ADMISION INTERNADOS | ADMISION_INTERNADOS | `admisionInternados.png` | — |
| CENTRO DE PROCEDIMIENTOS | CENTRO_PROCEDIMIENTO | `centroProcedimiento.png` | — |
| DEPÓSITO | DEPOSITO | `deposito.png` | — |
| INFORMES | INFORMES | `informes.png` | — |
| DIAGNOSTICO POR IMAGENES | DIAGNOSTICO_POR_IMAGENES | `diagnosticoPorImagenes.png` | — |
| OTROS ESTUDIOS | OTROS_ESTUDIOS | `otrosEstudios.png` | — |
| ENFERMERIA AMBULATORIA | ENFERMERIA_AMBULATORIA | `enfermeriaAmbulatoria.png` | — |
| FACTURACION AMBULATORIA | FACTURACION_AMBULATORIA | `facturacionAmbulatoria.png` | — |
| ADMINISTRACIÓN GENERAL | ADMINISTRACION_GENERAL | `administracionGeneralNa.png` | — |
| OFTALMOLOGIA | OFTALMOLOGIA | `oftalmologia.png` | — |
| FACTURACION INTERNADO | FACTURACION_INTERNADO | `facturacionInternado.png` | — |
| CAJA | CAJA | `caja.png` | — |
| COMPRAS | COMPRAS | `compras.png` | — |
| COBRANZA CONVENIOS | (cobranza) | `cobranzaConvenio.png` | — |
| LABORATORIO | LABORATORIO | `laboratorio.png` | — |
| FARMACIA | FARMACIA | `farmacia.png` | — |
| GUARDIA Y EMERGENCIAS | GUARDIA_Y_EMERGENCIAS | `guardiaYEmergencias.png` | — |
| CONFIGURACION | CONFIGURACION | `configuracion.png` | convenios parcial |
| PANEL DE CONTROL | PANEL_DE_CONTROL | `panelDeControl.png` | — |
| SEGURIDAD | SEGURIDAD | `seguridad.png` | → Identity |
| … | … | (~60 PNG en `iconos/`) | — |

Top uso login (dossier): RECEPCIÓN, HC, INTERNACION, ENFERMERIA INTERNADOS, DEMANDA ESPONTANEA, TURNOS, …

---

## 5. Ejemplo: profundidad real del módulo RECEPCIÓN

Solo en el árbol de archivos hay **decenas** de pantallas bajo `/pages/recepcion/`. El menú BD elige un subconjunto por perfil. Muestra representativa:

| Path legacy (ACCION ≈) | Rol | Equivalente Web |
|------------------------|-----|-----------------|
| `/pages/recepcion/inicio` | Home módulo | pendiente (o grilla) |
| `/pages/recepcion/recepcionPaciente/recepcionPaciente` | Recepción paciente (shell + tabs) | pendiente |
| `.../cabeceraRecepcion` (include) | Cola puesto + **Llamar** | `/recepcion/cola` |
| `.../colaEspera` | Espera ambulatoria | `/recepcion/espera-amb` |
| `.../datosPaciente`, `practicas`, `documentos`, … | Subflujos recepción | pendiente |
| `/pages/recepcion/consultaOrdenesDeServicio` | Consultas | pendiente |
| `/pages/recepcion/ocupacionAmbiente` | Ocupación ambientes | relacionado anunciador N1 (otro contexto) |
| … | … | pendiente |

**Conclusión para el usuario legacy:** con el menú Web actual **parece** que “Recepción” son 2 ítems sueltos; en legacy es un **módulo entero**. La paridad de menú debe mostrar el árbol RECEPCIÓN con hojas ✅ / 🔒 pendientes.

---

## 6. Plan de implementación (recomendado)

### Fase M0 — Orientación (este doc) ✅

- Mapa Web↔legacy + viabilidad.
- Mantener [`mapa-menu-hospital-web.md`](mapa-menu-hospital-web.md) al día con cada route nueva.

### Fase M1 — Empaquetar iconos + IA mínima

**Estado 2026-08-19: hecho en código (Hospital-Web).**

1. [x] Copiar PNG → `Hospital-Web/public/images/menu-modulos/`.
2. [x] **Home:** grilla de módulos (`/dashboard`).
3. [x] **Sidebar:** módulos expandibles; hojas Angular habilitadas; sin Angular → **disabled**.
4. [x] Satélites **AGI / Autogestión** y **ANUNCIADOR** aparte de RECEPCIÓN.
5. [x] Products/Showcase solo si `showcaseEnabled`.
6. [x] DEV admin: árbol completo sin filtro de perfil.
7. [ ] (Opcional) Dump perfil recepción UAT → evidence.

### Fase M2 — Menú desde Identity / filtro perfil

**Estado 2026-08-19: filtro en Web + flag DEV; Identity `GET /menus` pendiente.**

1. [x] `menuShowAll` en appsettings (DEV default `true` → árbol completo).
2. [x] Filtro por claims `Permission` / roles; `admin_role` → todo; fail-open sin claims.
3. [x] `menuDemoAllowedKeys` opcional para probar filtro sin Identity.
4. [ ] Consumir `GET /api/v1/identity/menus` (oleada C Identity).
5. [ ] Guard de ruta por `menuKey` / permiso.
6. [ ] Sync `MENU_APLICACION` → PG Identity.

### Fase M3 — Completar hojas por CU

- Cada SDD de paridad agrega la route y marca la hoja del árbol como **listo**.
- No abrir módulos enteros sin CU (regla waiver).

---

## 7. Matriz anti-pérdida (mensaje al usuario)

| Si en legacy ibas a… | En Hospital-Web hoy… |
|----------------------|----------------------|
| Grilla de módulos post-login | Todavía no: ves un menú piloto corto |
| RECEPCIÓN → cola del box / Llamar | **Cola recepción** |
| RECEPCIÓN → cola espera ambulatoria | **Espera ambulatoria** |
| Tótem AGI (otra URL) | **Recepción AGI** |
| Lista espera post-AGI / llamar TV | **Espera AGI** |
| Pantalla TV sala | URL `/display/anunciadores/{id}` (no menú) |
| DEMANDA ESPONTANEA | **Demanda espontánea** (incompleto) |
| CONFIGURACION → Convenios | **Convenios** (incompleto) |
| CONFIGURACION → Hab. turnos servicio / profesional | **Migrado T2** `/configuracion/hab-turnos-serv` · `/hab-turnos-pers` |
| Resto de módulos / pantallas | **Aún no migrado** — seguir en HOSPITAL_2 |

---

## 8. Mantenimiento

1. Toda route nueva en sidebar → fila en §3.2 **y** en [`mapa-menu-hospital-web.md`](mapa-menu-hospital-web.md).
2. Cuando exista dump Oracle del menú de un perfil piloto (p.ej. recepcionista), anexar `evidence/menu-recepcion-perfil-X.csv` y expandir §5.
3. No documentar tooling de agentes ni suites internas en este artefacto de producto.

---

## 9. Decisiones firmadas (ops) — 2026-08-19

| # | Pregunta | Decisión |
|---|----------|----------|
| 1 | Home vs sidebar | **Ambos:** grilla de módulos en home **y** sidebar con módulos expandibles (no excluyentes). |
| 2 | Hojas sin Angular | **Visibles e inhabilitadas** (disabled / no navegables), no ocultas. |
| 3 | Perfil piloto | **Admin DEV ve todo** vía `menuShowAll: true` (y/o `admin_role`). Filtro por perfil cuando `menuShowAll: false` + claims/`menuDemoAllowedKeys`. Ver §9.1. |

Implica actualizar M1: home = grilla PNG; sidebar = árbol por módulo; hojas `route: null` → UI disabled (tooltip “Pendiente de migración”).

### 9.1 Perfil piloto — decisión revisada (2026-08-19)

**Por ahora no hay `id_personal` recepcionista a mano.** Decisión:

> Filtro por perfil **implementado** en Hospital-Web, con **bypass DEV** `menuShowAll: true` para seguir viendo el árbol completo (admin).

| Flag / claim | Efecto |
|--------------|--------|
| `menuShowAll: true` (default DEV) | Grilla + sidebar **completos** (bypass). |
| `menuShowAll: false` + claims `Permission` | Solo módulos cuyo `menuKey` esté permitido (`RECEPCION`, `menu:AGI`, …). |
| `menuShowAll: false` + `menuDemoAllowedKeys` | Igual, sin JWT de menús (prueba local). |
| Rol `admin_role` / *admin* | Árbol completo aunque `menuShowAll=false`. |
| Sin claims ni demo | **Fail-open** (no vaciar menú hasta Identity oleada C). |

UI (grilla compacta, sidebar, disabled) **no cambia** con el filtro: solo qué módulos aparecen.

**Cuando haya usuario UAT:** exportar perfil → evidence → apagar `menuShowAll` en no-DEV.
