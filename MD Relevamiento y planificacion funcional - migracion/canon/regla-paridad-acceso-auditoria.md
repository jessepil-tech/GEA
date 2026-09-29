---
title: Regla — paridad de acceso y trazabilidad (quién puede y quién hizo)
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.regla-paridad-acceso-auditoria
---

# Acceso y trazabilidad — las dos preguntas con «quién»

Capas 3–4 de [`gobierno-migracion.md`](gobierno-migracion.md).
Complementa [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md) (qué se ve) y
[`regla-paridad-logica-plsql.md`](regla-paridad-logica-plsql.md) (qué se calcula) con lo que
ninguna de las dos cubre: **quién tiene derecho a hacerlo** y **qué queda registrado**.

**Problema que resuelve:** una pantalla puede pasar los cuatro ejes del gate UI, tener su
golden master verde y su medición de volumen, y quedar accesible para quien no debe verla.
El gate actual no pregunta por el sujeto. En un sistema con historia clínica eso no es una
omisión de configuración.

## Por qué no alcanza con mirar las pantallas

El legacy **no** controla el acceso en la pantalla: apenas 13 `.xhtml` chequean permiso y hay
10 usos de `tienePermiso(`. El control vive en otros dos lugares, ninguno visible desde el
`.xhtml`:

1. **El menú por perfil.** La seguridad es una **aplicación aparte** (`seguridad/frontend/…`,
   `BBMenuPerfilAcceso`), con `perfil_acceso`, `rol_acceso` y `menu_rol_acceso`: el perfil
   decide **qué entradas de menú existen** para ese usuario. Si la plataforma nueva publica
   la ruta, la pantalla es alcanzable aunque el legacy no la mostrara.
2. **El rol funcional, dentro del PL/SQL.** `ts.rol_funcional_pers` se consulta en
   `ATENCION`, `INDICACION`, `INDICACION_MEDICA`, `INFORMES` y `PERSONAS` (37 usos en los
   *bodies* capturados), y **aborta la operación** cuando el rol falta:

```sql
select count(*) from ts.rol_funcional_pers r
 where r.id_personal = ln_id_personal
   and r.rol_funcional = 'CONFIRMA_INFORMES';
if (ln_aux = 0) then
   Raise_application_error(-20000, 'Solo el personal que realizo el informe o que
                                    confirma informes puede confirmar el informe.');
end if;
```

Eso significa tres cosas: la autorización es **regla de negocio**, no configuración; el
**mensaje de error es contrato** (igual que en [`regla-paridad-logica-plsql.md`](regla-paridad-logica-plsql.md));
y un servicio nuevo que no replique el chequeo deja que **cualquiera confirme un informe
clínico**, sin que ningún test funcional de camino feliz lo note.

## Trazabilidad: 264 tablas auditadas por trigger

El legacy tiene **264 packages `TBL_AUD_*`** que registran los cambios **campo por campo**,
con procedimientos por tipo de dato y valor viejo/valor nuevo:

> **Los dos números, medidos (15-sep-2026).** Hay **1.419 triggers `TAUD_*`** en el legacy y no
> todos auditan lo mismo: **264 invocan un package `TBL_AUD_*`** —uno por tabla, relación 1 a
> 1, de ahí que «264 packages» y «264 tablas con historial» sean el mismo número— y los otros
> **1.155 solo estampan `fecha_last_update` y `actualizado_por`**, que es sello de último
> cambio y no historial. Verificado leyendo los triggers: `taud_CAMA_INTERNACION` llama al
> package campo por campo; `taud_AJUSTE_DET_INDICA_NUTR` solo hace
> `:new.fecha_last_update := sysdate`.
>
> Consecuencia para un corte: si escribe en una de las **264**, el destino debe registrar el
> historial o declarar `diferido(auditoria)`. Si escribe en una de las 1.155, alcanza con
> preservar los dos campos de sello. Y una cifra abierta: el dossier estimó «83 GB en 283
> tablas», que no se puede reconciliar con los artefactos locales; se resuelve contra la copia
> Oracle ([`tools/consultas-relevamiento.sql`](../../tools/consultas-relevamiento.sql) § 6).

```sql
procedure aud_ROL_FUNCIONAL_PERS_V (ID_PERSONAL NUMBER, ROL_FUNCIONAL VARCHAR2,
        as_campo varchar2, as_tipo varchar2, an_valor_old VARCHAR2, an_valor_new VARCHAR2)
```

No es un `actualizado_por` en la fila: es historial de modificaciones. Si el corte escribe en
una tabla auditada y la plataforma nueva no registra nada, **se pierde trazabilidad que hoy
existe**, y se pierde en silencio: nada falla.

## Principio

> Cada CU declara **quién puede ejecutarlo** y **qué queda registrado**, y lo prueba con un
> actor sin derecho. Un PASS logrado con el usuario administrador no dice nada sobre acceso.

## Qué exige el gate

En el `spec.md` (paso 3) y en el ledger del `verify-report.md` (paso 6):

| Campo | Contenido |
|-------|-----------|
| Entrada de menú | Perfiles que la ven en el legacy, y el equivalente en el destino |
| Autorización de negocio | Roles funcionales que el PL/SQL exige (`rol_funcional = '…'`), con la firma donde se valida |
| Prueba negativa | Un actor **sin** el rol intenta la operación: se rechaza, y con el mensaje del legacy |
| Auditoría | Si la tabla escrita tiene `TBL_AUD_*`: qué registra el destino, o `diferido(auditoria)` con el riesgo escrito |

La prueba negativa es la parte que no se puede narrar: exige el intento y su rechazo, como
cualquier fila del ledger ([`regla-evidencia-ejecutable.md`](regla-evidencia-ejecutable.md)).

## Estado del destino (para no diseñar en el aire)

`Hospital-Identity` tiene hoy `user_claims` / `role_claims` con `claim_type = 'Permission'`, y
`users.subject_type` + `legacy_id_personal` como puente al personal del legacy (V5–V6). Es
una base razonable, pero **no** modela lo del legacy: no hay perfil→menú ni rol funcional del
personal. Mientras no exista, un corte no puede afirmar paridad de acceso: declara
`diferido(acceso)` con el perfil y el rol que quedaron sin replicar.

Cómo se cierra ese hueco —mapeo tabla por tabla, qué queda fuera de Identity y los dos
defectos de autorización abiertos hoy en `Hospital-Web` (filtro de menú **fail-open** y
cualquier rol que contenga «admin» tratado como acceso total)—:
[`destino-seguridad-identity.md`](../planificacion/destino-seguridad-identity.md).

## No hacer

- Cerrar un gate probando solo con un usuario con todos los permisos.
- Tratar el rol funcional como configuración de UI: es una precondición de negocio que el
  legacy verifica en la base y que aborta la operación.
- Cambiar el mensaje de rechazo: el texto del `Raise_application_error` es lo que el usuario
  conoce y lo que las capturas de soporte muestran.
- Publicar una ruta en el destino porque «la pantalla ya está migrada», sin mirar qué perfiles
  la veían.
- Dar por hecha la auditoría porque la tabla tiene `fecha_last_update` y `actualizado_por`:
  eso es el último cambio, no el historial.
