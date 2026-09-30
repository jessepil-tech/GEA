---
title: Paciente-Web — portal (sucesor HOS-APP paciente)
description: Front Angular para cuenta web de paciente. Staff sigue en Hospital-Web.
version: 0.1.0
status: deferred
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.paciente-web
---

# Paciente-Web (portal)

**Estado:** **diferido** — fila en [`backlog-orden-2026-08-14.md`](../../../planificacion/backlog-orden-2026-08-14.md).  
**No** abrir spec de implementación hasta relevamiento A–C.  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

## Decisión de producto (2026-09-08)

| Audiencia | Destino |
|-----------|---------|
| Personal / staff (turnero, HIS) | **Hospital-Web** — T5 `/turnos/agenda`; no clonar HOS-APP.war |
| Paciente (cuenta web) | **Paciente-Web** — Angular aparte, misma Hospital-API + **mismo Hospital-Identity** |

**No** fusionar el portal en el shell clínico. **No** extraer AGI de Web por este corte (kiosco ≠ cuenta paciente).

## Qué cubre (v1 propuesto, Clarify pendiente)

Superficie **paciente** de HOS-APP: registro/validar cuenta, login, recuperar clave, perfil, wizard `step0`–`step4`, cancelar, próximos turnos.  
Hijos posteriores: reclamo, contacto, informes, recetas.

Staff que hoy entra a HOS-APP por SSO (`nuevoturno`) **no** es este producto.

## Identity (mismo servicio)

Reusar **Hospital-Identity**. No un segundo IdP.

| Staff (oleada A) | Paciente (este corte) |
|------------------|------------------------|
| `subjectType=PERSONAL` | `subjectType=PACIENTE` (extender; hoy el `/me` asume PERSONAL) |
| `legacy.idPersonal` | `legacy.idPaciente` |
| Perfil `MENU_APLICACION` | Permisos de **portal** (ver/reservar/cancelar **propios**), no menú HIS |
| Usuario Oracle / login personal | `ts.paciente.contrasena_web` + mail + `cuenta_web_validada` (~128k cuentas) |

Roles sí; el menú HIS **no** aplica. El JWT debe atar al `id_paciente`, no al legajo.

## Prerrequisitos para abrir capa 3

- T5.1 (ficha agenda staff) no bloquea el relevamiento, sí el “paciente reserva oferta viva”.  
- T6 ciclo de vida / canal `WEB` (`login_tur_web`, `medio_sol_turno`) en Clarify.  
- No pisa el carril P3 anunciador ni E2E mostrador.

## Siguiente

`docs/sdd/relevamiento-paciente-web/` (pipeline + maestros) **antes** de `spec.md`.
