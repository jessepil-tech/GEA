---
title: Pipeline — Administración General (maestros)
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.relevamiento-maestros.pipeline
---

# Pipeline — Administración General

`phase_id:` **`sdd.hospital.relevamiento-maestros.pipeline`**  
Fecha: **2026-09-16**  
Padre: [`README.md`](README.md) · Proceso: [`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md)

Guion de quien **configura el hospital**. Este módulo no tiene punta operativa propia
comparable a una grilla de turnos: el resto del HIS **consume** lo que acá se da de alta.

## Diagrama

```text
Identity / perfil menú ADMINISTRACION_GENERAL_NA
    → Catálogos globales (tipo doc, provincia, nacionalidad, motivo, forma pago)
    → Centro de atención (+ grp / unidad de negocio)
    → Servicio + especialidad + vínculo servicio_centro
    → Personal / paciente / equipo
    → Nomenclador (sección → prestación) + convenio / plan / entidad
    → Parámetros (general, atención, internación, HC, mail/SMS)
         ↓
Consumo: Turnos, Recepción, AGI, Admisión, Lab, Farm, Facturación
         ↓
Hojas “modulos” (101): ABM de cada vertical — viajan con el stream dueño
         ↓
Side-effects: jobs vigencia hab / mail-SMS / padrones; TBL_AUD_* al escribir
```

**Anti-sesgo:** `inicio.xhtml` del tile es lanzador, no el módulo.

Checklist A–E: ninguna etapa en silencio.

- [x] Fase A: pipeline (este archivo) + A2b
- [x] Fase B: pantallas menú + packages tronco + `ts.*` citados
- [x] Fase C: [`maestros.md`](maestros.md)
- [x] `## Profundidad de análisis` en el README: seis capas, ninguna en silencio
- [x] Sin demo de negocio — constancia en README
- [x] Gap destino en [`matriz.md`](matriz.md)
- [x] Fase D: [`cortes.md`](cortes.md)
- [x] Fase E: README

---

## Fases A1–A9

| # | Etapa | Legacy (evidencia) | Destino | Estado |
|---|-------|--------------------|---------|--------|
| A1 | Identidad / personal | ABM `personal` id=10208 `/pages/configuracion/personal/personal`. Duplicado en tile ACREDITACION (`50102`). Matrícula/prescriptor `11212`. Unificación `10244`. | T1 picker/login [`turnos-maestros-personal/`](../../cortes/turnos/turnos-maestros-personal/) **gate parcial**; **ABM catálogo diferido** | **Parcial** |
| A2 | Roles / perfiles | Entrada = `MENU_PERFIL_ACCESO` sobre el árbol 10000. SEGURIDAD (tile aparte, origin no lo ve). Rol funcional: no hay un único `rol_funcional_pers` de “configurador”; cada ABM puede abortar en PL/SQL | Tile Web visible; Identity `GET /menus` M2 pendiente; `menuShowAll` DEV | **Parcial** (shell) |
| A2b | Orientación / gate | Padre `ADMINISTRACION_GENERAL_NA` id=10000 · `ACCION` `/pages/configuracion/inicio`. No es path suelto. Gate centro: varios ABM operativos filtran por centro (servicio_centro, caja, recepción); el lanzador del tile **no** es `inicioRecepcionCentro` | Tile → no hay `/configuracion/inicio`; Convenios va a `/catalogo/convenios`. Hab turnos colgados bajo TURNOS (W6) | **Parcial** — gate por ABM al abrir cada corte (xhtml) |
| A3 | Maestros de negocio | Centro 10203 · servicio 10002 · especialidad 10003 · servicio_centro 10204 · paciente 10241 · convenio 10604 · nomenclador 10402/10403 · recepción 10205 · caja 10206 | DDL padres en SDD Turnos/CU-A (`ts.centro_atencion`, `servicio`, `convenio`, `paciente`, `prestacion`). ABM: **convenios** CU-A | **DDL parcial; ABM no** (salvo convenio + P3 anunciador) |
| A4 | Habilitación / vínculo | `servicio_centro`; `hab_turnos_*` (10811–10813); `personal_servicio` / equipo; `grp_servicio_centro` 10292; `call_center` 10817 | T2 hab **gate-done**; vínculo servicio-centro **sin ABM**; `CheckHabTurnosJob` diferido(jobs) | **Parcial** |
| A5 | Parámetros / reglas | `paramGeneral` 10009 y hermanos (atención, triage, internación, contable, enfermería, HC, autogestión, interface compras). Mail/SMS servers 10016/10017. Motivo 10008 | No hay pantallas de parámetros en Web | **No migrado** |
| A6 | Generación / materialización | **N/A** como núcleo del tile: no crea la oferta del día. Excepciones que **cuelgan del menú 10800**: generar/eliminar agenda (Turnos T4 **gate-done**); carga padrón convenio 10652; interfaces migración 10701–10712 | T4 cobrado en stream Turnos. Padrones / interfaces **no** | **N/A** (núcleo) / **parcial** (hojas prestadas a Turnos) |
| A7 | Operación diaria | Consultas bajo `facturacion/consultas` (10631–10637). No hay “mostrador de maestros” | Ausente | **N/A** al tile; consultas **no migradas** |
| A8 | Ciclo de vida | Unificación persona 10244; desconfirmación/habilitar paciente 10242/10243; auditoría convenio 10606; baja lógica típica HIS en cada ABM | Ausente (salvo lo que CU-A haga en convenio) | **No migrado** |
| A9 | Side-effects | `server_mail` / `server_sms` → jobs `EnvioMailsJob` / `EnvioSmsJob` / `ProcesarSmsJob`. `CheckHabTurnosJob` lee hab. `MigraPersonalV8AV9Job` (¿one-shot?). Escritura auditada `TBL_AUD_*`. BIRT listados RRHH (otra rama de menú) | Jobs **diferido(relevamiento-procesos-programados)**. Auditoría **diferido(auditoria)** si el CU escribe tabla auditada | **Diferido** |

Instalación de referencia: genérica `cliente="TS"` (todas las ramas `esClienteX` apagadas en el WAR del repo).
