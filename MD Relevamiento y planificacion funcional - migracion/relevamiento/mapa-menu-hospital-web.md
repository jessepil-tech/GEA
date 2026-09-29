---
title: Mapa menú Hospital-Web ↔ SDD ↔ legacy
version: 1.5.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.mapa-menu-hospital-web
---

# Mapa — menú Hospital-Web ↔ SDD ↔ legacy

Fuente nav: `Hospital-Web/.../hospital-menu.catalog.ts` + `sidebar-nav.config.ts`
(+ rutas display fuera del shell).

**Árbol completo + viabilidad de replicar menú/iconos legacy:**  
[`arbol-mapeo-menu-legacy-web.md`](arbol-mapeo-menu-legacy-web.md).

**Display TV:** ruta anónima `/display/anunciadores/:id` (API Key). En legacy la sala
es la app Vue con URL propia (TV/kiosk), no un botón de HOSPITAL_2; un “Abrir display”
en ops Web (`window.open`) es útil para demo/DEV y no contradice paridad de sala.

Leyenda estado SDD: **gate-done** · **active** · **starter** (no dominio) · **deuda**.

**Deuda transversal — validaciones:** pantallas migradas **antes de T2** (habilitación
turnos) no tienen inventario BB/MessageBundle (gate v1.4). Registro:
[`deuda-validaciones-pre-hab-turnos.md`](../estado/deuda-validaciones-pre-hab-turnos.md).
Al retocar → aplicar gate o `diferido(slug)`.

## Regla de producto — cuándo suena el anunciador

| Acto | ¿Publica en TV? |
|------|-----------------|
| Confirmar recepción / ticket (AGI o cola) | **No** — solo entra a espera |
| Botón **Llamar** (`/agi/espera` o `/recepcion/cola`) | **Sí** — INSERT `llamar=S` + WS |
| Botón **Llamar** lista atención médica (clínico) | **Sí** — path `ATENCION_MEDICA` (SDD abierto) |
| Tótem AGI legacy | **No** llama; solo ticket |

Paridad: `btnLlamar` / `actLlamar` del puesto, no el alta de recepción.  
SDD: [`cu-clinico-b1-llamar-recepcion/`](../cortes/recepcion/cu-clinico-b1-llamar-recepcion/).

| Menú / ruta | Qué hace hoy | SDD principal | Legacy (path / bean) | Gaps / deuda |
|-------------|--------------|---------------|----------------------|--------------|
| **Dashboard** `/dashboard` | Grilla de módulos PNG (112×112) + filtro `menuShowAll` | [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) · [`arbol-mapeo-menu-legacy-web.md`](arbol-mapeo-menu-legacy-web.md) M1–M2 | `pages/inicio.xhtml` (grilla módulos) | Tile→módulo **gate-done**; Identity `GET /menus` pendiente |
| **Anunciadores** `/anunciadores` | Lista + **Abrir display**; **ABM** → [`anunciador-agi-config-abm/`](../cortes/anunciador/anunciador-agi-config-abm/) | [`piloto-agi-anunciador/`](../cortes/anunciador/piloto-agi-anunciador/) · [`arbol-mapeo-menu-legacy-web.md`](arbol-mapeo-menu-legacy-web.md) | Config HOSPITAL + Node list; icono `anunciador.png` | P3 **active** |
| *(detalle)* `/anunciadores/:id/llamados` | Llamados con login | mismo + [`anunciador-ws/`](../cortes/anunciador/anunciador-ws/) | Vue `Llamados` / Node pacientes | Ciclo vida **gate-done** |
| **Display TV** `/display/anunciadores/:id` *(sin menú)* | Sala: llamados, ocupación, logo/fondo, TTS | [`apagar-anunciador-node/`](../cortes/anunciador/apagar-anunciador-node/) N1–N3 · [`anunciador-ws/`](../cortes/anunciador/anunciador-ws/) · [`ciclo-vida-llamado-anunciador/`](../cortes/anunciador/ciclo-vida-llamado-anunciador/) | `anunciadorVue` Simple/Compuesto | Ciclo gate-done; sync BLOB/lexemas UAT |
| **Cola recepción** `/recepcion/cola` | Cola A: pendientes, últimos, **Llamar** → TV | [`paridad-recepcion-cola/`](../cortes/recepcion/paridad-recepcion-cola/) M1–M3 | `pages/recepcion/recepcionPaciente/cabeceraRecepcion.xhtml` · `bbRecepcionColaEsperaRecep.actLlamar` · popups en `recepcionPaciente.xhtml` | M5 menú acciones; tile no debe aterrizar aquí — [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) |
| **Inicio recepción** `/recepcion/inicio` | Índice del módulo (no gate) | [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) RF-2 | `inicioRecepcionCentro.xhtml` (gate = hijo) | Índice **done**; gate → [`paridad-recepcion-gate/`](../cortes/recepcion/paridad-recepcion-gate/) |
| **Espera ambulatoria** `/recepcion/espera-amb` | Cola B read-model + filtros | [`paridad-recepcion-cola/`](../cortes/recepcion/paridad-recepcion-cola/) M4 | `pages/recepcion/recepcionPaciente/colaEspera.xhtml` · `bbConsultaColaEsperaRecepcion` | Acciones menú M5 pendientes. Llamar de la fila → [`cola-b-llamar/`](../cortes/recepcion/cola-b-llamar/) **gate-done** (Clarify **B**: API; sin botón en esta pantalla) |
| **Lista espera atención médica** `/ambulatoria/espera-atencion` | Cola personal + engranaje **Llamar** → TV (`ATENCION_MEDICA`) | [`cu-llamar-atencion-medica/`](../cortes/recepcion/cu-llamar-atencion-medica/) | `listaEsperaAtencionMedica.xhtml` · `BBListaEsperaAtencionMedica` · `f_set_paciente_anun_cola` | **gate-done** 2026-09-16; norte/Atender/cola servicio `diferido`; writer E1–E2 `cola-b-llamar` |
| **Recepción AGI** `/agi/recepcion` | Identificar, turnos, confirmar ticket (**sin** anunciar) | [`piloto-agi-g1/`](../cortes/recepcion/piloto-agi-g1/) (+ g1-b/c/d) | AGI `termAutoGestCentro/inicio` (tótem; app aparte) | No es paridad full recepción JSF |
| **Espera AGI** `/agi/espera` | Lista post-recepción; **Llamar** → TV; PDF | [`cu-clinico-b-post-recepcion/`](../cortes/recepcion/cu-clinico-b-post-recepcion/) · [`cu-clinico-b1-llamar-recepcion/`](../cortes/recepcion/cu-clinico-b1-llamar-recepcion/) · [`piloto-agi-g1-d-birt/`](../cortes/recepcion/piloto-agi-g1-d-birt/) | Post-AGI + paridad `btnLlamar` puesto | Ciclo vida gate-done |
| **Demanda espontánea** `/agi/demanda-espontanea` | Ingreso sin turno (CU-C) | [`cu-clinico-c-demanda-espontanea/`](../cortes/recepcion/cu-clinico-c-demanda-espontanea/) | Módulo `DEMANDA_ESPONTANEA` · `pages/ambulatoria/demandaEspontanea/*` (p.ej. `listaEsperaDemandaEspontanea.xhtml`) · icono `demandaEspontanea.png` | Ampliar inventario CU |
| **Convenios** `/catalogo/convenios` | ABM convenios | [`cu-clinico-a-catalogo-abm/`](../cortes/recepcion/cu-clinico-a-catalogo-abm/) | `pages/configuracion/convenio/convenio.xhtml` (módulo CONFIGURACION) | Resto catálogo / planes |
| **Centro atención** *(ruta G2)* | ABM `centro_atencion` | [`maestros-m1a-centro/`](../cortes/maestros/maestros-m1a-centro/) | `centroAtencion.xhtml` · 10203 | capa 4; UI no codeada |
| **Servicio / Servicio centro** *(ruta G2)* | ABM catálogo + vínculo | [`maestros-m1b-servicio/`](../cortes/maestros/maestros-m1b-servicio/) | `servicio.xhtml` · `servicioCentro.xhtml` · 10002/10204 | tras M1a |
| **Hab. turnos servicio** `/configuracion/hab-turnos-serv` | Buscar → lista → Agregar/Editar/Eliminar | [`turnos-config-hab-horarios/`](../cortes/turnos/turnos-config-hab-horarios/) · padre menú [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) | `habTurnosServCentro.xhtml` · O1 **15411** TURNOS/Dominios | URL puede quedar; **menú** no es Configuración |
| **Hab. turnos profesional** `/configuracion/hab-turnos-pers` | Buscar → lista → Agregar/Editar/Eliminar | [`turnos-config-hab-horarios/`](../cortes/turnos/turnos-config-hab-horarios/) · padre menú [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) | `habTurnosPersServ.xhtml` · O1 **15412** | gate-done T2 |
| **Hab. turnos equipo** `/configuracion/hab-turnos-equipo` | Buscar equipo → lista → Agregar/Editar/Eliminar | [`turnos-hab-equipo/`](../cortes/turnos/turnos-hab-equipo/) · padre menú TURNOS | `habTurnosEquipoServ.xhtml` · O1 **15413** | gate-done 2026-09-23 D-TUR-12; padre vínculo diferido |
| **Turnos por Equipo** `/configuracion/horarios-turnos-equipo` | Buscar equipo → grupo → prestaciones → horario | [`turnos-horarios-equipo/`](../cortes/turnos/turnos-horarios-equipo/) · padre menú TURNOS | `turnosEquipo.xhtml` · O1 **15416** | **gate-done** 2026-09-23 D-TUR-13 |
| **Turnos por Servicio** `/configuracion/horarios-turnos-serv` | Grupos → prestaciones → horario → días | [`turnos-horarios-grupos/`](../cortes/turnos/turnos-horarios-grupos/) · padre menú TURNOS | `turnosServicios/*` · menú lateral xhtml | URL `/configuracion/…`; **menú** bajo TURNOS (O1) |
| **Turnos por Profesional** `/configuracion/horarios-turnos-pers` | Idem cadena pers | [`turnos-horarios-grupos/`](../cortes/turnos/turnos-horarios-grupos/) · padre menú TURNOS | `turnosPersonal/*` | Igual: menú TURNOS |
| **Generar Agenda Turnos** `/configuracion/grilla-turnos-generar` | Cascada serv/pers → fechas/días → Generar → observaciones | [`turnos-generacion-grilla/`](../cortes/turnos/turnos-generacion-grilla/) · padre menú TURNOS | `generacionGrillaTurnos.xhtml` · `BBGeneracionGrillaTurnos` | **gate-done** T4; equipo D-TUR-17; e2e `grilla-turnos-generar-eliminar.spec.ts` |
| **Eliminar Agenda Turnos** `/configuracion/grilla-turnos-eliminar` | Consultar candidatos → confirmar → eliminar | [`turnos-generacion-grilla/`](../cortes/turnos/turnos-generacion-grilla/) | `eliminarGrillaTurnos.xhtml` · `BBEliminarGrillaTurno` | **gate-done** T4; SMS/mail D-TUR-19 (T7) |
| **Consulta Agendas Generadas** `/configuracion/grilla-turnos-consulta` | Matriz año → drill-down día → turnos → Imprimir PDF | [`turnos-generacion-grilla/`](../cortes/turnos/turnos-generacion-grilla/) · hijos [`turnos-consulta-agendas-drilldown/`](../cortes/turnos/turnos-consulta-agendas-drilldown/) · [`turnos-consulta-agendas-imprimir/`](../cortes/turnos/turnos-consulta-agendas-imprimir/) | `consultaAgendasGeneradas.xhtml` · `BBConsultaAgendasGeneradas` | **gate-done** T4; e2e `grilla-turnos-consulta.spec.ts` |
| **Agenda / Turnero** `/turnos/agenda` | Filtros + calendario + grilla día → reservar/otorgar/liberar/sobreturno; **T5.1:** buscadores + west ficha + info popups + elegibilidad seed + cobros rechazo; **T5.1e:** chrome infoTurno HIS; **persist:** obs + fecha prescripción al otorgar; **prep/req:** GET al abrir; **T5.2:** accordion + popup Sobreturno; **T6.1:** reasignar menú fila; **T5.3:** TURNOS REPETIDOS **gate-done**; **T5.4:** Turnos Múltiples **gate-done**; **T5.4-b:** cambiar horario **gate-done**; **T5.5:** Consulta Agenda **gate-done** (PDF valores **gate-done**; Excel diferido); **T5.6:** Pre-agenda **gate-done** | [`turnos-agenda-otorgar/`](../cortes/turnos/turnos-agenda-otorgar/) · [`turnos-agenda-ficha-paciente/`](../cortes/turnos/turnos-agenda-ficha-paciente/) · [`turnos-agenda-info-popups/`](../cortes/turnos/turnos-agenda-info-popups/) · [`turnos-agenda-elegibilidad-cobros/`](../cortes/turnos/turnos-agenda-elegibilidad-cobros/) · [`turnos-agenda-cobros/`](../cortes/turnos/turnos-agenda-cobros/) · [`turnos-agenda-reasignar/`](../cortes/turnos/turnos-agenda-reasignar/) · [`turnos-agenda-sobreturno/`](../cortes/turnos/turnos-agenda-sobreturno/) · [`turnos-agenda-info-turno/`](../cortes/turnos/turnos-agenda-info-turno/) · [`turnos-agenda-info-turno-persist/`](../cortes/turnos/turnos-agenda-info-turno-persist/) · [`turnos-agenda-info-turno-prest/`](../cortes/turnos/turnos-agenda-info-turno-prest/) · [`turnos-agenda-repetidos/`](../cortes/turnos/turnos-agenda-repetidos/) · [`turnos-agenda-multiples/`](../cortes/turnos/turnos-agenda-multiples/) · [`turnos-agenda-consultas/`](../cortes/turnos/turnos-agenda-consultas/) · [`turnos-agenda-preagenda/`](../cortes/turnos/turnos-agenda-preagenda/) · padre menú TURNOS | `asignacionTurnos/agenda.xhtml` · `consulta.xhtml` · `preAgendaTurnos.xhtml` · `turnosRepetidos.xhtml` · `turnosMultiples.xhtml` · `BBAgenda` · **no** `inicioAgenda` HOS-APP | T5 **gate-done** 2026-09-07; T5.1–T5.6 **gate-done**; e2e `turnos-agenda.spec.ts` |
| **Products** `/products` | Demo starter | — | — | **No hospital** — ocultar |
| **TURNOS** `/turnos/inicio` | Picker call center (T1); tile → aquí | [`turnos-maestros-personal/`](../cortes/turnos/turnos-maestros-personal/) · [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) · [`relevamiento-turnos/`](relevamiento-turnos/) | `inicioTurnos.xhtml` · `BBInicioTurnos` | T4–T5 config+agenda gate-done; ABM personal/CC diferidos |
| **Showcase** `/showcase/*` | Galería UI starter | [`ux-shell-primefaces/`](../cortes/plataforma/ux-shell-primefaces/) | — | Off si `showcaseEnabled=false` |
| **Profile** `/profile` | Perfil usuario | [`identidad-oleada-a/`](../cortes/plataforma/identidad-oleada-a/) | — | Según authMode |

## Auth (fuera del menú ops)

| Ruta | Rol | SDD |
|------|-----|-----|
| `/auth/signin` | Login local Identity | [`identidad-oleada-a/`](../cortes/plataforma/identidad-oleada-a/) |
| `/auth/oidc/*` | OIDC / callback | mismo + docs Identity |

## Cómo mantener este mapa

1. Toda pantalla nueva en sidebar → fila aquí **y** en [`arbol-mapeo-menu-legacy-web.md`](arbol-mapeo-menu-legacy-web.md) §3.2.
2. Todo gap de paridad → columna Gaps con **slug** (`docs/sdd/<slug>/`), no texto suelto.
3. Proceso: [`proceso-sdd-paridad-completa.md`](../canon/proceso-sdd-paridad-completa.md).
