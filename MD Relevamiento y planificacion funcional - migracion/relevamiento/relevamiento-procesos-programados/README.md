---
title: Relevamiento — procesos programados (jobs Quartz) del HIS legacy
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.relevamiento-procesos-programados
---

# Procesos programados — las capacidades que ninguna pantalla muestra

Capa 3b′ de [`gobierno-migracion.md`](../../canon/gobierno-migracion.md).

**Qué es:** el inventario de los **82 jobs** del HIS (67 en `SCHEDULER` y 15 repartidos en
otros nueve proyectos) y el cruce con los circuitos que el programa ya declaró cerrados.

**Por qué existe:** el relevamiento A–C parte del **menú y las pantallas**. Un proceso
nocturno no cuelga de ninguna hoja del árbol de módulos, así que el método vigente **no
puede verlo**, y la matriz anti-gap tampoco: no hay pantalla desde donde preguntar por él.

Detalle job por job: [`inventario.md`](inventario.md).

> **Alcance (2026-09-15):** el cliente dejó `SCHEDULER` **fuera** de los proyectos a migrar
> ([`alcance-proyectos-migracion.md`](../../planificacion/alcance-proyectos-migracion.md)). Eso no vacía este
> relevamiento: los jobs son cáscaras finas y la lógica vive en PL/SQL y en
> `HOSPITAL-BUSINESS`, que **sí** está dentro de alcance. Lo que la exclusión deja pendiente
> es el **disparador**, y con él las capacidades de la sección siguiente. El proyecto sale del
> alcance; las capacidades no salen del hospital.

## El hallazgo

**Tres circuitos declarados `gate-done` están incompletos**, y no por un gap de pantalla:
porque parte de su comportamiento vive en jobs que nadie relevó. Todo lo que sigue está
verificado contra el PL/SQL real capturado de Oracle
(`Hospital-Legacy/tools/relevamiento/out/plsql/package_body/`, 326 *bodies*), no inferido
de nombres de clase.

### 1. `MigrarTurnoVencidoJob` — toca tres circuitos a la vez

El nombre engaña: `TS.TURNOS.f_migra_turno_vencido()` no archiva turnos solamente. Lo
primero que ejecuta, antes de tocar un turno, es el barrido de la cola y del anunciador:

| Sentencia | Efecto |
|-----------|--------|
| `update ts.cola_espera_recep set llamado='S' where llamado='N' and fecha_hora_ingreso_cola < sysdate-6/24` | Purga de la cola de recepción a las 6 h |
| `update ts.cola_espera_triage …` (ídem) | Purga de la cola de triage a las 6 h |
| `update ts.llamado_anunciador set llamar='N' where llamar='S' and fecha_hora_llamado < sysdate-1/24` | Apaga los llamados del anunciador a la hora |

Por qué es determinante: en `TS.RECEPCIONES.f_llamar_prox_nro_recep` el próximo paciente se
elige con `col.llamado = 'N'`. Es decir, **`llamado='N'` *es* la cola de pendientes del
mostrador**. Y del lado del tótem, `TS.ANUNCIADORES` consume `llamar='S'` para mostrar y lo
apaga después.

Consecuencias si el job no existe en la plataforma nueva:

- **Mostrador / recepción** (`colaEspera.xhtml`, `cabeceraRecepcion.xhtml`): los pacientes
  que entraron y se fueron sin ser llamados quedan en `llamado='N'` **para siempre**. La
  cola nunca se limpia entre jornadas y el sistema sigue ofreciendo como «próximo» a gente
  que ya no está. Se nota el primer día.
- **Anunciador / tótem AGI**: los llamados quedan en `llamar='S'` permanentemente y la
  pantalla de TV sigue anunciando pacientes de horas antes.
- **Turnos**: archiva el turno vencido, copia `mensaje_turno` a `mensaje_turno_vencido`,
  borra de `ts.turno`, y para los turnos `OTORGADO` cuya habilitación dejó de estar vigente
  **genera el mail y el SMS de reprogramación**. Ese aviso al paciente **nace en el job**,
  no en una pantalla. Además desvincula `cola_espera_serv_amb`, `det_indica_prest_int` y
  `atencion_int` de los turnos borrados, y repunta `sms_recibido` al turno vencido.

Nota aparte: el mismo procedimiento ejecuta `alter system disconnect session` para matar
sesiones Oracle viejas. Eso **no** se porta: es un parche de infraestructura del legacy.

### 2. `CheckHabTurnosJob` — sostiene la habilitación de turnos

`TS.TURNOS.f_check_hab_turnos()` apaga y recalcula `vigente` en las tres tablas de
habilitación (`hab_turnos_serv_centro`, `hab_turnos_pers_serv`, `hab_turnos_equipo_serv`) y
luego `atiende_turnos` en `servicio_centro`, `personal_servicio` y `equipo_serv_centro`.

La pantalla graba las fechas de vigencia; **el flag que el resto del sistema consulta lo
recalcula solamente este job**. Sin él, una habilitación cargada hoy con vigencia desde
mañana nunca se activa, y una vencida ayer sigue activa. El síntoma aparece un día después
de la carga, lo que lo vuelve difícil de atribuir a su causa.

### 3. Confirmación **y cancelación** de turno por SMS

`TS.ENVIO_MAIL_SMS.f_procesar_sms()` (que corre `ProcesarSmsJob`) cierra un circuito
completo de paciente que no tiene pantalla alguna:

- Si el paciente responde `SI…` a un `RECORDATORIO` y el turno está `OTORGADO`:
  `update ts.turno set confirmado_por_sms = 'S'`.
- Si responde para cancelar: llama a `ts.turnos.f_libera_turno_pac(…, 'SMS', …)`, o sea
  **libera el turno**, y dispara el SMS de cancelación configurado en el centro de atención.

Esto es una **capacidad de negocio del circuito de Turnos**, con escritura sobre `ts.turno`,
que ningún inventario por pantalla puede descubrir. El canal de entrada lo alimenta
`RecepcionSmsJob`, que inserta en `ts.sms_recibido`.

### Circuito que sale limpio

**Catálogo de convenios y elegibilidad**: ningún job lo mantiene ni calcula elegibilidad. La
única relación es de arrastre (el turno vencido copia `id_convenio`). Los jobs de AFIP,
Datatech y liquidación son facturación, no convenios.

## Falsos positivos que conviene conocer

Dos jobs que por nombre parecían tocar el mostrador y **no lo hacen**:

| Job | Por qué se descarta |
|-----|---------------------|
| `AtencionAutomaticaColaEsperaAmbJob` | Todo su cuerpo está dentro de un `if` por cliente específico, y termina en un *named query* que **no está definido** en ningún mapeo del repositorio |
| `GeneracionOrdenLiqHonEntidadJob` | Es un no-op: la única línea de negocio está comentada. Abre conexión, loguea y cierra |

Y uno con matiz favorable: `GeneracionOcupacionAmbienteAmbJob` genera la ocupación de
ambientes del día siguiente, pero **existe la pantalla** `generarOcupacionAmbiente.xhtml`
para dispararlo a mano. Lo que se pierde sin el job no es la capacidad: es la
automatización. Se cierra declarándolo como requisito operativo.

## Lo que no se puede saber leyendo código

**La frecuencia de 65 de los 67 jobs no está en el repositorio.** No hay XML ni
`.properties` de Quartz: `SchedulerControllerJob` lee `TS.TAREA_PROGRAMADA` y arma cada
trigger con `INTERVALO`, `INTERVALO_MINUTOS`, `HORA_INICIO` e `INTERVALO_DIA`. Solo dos
frecuencias están en código (el controlador cada 60 minutos, el borrado de temporales cada
4 horas).

Más importante: esa tabla tiene una columna **`ACTIVA`**, y el job solo se programa si está
en verdadero. Por lo tanto **no se sabe cuáles de los 67 corren hoy en producción** leyendo el repo.
La captura de [`dump-tarea-programada.tsv`](dump-tarea-programada.tsv) responde para esta
copia (abajo). Eso corta en los dos sentidos: puede reducir el alcance real, o puede haber
jobs activos con frecuencias que nadie recuerda haber configurado.

Una sola consulta cierra las dos preguntas:

```sql
select clase, activa, intervalo, intervalo_minutos, hora_inicio, intervalo_dia
  from ts.tarea_programada
 where aplicacion = 'SCHEDULER'
 order by activa desc, clase;
```

La consulta ya es ejecutable: la copia Oracle es **copia fiel de producción** (declarado por
el cliente, 2026-09-15) y está escrita en
[`tools/consultas-relevamiento.sql`](../../../tools/consultas-relevamiento.sql) § 1. La
evidencia se registra con la nota «copia de producción; se asume equivalencia con los datos
productivos» —afirmación del cliente, no medición nuestra— y se lee sin mutar el oráculo.

El dump de HOSPROD ya respondió frecuencia y `ACTIVA` **en la copia**. Hasta confirmar
prod, **ningún disparador Quartz entra a un corte** (no se inventa el schedule). Las
capacidades overnight se declaran `diferido(relevamiento-procesos-programados)` con la
premisa de que en el hospital **sí** corren.

## Captura HOSPROD 2026-09-16

JDBC `127.0.0.1:1521` SID `HOSPROD` por el contenedor Forti `hospital-infra-vpn`
(Oracle 11.2.0.4). Copia de producción según el cliente; no se mutó el oráculo.
Artefactos: [`dump-tarea-programada.tsv`](dump-tarea-programada.tsv) ·
[`dump-tarea-programada-resumen.tsv`](dump-tarea-programada-resumen.tsv) ·
[`dump-equipo-tarea-programada.tsv`](dump-equipo-tarea-programada.tsv) ·
[`dump-meta.md`](dump-meta.md).

| Hallazgo | Dato |
|----------|------|
| Filas en `ts.tarea_programada` | **50**, todas `aplicacion=SCHEDULER` |
| `ACTIVA` | **N en las 50** |
| `CheckHabTurnosJob` | `N` · `MINUTOS` / 60 (si se encendiera) |
| `MigrarTurnoVencidoJob` | `N` · `MINUTOS` / 41 |
| `ProcesarSmsJob` / `RecepcionSmsJob` / `EnvioSmsJob` | `N` |
| `LlamadorAnunciadorJob` (AGI) | **no está** en la tabla |
| `ts.equipo_tarea_programada` | 1 fila `SCHEDULER` |

El inventario de código lista 67 jobs en el proyecto `SCHEDULER`; **17 no tienen fila**
aquí (nunca se programaron en esta instalación, o se borraron).

**Qué no cierra esta captura:** HOSPROD es entorno **no productivo**. Hasta que
producto confirme contra prod, se asume que `ACTIVA=N` es de la copia (no hace
falta que corran jobs ahí) y que **en el hospital sí están encendidos**. No se
toma este dump como prueba de que producción los apagó. A1 sigue abierto: traer
el `ACTIVA` de prod o una captura que lo distinga.

## El hallazgo de método

`SCHEDULER` no es el único proyecto con jobs. Hay 15 más en otros nueve:

| Proyecto | Jobs | Relevancia |
|----------|-----:|------------|
| `ANMAT` | 6 | Trazabilidad (ver [`relevamiento-integraciones-externas/`](../relevamiento-integraciones-externas/)) |
| `AGI` | 2 | **`LlamadorAnunciadorJob`** — el tótem tiene lógica programada propia |
| `AGH` · `AGP` · `HOS-APP` · `HOSPITAL_2` · `WS-HOSPITAL` · `RECETAS` · `PROVEEDORES` | 1 cada uno | A relevar con su circuito |

El `LlamadorAnunciadorJob` de AGI es directamente relevante: **el circuito del anunciador
está declarado `gate-done` y tiene un job propio que este inventario no cubre.**

## Consecuencia para el proceso

Un circuito no está cerrado por tener sus pantallas migradas. Antes del `gate-done` hay que
preguntar: **¿qué corre solo en este circuito?** La pregunta ahora es parte del paso 3 del
[loop](../../canon/loop-migracion-corte.md) y el índice del legacy la responde con
`./tools/indice-legacy.sh --jobs <dominio>`.

## Próximo paso

1. ~~Consulta a `ts.tarea_programada`~~ — **hecho** 2026-09-16 (HOSPROD, 50× `ACTIVA=N`).
   Premisa de trabajo: apagados en la copia no productiva; **prod se asume encendido**
   hasta confirmación. No inventar disparador ni cerrar A1 con este dump.
2. No abrir slug de disparador ni tratar las cuatro capacidades como apagadas en el
   hospital. El E2E diurno las deja `diferido(relevamiento-procesos-programados)`. A1 =
   confirmar `ACTIVA` en prod (no con este dump).
3. `LlamadorAnunciadorJob` no figura en la tabla: relevarlo en AGI (código + si hay otra
   tabla de schedule) antes de dar el anunciador por cerrado. Premisa: en prod corre
   aunque HOSPROD no lo tenga programado, hasta que producto diga lo contrario.
