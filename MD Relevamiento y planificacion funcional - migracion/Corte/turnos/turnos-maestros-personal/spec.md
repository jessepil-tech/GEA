---
title: SDD — T1 Turnos · maestros identidad (personal / call center / motivos)
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-27
phase_id: sdd.hospital.turnos-maestros-personal
---

# Spec — T1 Maestros identidad turno

Padre: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) · corte **T1**  
[`cortes.md`](../../../relevamiento/relevamiento-turnos/cortes.md).

## Problema

`ts.turno` (V31) ya referencia `id_personal*`, `id_call_center_otorga`, `id_motivo_*`, pero
**no existen** en Flyway Api `ts.personal`, `ts.call_center`, `ts.personal_call_center`,
`ts.motivo`, `ts.tipo_motivo_df`. El operador de turnos legacy **no entra** al módulo
sin vínculo `PERSONAL_CALL_CENTER` (`BBInicioTurnos`). Identity autentica; no hay puente
a personal hospitalario. AGI sigue viviendo de seed de turnos OTORGADO — no sustituye esto.

## Resultado

1. DDL canónico `ts` en Hospital-Api (nombres/columnas = inventario PG migrado).
2. Seed DEV: al menos un personal operador + call center + vínculo; motivos mínimos
   de tipos usados en turnos (suspende / sobreturno / reemplazo) si existen en catálogo.
3. API (JWT): resolver personal por login; listar call centers del personal; error de
   negocio si no hay vínculo (paridad `USUARIO_SIN_CALL_CENTER`).
4. UI mínima: ruta módulo TURNOS que **solo** selecciona call center (paridad
   `inicioTurnos.xhtml`) y guarda el id en sesión/cliente para cortes T2+.
5. IT + smoke.

## Clarify (bloqueo de implement)

| # | Pregunta | Respuesta este slice |
|---|----------|----------------------|
| 1 | Pipeline config (maestros, permisos, habilitación) | **Cubierto:** personal, call center, vínculo, motivos, gate operador. **Diferido:** `hab_turnos_*` → [`turnos-config-hab-horarios`](../../../relevamiento/relevamiento-turnos/cortes.md) T2; horarios T3; generación T4. Permiso menú fino Identity `GET /menus` → **diferido** `identidad-menus-m2` (M2 árbol). |
| 2 | Happy path | Login Identity → `login_name` = username → call centers → elegir uno → entrar shell módulo (sin agenda). |
| 3 | Ciclo de vida | `personal.estado`; vínculo call center crear/leer. **Sin** DELETE físico. Alta/edición/baja ABM completo → **diferido** `catalogo-abm-personal`. Motivos: seed + GET; ABM → **diferido** `catalogo-abm-motivo`. |
| 4 | Errores / permisos | Sin personal para el login → 403/404 de negocio (no 500). Sin call center → mismo. JWT inválido → 401. |
| 5 | Side-effects | Ninguno (no TV, no PDF, no cola). |
| 6 | Fuera de alcance | Ver § No objetivos — cada ítem con slug o N/A. |

## Inventario de capacidades (este slice)

| Capacidad | Evidencia legacy | ¿Escritura BD? | ¿Realtime? | Después qué | Este slice |
|-----------|------------------|---------------|------------|-------------|------------|
| Gate operador call center | `BBInicioTurnos` · `inicioTurnos.xhtml` · `Configuracion.selectPersonalCallCenterEqual` | Lee vínculo; setea session | No | `inicio.xhtml` módulo | **In scope** (API + UI picker) |
| Catálogo call center | Tabla `CALL_CENTER` | ABM config | No | Gate | **DDL + GET**; ABM **diferido** `catalogo-abm-call-center` |
| Personal hospitalario | Tabla `PERSONAL` (13 cols) | ABM config | No | Hab / grilla / otorga | **DDL + lookup login**; ABM **diferido** `catalogo-abm-personal` |
| Vínculo personal↔call center | `PERSONAL_CALL_CENTER` | INSERT config | No | Gate | **DDL + seed + GET**; ABM **diferido** con call center |
| Admin turnos serv/centro | `PERSONAL_ADM_TUR_SERV_CENTRO` · `BBPersonalAdministraTurno` | INSERT | No | Generación T4 | **DDL** (desbloquea T2/T4); ABM/UI **diferido** T2 |
| Motivos (suspende, etc.) | `MOTIVO` + `TIPO_MOTIVO_DF` | ABM | No | T6 ciclo | **DDL + seed tipos/filas mínimas + GET**; ABM **diferido** `catalogo-abm-motivo` |
| Menú `ATENCION_TURNOS` | Perfil HOSPITAL_2 | N/A | No | Entra módulo | **Parcial:** habilitar hoja Web al picker. Filtro Identity **diferido** `identidad-menus-m2` |
| Agenda / otorgar | `agenda.xhtml` | Sí | No | Recepción | **Diferido** `turnos-agenda-otorgar` (T5) |

## RF de escritura (mínimo)

| RF | Crear | Estado visible | Transición | Fin |
|----|-------|----------------|------------|-----|
| RF-S1 Seed/vínculo DEV | Filas `personal`, `call_center`, `personal_call_center` | Operador puede elegir CC | `personal.estado` (p.ej. activo) | Sin delete; baja ABM diferida |
| RF-S2 (opcional) persistir CC elegido | Dato de sesión cliente (no obligatoriedad de tabla nueva) | Módulo “abierto” con CC | Cambiar CC = volver al picker | Logout limpia |

No inventar tabla `public` ni `*_agi` para el vínculo. Si hace falta persistir CC server-side, reutilizar session existente — **no** nuevo modelo.

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | Flyway: `ts.personal`, `ts.call_center`, `ts.personal_call_center`, `ts.motivo`, `ts.tipo_motivo_df`, `ts.personal_adm_tur_serv_centro` — **mismas columnas** que inventario (`pg_ts_columns.csv`) |
| RF-2 | PK + FKs internas (vínculo → personal y call_center; motivo.tipo_motivo → tipo_motivo_df si aplica) |
| RF-3 | FKs de `ts.turno` a personal / call_center / motivo: **habilitar solo si** seed AGI no queda huérfano; si no, seguir diferidas y anotar en [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md) |
| RF-4 | `GET` personal actual / call centers del usuario (CQRS query; Resource delgado) |
| RF-5 | Error de negocio si `login_name` no mapea o no hay `personal_call_center` |
| RF-6 | UI `/turnos/inicio` (o equivalente): picker CC; tile TURNOS deja de ser `pending()` hacia esa ruta |
| NFR-1 | Capas starter Api (`core` port → application → infra JDBC → presentation) |
| NFR-2 | `ts.persona` (V30) **no** es `ts.personal`; no mezclar |
| NFR-3 | Identity sigue siendo el único IdP; **D-TUR-01:** `username` JWT ↔ `ts.personal.login_name` |

## Decisiones

| Id | Decisión |
|----|----------|
| D-TUR-01 | Mapeo identidad: `Hospital-Identity` username = `ts.personal.login_name` (columna legacy). Sin tabla puente nueva. |
| D-TUR-02 | ABM completo de personal/call center/motivos **fuera** de T1 (slugs catálogo). T1 = DDL + seed + lectura + gate. |
| D-TUR-03 | Paridad de perfiles de menú 1:1 con HOSPITAL_2 **no** es T1 → `identidad-menus-m2`. |

## No objetivos (este slice)

| Ítem | Destino |
|------|---------|
| `hab_turnos_*`, horarios, `f_gen_grilla_turnos` | T2–T4 |
| Agenda, reservar, otorgar | T5 `turnos-agenda-otorgar` |
| Suspender / reemplazo / reasignar | T6 |
| SMS/mail/BIRT turno | T7 |
| ABM HOSPITAL_2 personal (legajo, categoría, etc.) | `catalogo-abm-personal` |
| `GET /menus` Identity | `identidad-menus-m2` |
| Ensanchar AGI / seed eterno OTORGADO | Prohibido como “atajo T1” |

## Paridad / WAIVE

| Superficie | ¿Legacy la tiene? | Decisión |
|------------|-------------------|----------|
| Gate call center al entrar a TURNOS | **Sí** `BBInicioTurnos` | Implementar (RF-4–6). **No WAIVE** |
| ABM personal/call center/motivos | **Sí** módulo CONFIGURACION | **Diferir** slugs catálogo — no WAIVE |
| Árbol menú por perfil | **Sí** | **Diferir** `identidad-menus-m2` |
| Grilla del día | **Sí** | **Diferir** T5 — no es T1 |

## Criterios de aceptación

1. Flyway aplica en Api; `\d ts.personal` (etc.) coincide en **nombres** con inventario (13 / 10 / 4 / 9 / 3 / 5 columnas).
2. IT: usuario seed con vínculo → lista CC; usuario sin vínculo → error de negocio.
3. UI picker visible con JWT; tile TURNOS navega al picker.
4. AGI G1 / colas / anunciador **sin regresión**.
5. `verify-report.md`: ninguna capacidad de la tabla de inventario en silencio.

## Evidencia paths

- `HOSPITAL_2/.../beans/turnos/BBInicioTurnos.java`
- `HOSPITAL_2/WebRoot/pages/turnos/inicioTurnos.xhtml`
- Inventario: `docs/sdd/inventario-ddl-oracle-pg/pg_ts_columns.csv`
- FKs turno: [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md)
