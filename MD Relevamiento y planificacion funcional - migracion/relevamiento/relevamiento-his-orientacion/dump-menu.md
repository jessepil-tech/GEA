---
title: Dump MENU_APLICACION (O1)
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-08-31
phase_id: sdd.hospital.relevamiento-his-orientacion.o1
---

# Dump `MENU_APLICACION` (O1)

Captura **solo lectura** vía JDBC `ojdbc7` (Oracle 11.2 no admite python-oracledb thin). Forward VPN: host `127.0.0.1:1521` (`Hospital-Infra/vpn`). Sin passwords, sin documentos ni nombres de personal.

| | |
|--|--|
| Fecha UTC | 2026-08-31T19:48:28.237536Z |
| Banner | Oracle Database 11g Release 11.2.0.4.0 - 64bit Production |
| JDBC (sin credenciales) | `jdbc:oracle:thin:@127.0.0.1:1521:HOSPROD` usuario `ts` schema `TS` |
| `ID_APLICACION` HOSPITAL | **2** (home `BBModulos` usa `2L`; confirmado en `dump-aplicacion.csv`) |
| Filas catálogo | 1240 |
| Login origin | `origin` — módulos SP: **37** — nodos `f_get_menu_acceso_all`: **1199** |
| Recorte admin | Perfil **ADMINISTRADOR GENERAL SISTEMAS** (`ID_PERFIL_ACCESO=1`) vía una cuenta que lo tiene (login **no versionado**). Módulos SP: **39** — nodos: **1201** |

## Cómo se obtuvo

1. `SELECT` de `TS.APLICACION` + `TS.MENU_APLICACION` (árbol completo).
2. `TS.SEGURIDAD.f_get_menu_acceso(id_aplicacion, NULL, id_personal)` = tiles de `inicio.xhtml`.
3. Por cada tile: `f_get_menu_acceso_all(id_modulo, id_personal)` = menú horizontal (como `MenuBuilder`).
4. Admin = cuenta con perfil `ADMINISTRADOR GENERAL SISTEMAS` (no se versiona el `LOGIN_NAME`).

CSV: `dump-aplicacion.csv`, `dump-menu-aplicacion.csv`, `dump-menu-origin.csv`, `dump-menu-admin.csv`, `dump-menu-origin-modulos.csv`, `dump-menu-admin-modulos.csv`.
Árboles: `dump-menu-arbol-hospital.md`, `dump-menu-arbol-origin.md`, `dump-menu-arbol-admin.md`.

## Perfiles (solo id + descripción)

### origin

| id_perfil_acceso | perfil_acceso |
|------------------|---------------|
| 67 | PERFIL ORIGIN |

### admin (perfil usado para el recorte)

| id_perfil_acceso | perfil_acceso |
|------------------|---------------|
| 1 | ADMINISTRADOR GENERAL SISTEMAS |

## Módulos origin (tiles SP)

| id | descripcion | nro_orden | accion |
|----|-------------|-----------|--------|
| 50000 | ACREDITACION_PROFESIONALES | 6 | `/pages/recursosHumanos/inicio` |
| 10000 | ADMINISTRACION_GENERAL_NA | 8 | `/pages/configuracion/inicio` |
| 90000 | ADMISION_INTERNADOS | 12 | `/pages/admisionInternados/inicioCentroPuestoAdmision` |
| 40000 | ANALISIS_DEBITOS | 16 | `/pages/analisisDebitos/inicioAnalisisDebitos` |
| 170000 | AREA_ORGANIZACIONAL | 17 | `/pages/areaOrganizacional/inicioAreaOrganizacional` |
| 75000 | ATENCION_MEDICA | 24 | `/pages/ambulatoria/ambulatoria/inicioServicioCentro` |
| 224000 | ATENCION_MEDICA_DOMICILIARIA | 25 | `/pages/ambulatoria/domiciliaria/inicio` |
| 15000 | ATENCION_TURNOS | 128 | `/pages/turnos/inicioTurnos` |
| 25000 | CAJA | 28 | `/pages/caja/inicioCaja` |
| 96000 | CENTRO_PROCEDIMIENTO | 24 | `/pages/cirugia/inicioServicioCentro` |
| 30000 | COBRANZA_CONVENIO | 28 | `/pages/cobranzaConvenio/inicioCobranzas` |
| 55000 | COMPRAS | 32 | `/pages/compras/inicioCentroCompras` |
| 86000 | DEMANDA_ESPONTANEA | 40 | `/pages/ambulatoria/demandaEspontanea/inicioServicioCentro` |
| 45000 | DEPOSITO | 42 | `/pages/farmacia/inicioFarmacia` |
| 60000 | DIAGNOSTICO_POR_IMAGENES | 44 | `/pages/ambulatoria/imagenologia/inicioServicioCentroImag` |
| 87000 | ENFERMERIA_AMBULATORIA | 48 | `/pages/ambulatoria/enfermeria/inicioServicioCentro` |
| 100000 | ENFERMERIA_INTERNADOS | 52 | `/pages/enfermeria/inicioEnfermeriaPiso` |
| 180000 | ESTADISTICAS | 54 | `/pages/estadisticas/inicio` |
| 35000 | FACTURACION_AMBULATORIA | 56 | `/pages/facturacion/inicioFacturacion` |
| 94000 | FACTURACION_INTERNADO | 60 | `/pages/facturacionInternado/inicioFacturacionInternado` |
| 223000 | GUARDIA_Y_EMERGENCIAS | 40 | `/pages/guardiaYEmergencias/inicioGuardiaYEmergencias` |
| 88000 | HISTORIA_CLINICA | 64 | `/pages/historiaClinica/historiaClinica` |
| 220000 | HOSPITAL_DE_DIA | 120 | `/pages/centroProc/inicio` |
| 88500 | HOUSEKEEPING | 68 | `/pages/housekeeping/inicioHousekeeping` |
| 16000 | INCIDENTES | 68 | `/pages/incidentes/inicio` |
| 120000 | INFECTOLOGIA | 72 | `/pages/infectologia/inicioInfectologia` |
| 80000 | INFORMES | 76 | `/pages/informesCompaginacion/inicio` |
| 89000 | INTERNACION | 80 | `/pages/internacion/inicioServicioCentro` |
| 85000 | LABORATORIO | 84 | `/pages/laboratorio/laboratorio/inicioServicioCentroLab` |
| 14000 | LIQUIDACION_HONORARIO | 88 | `/pages/liquidacionHonorario/inicio` |
| 130000 | NUTRICION | 92 | `/pages/nutricion/inicioNutricion` |
| 150200 | OFTALMOLOGIA | 100 | `/pages/ambulatoria/oftalmologia/inicioServicioCentroOftalmologia` |
| 95000 | OTROS_ESTUDIOS | 104 | `/pages/ambulatoria/otrosEstudios/inicioServicioCentroOtros` |
| 101000 | PANEL_DE_CONTROL | 108 | `/pages/panelDeControl/inicio` |
| 20000 | RECEPCION | 112 | `/pages/recepcion/inicioRecepcionCentro` |
| 140200 | SERVICIO | 124 | `/pages/servicio/inicioServicioCentro` |
| 225000 | TERAPIA FISICA | 100 | `/pages/ambulatoria/terapiaFisica/inicio` |

## Módulos admin (tiles SP)

| id | descripcion | nro_orden | accion |
|----|-------------|-----------|--------|
| 50000 | ACREDITACION_PROFESIONALES | 6 | `/pages/recursosHumanos/inicio` |
| 10000 | ADMINISTRACION_GENERAL_NA | 8 | `/pages/configuracion/inicio` |
| 90000 | ADMISION_INTERNADOS | 12 | `/pages/admisionInternados/inicioCentroPuestoAdmision` |
| 40000 | ANALISIS_DEBITOS | 16 | `/pages/analisisDebitos/inicioAnalisisDebitos` |
| 170000 | AREA_ORGANIZACIONAL | 17 | `/pages/areaOrganizacional/inicioAreaOrganizacional` |
| 75000 | ATENCION_MEDICA | 24 | `/pages/ambulatoria/ambulatoria/inicioServicioCentro` |
| 224000 | ATENCION_MEDICA_DOMICILIARIA | 25 | `/pages/ambulatoria/domiciliaria/inicio` |
| 15000 | ATENCION_TURNOS | 128 | `/pages/turnos/inicioTurnos` |
| 25000 | CAJA | 28 | `/pages/caja/inicioCaja` |
| 96000 | CENTRO_PROCEDIMIENTO | 24 | `/pages/cirugia/inicioServicioCentro` |
| 30000 | COBRANZA_CONVENIO | 28 | `/pages/cobranzaConvenio/inicioCobranzas` |
| 55000 | COMPRAS | 32 | `/pages/compras/inicioCentroCompras` |
| 65000 | CRM | 36 | `/pages/crm/inicioCrm` |
| 86000 | DEMANDA_ESPONTANEA | 40 | `/pages/ambulatoria/demandaEspontanea/inicioServicioCentro` |
| 45000 | DEPOSITO | 42 | `/pages/farmacia/inicioFarmacia` |
| 60000 | DIAGNOSTICO_POR_IMAGENES | 44 | `/pages/ambulatoria/imagenologia/inicioServicioCentroImag` |
| 87000 | ENFERMERIA_AMBULATORIA | 48 | `/pages/ambulatoria/enfermeria/inicioServicioCentro` |
| 100000 | ENFERMERIA_INTERNADOS | 52 | `/pages/enfermeria/inicioEnfermeriaPiso` |
| 180000 | ESTADISTICAS | 54 | `/pages/estadisticas/inicio` |
| 35000 | FACTURACION_AMBULATORIA | 56 | `/pages/facturacion/inicioFacturacion` |
| 94000 | FACTURACION_INTERNADO | 60 | `/pages/facturacionInternado/inicioFacturacionInternado` |
| 223000 | GUARDIA_Y_EMERGENCIAS | 40 | `/pages/guardiaYEmergencias/inicioGuardiaYEmergencias` |
| 88000 | HISTORIA_CLINICA | 64 | `/pages/historiaClinica/historiaClinica` |
| 220000 | HOSPITAL_DE_DIA | 120 | `/pages/centroProc/inicio` |
| 88500 | HOUSEKEEPING | 68 | `/pages/housekeeping/inicioHousekeeping` |
| 16000 | INCIDENTES | 68 | `/pages/incidentes/inicio` |
| 120000 | INFECTOLOGIA | 72 | `/pages/infectologia/inicioInfectologia` |
| 80000 | INFORMES | 76 | `/pages/informesCompaginacion/inicio` |
| 89000 | INTERNACION | 80 | `/pages/internacion/inicioServicioCentro` |
| 85000 | LABORATORIO | 84 | `/pages/laboratorio/laboratorio/inicioServicioCentroLab` |
| 14000 | LIQUIDACION_HONORARIO | 88 | `/pages/liquidacionHonorario/inicio` |
| 130000 | NUTRICION | 92 | `/pages/nutricion/inicioNutricion` |
| 150200 | OFTALMOLOGIA | 100 | `/pages/ambulatoria/oftalmologia/inicioServicioCentroOftalmologia` |
| 95000 | OTROS_ESTUDIOS | 104 | `/pages/ambulatoria/otrosEstudios/inicioServicioCentroOtros` |
| 101000 | PANEL_DE_CONTROL | 108 | `/pages/panelDeControl/inicio` |
| 20000 | RECEPCION | 112 | `/pages/recepcion/inicioRecepcionCentro` |
| 70000 | SEGURIDAD | 120 | `/pages/seguridad/inicioSeguridad` |
| 140200 | SERVICIO | 124 | `/pages/servicio/inicioServicioCentro` |
| 225000 | TERAPIA FISICA | 100 | `/pages/ambulatoria/terapiaFisica/inicio` |

`DESCRIPCION` del dump es la **clave** (`ATENCION_TURNOS`, `ADMINISTRACION_GENERAL_NA`). La grilla PNG usa otra etiqueta (`TURNOS`, `ADMINISTRACION GENERAL`) vía `Resources.getValue` + ícono.

## `habTurnosServCentro` (padre real en catálogo)

La misma `ACCION` está **dos veces** (ítems distintos). Origin tiene ambas.

| id | descripcion | cadena de padres | accion |
|----|-------------|------------------|--------|
| 10811 | hab_turnos_servicio | `ADMINISTRACION_GENERAL_NA` → modulos → turnos → configuracion (`10803`) | `/pages/configuracion/servicio/habTurnosServCentro` |
| 15411 | hab_turnos_servicio | `ATENCION_TURNOS` → dominios → configuracion (`15403`) | `/pages/configuracion/servicio/habTurnosServCentro` |

Paridad Web: la hoja de **TURNOS / Dominios** es **15411**. El path xhtml `pages/configuracion/servicio/` no define el padre.

## Tiles solo en admin (no en origin)

| id | descripcion | accion |
|----|-------------|--------|
| 65000 | CRM | `/pages/crm/inicioCrm` |
| 70000 | SEGURIDAD | `/pages/seguridad/inicioSeguridad` |
