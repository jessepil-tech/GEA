---
title: Estado piloto / paridad vs foco GENERAL
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.estado-piloto-vs-general
---
# Estado piloto / paridad vs foco GENERAL

Fecha: **2026-09-16**  
**Orden vigente:** [`backlog-orden-2026-08-14.md`](../planificacion/backlog-orden-2026-08-14.md)  
**Criterio de avance:** [`criterio-avance-e2e-datos.md`](../canon/criterio-avance-e2e-datos.md)
(E2E permanente; escritura cobrada en `ts`; copia Oracle = bootstrap, no ABM; no seed/MVP).  
**Orientación Web:** [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) (**gate-done**; hijo [`paridad-recepcion-gate/`](../cortes/recepcion/paridad-recepcion-gate/))  
**Cierre AGI+Anunciador:** [`cierre-paridad-agi-anunciador/`](../cortes/anunciador/cierre-paridad-agi-anunciador/)  
**DDL canónico:** [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md) ·  
retiro piloto: [`retiro-tablas-piloto-public.md`](../arquitectura/retiro-tablas-piloto-public.md)  
**Mapa UI:** [`mapa-menu-hospital-web.md`](../relevamiento/mapa-menu-hospital-web.md) ·  
**Árbol menú Legacy↔Web:** [`arbol-mapeo-menu-legacy-web.md`](../relevamiento/arbol-mapeo-menu-legacy-web.md)

## Snapshot (una mirada)

| Área | Estado |
|------|--------|
| Identity oleada A | Hecho |
| Piloto Anunciador + display API Key | gate-done |
| Vertical G1 recepción → G1-d.birt | gate-done |
| CU-A / B / B.1 / C | gate-done (C medidor; no ensanchar seed) |
| Paridad Cola A+B (M1–M4) | gate-done |
| Apagar Node anunciador (N0–N5) | **gate-done DEV** |
| **Ciclo vida `LLAMAR`** | **gate-done** núcleo TV (enganche anulación → CU clínico) |
| **Llamar Cola B / atención médica** | **gate-done** 2026-09-16 (`cola-b-llamar` + `cu-llamar-atencion-medica`; acceso Identity diferido) |
| R3.1 ticket | ingeniería done; UAT impresora on-site |
| R3.2 harness testing BIRT real | hecho |
| R3.3 refactor motor genérico reportes | **hecho y commiteado** |
| R3.4 PoC AWS ECS Fargate | **hecho** (E2E PDF BIRT real) |
| **DDL `ts` + cutover Api** | **Hecho** (V27–V33; Fases 2–4 plan-cutover) |
| **Cierre paridad AGI+Anunciador** | **Active** — P0 cobrado; P1–P5 abiertos |
| **Paridad orientación Web** | **gate-done** W6 2026-08-31; hijo recepción-gate abierto |
| **Relevamiento Turnos (capa 3)** | **T0 hecho**; T1 parcial; **T2–T7 + T5-print + T5.5-excel + T6.* gate-done**; D-TUR-12 hab equipo **gate-done**; D-TUR-13 horarios equipo **gate-done** 2026-09-23; D-TUR-17 tramo 1 [`turnos-grilla-equipo`](../cortes/turnos/turnos-grilla-equipo/) **gate-done** 2026-09-24; tramo 2 [`turnos-agenda-equipo`](../cortes/turnos/turnos-agenda-equipo/) **gate-done** 2026-09-28; historial equipo [`turnos-agenda-historial-equipo`](../cortes/turnos/turnos-agenda-historial-equipo/) **gate-done** 2026-09-28; suspender/quitar equipo [`turnos-grilla-suspender-equipo`](../cortes/turnos/turnos-grilla-suspender-equipo/) **gate-done** 2026-09-28; consulta, cola, sobreturno y múltiples de equipo **gate-done** 2026-09-28 |
| Bootstrap Oracle→PG padres | Opcional (FK); **no** desbloquea ABM |
| Sync Oracle→PG colas | pendiente UAT |

**Anuncio en TV:** botón **Llamar** (Espera AGI / Cola recepción) **o** engranaje Llamar en lista espera atención médica (`/ambulatoria/espera-atencion`). Writer clínico = [`cu-llamar-atencion-medica/`](../cortes/recepcion/cu-llamar-atencion-medica/) **gate-done**. **No** al confirmar recepción.

> ### Deuda descubierta 2026-09-15 — capacidades sin pantalla
>
> El relevamiento de [procesos programados](../relevamiento/relevamiento-procesos-programados/README.md)
> encontró que **tres circuitos de este snapshot están incompletos**: su comportamiento se
> apoya en jobs del legacy que nunca se relevaron, porque no cuelgan del menú.
>
> | Circuito | Qué falta | Job |
> |----------|-----------|-----|
> | Paridad Cola A+B (M1–M4) | La cola **nunca se autolimpia**: sin el barrido de 6 h, los pacientes que se fueron quedan como «próximo a llamar» para siempre | `MigrarTurnoVencidoJob` |
> | Ciclo vida `LLAMAR` / TV | Los llamados **nunca se apagan**: sin el reset de 1 h, la TV sigue anunciando pacientes viejos. Y AGI tiene su propio `LlamadorAnunciadorJob` sin relevar | `MigrarTurnoVencidoJob` · `LlamadorAnunciadorJob` (AGI) |
> | T2 habilitación · T5/T6 agenda | Vigencia de habilitación sin recalcular; aviso de reprogramación, y **confirmación y cancelación de turno por SMS** (escribe `ts.turno`) | `CheckHabTurnosJob` · `MigrarTurnoVencidoJob` · `ProcesarSmsJob` |
>
> **No se reabren los gates** por decisión unilateral: son capacidades que el legacy tiene,
> así que corresponde `diferido(slug)` con nombre, no `WAIVE`
> ([`regla-waiver-paridad-legacy.md`](../canon/regla-waiver-paridad-legacy.md)). Antes de dimensionar
> hace falta el dato de producción: qué jobs están `ACTIVA` en `ts.tarea_programada`.

## Slices

| Slice | SDD | Estado |
|-------|-----|--------|
| **CU #1 Anunciador** (A1+A2) | [`piloto-agi-anunciador/`](../cortes/anunciador/piloto-agi-anunciador/) | **gate-done** |
| **G1…G1-d.birt** | [`piloto-agi-g1/`](../cortes/recepcion/piloto-agi-g1/) … [`piloto-agi-g1-d-birt/`](../cortes/recepcion/piloto-agi-g1-d-birt/) | **gate-done** |
| **R3.1** logo/barcode ticket | [`birt-runtime-destino.md`](../arquitectura/birt-runtime-destino.md) §4.1 | ingeniería done; UAT impresora |
| **R3.2** harness testing BIRT real | [`birt-runtime-destino.md`](../arquitectura/birt-runtime-destino.md) §4.2 | **hecho** |
| **R3.3** refactor motor genérico | [`birt-runtime-destino.md`](../arquitectura/birt-runtime-destino.md) §4.2 | **hecho y commiteado** |
| **R3.4** PoC AWS Fargate | [`despliegue-hospital-reports-aws.md`](../arquitectura/despliegue-hospital-reports-aws.md) | **hecho** E2E |
| **Avance sidecar** | [`sidecar-reports-avance.md`](sidecar-reports-avance.md) | snapshot |
| **CU-A catálogo** | [`cu-clinico-a-catalogo-abm/`](../cortes/recepcion/cu-clinico-a-catalogo-abm/) | **gate-done** |
| **CU-B post-recepción** | [`cu-clinico-b-post-recepcion/`](../cortes/recepcion/cu-clinico-b-post-recepcion/) | **gate-done** |
| **CU-B.1 Llamar** | [`cu-clinico-b1-llamar-recepcion/`](../cortes/recepcion/cu-clinico-b1-llamar-recepcion/) | **gate-done** |
| **CU-C demanda espontánea** | [`cu-clinico-c-demanda-espontanea/`](../cortes/recepcion/cu-clinico-c-demanda-espontanea/) | **gate-done** (medidor) |
| **Paridad recepción/cola** | [`paridad-recepcion-cola/`](../cortes/recepcion/paridad-recepcion-cola/) | **gate-done M1–M4** |
| **Anunciador WS** | [`anunciador-ws/`](../cortes/anunciador/anunciador-ws/) | **gate-done** |
| **Apagar Node** | [`apagar-anunciador-node/`](../cortes/anunciador/apagar-anunciador-node/) | **gate-done DEV** |
| **Ciclo vida llamado** | [`ciclo-vida-llamado-anunciador/`](../cortes/anunciador/ciclo-vida-llamado-anunciador/) | **gate-done** núcleo TV |
| **Llamar Cola B** | [`cola-b-llamar/`](../cortes/recepcion/cola-b-llamar/) | **gate-done** 2026-09-16 (Clarify B; GM+NFR; acceso diferido) |
| **Llamar atención médica** | [`cu-llamar-atencion-medica/`](../cortes/recepcion/cu-llamar-atencion-medica/) | **gate-done** 2026-09-16 E3; `diferido(acceso)` Identity |
| **Cutover `ts`** | [`plan-cutover-api-schema-ts.md`](../planificacion/plan-cutover-api-schema-ts.md) | **Fases 2–4 hechas** |
| **Cierre paridad AGI+Anunciador** | [`cierre-paridad-agi-anunciador/`](../cortes/anunciador/cierre-paridad-agi-anunciador/) | **active** P0 cobrado; **P3 SDD** [`anunciador-agi-config-abm/`](../cortes/anunciador/anunciador-agi-config-abm/) |
| **ABM anunciador/terminal** | [`anunciador-agi-config-abm/`](../cortes/anunciador/anunciador-agi-config-abm/) | **active** C5 `diferido(anunciador-agi-config-abm-c5)` |
| **M1a ABM centro** | [`maestros-m1a-centro/`](../cortes/maestros/maestros-m1a-centro/) | **gate-done** 2026-09-18 |
| **M1b servicio (catálogo)** | [`maestros-m1b-servicio/`](../cortes/maestros/maestros-m1b-servicio/) | **gate-done** 2026-09-18 |
| **M1c especialidad** | [`maestros-m1c-especialidad/`](../cortes/maestros/maestros-m1c-especialidad/) | **gate-done** 2026-09-18 |
| **M1c hijo especialidad_serv** | [`maestros-m1c-especialidad-serv/`](../cortes/maestros/maestros-m1c-especialidad-serv/) | **gate-done** 2026-09-18 |
| **Relevamiento Turnos (capa 3)** | **T0 hecho**; A4 T2 · A5 T3 · A6 T4 · **A7 T5 gate-done** |
| **T1 maestros identidad turno** | [`turnos-maestros-personal/`](../cortes/turnos/turnos-maestros-personal/) | **gate parcial** |
| **T2 habilitación hab_turnos_*** | [`turnos-config-hab-horarios/`](../cortes/turnos/turnos-config-hab-horarios/) | **gate-done** 2026-08-27 |
| **T3 grupos + horarios** | [`turnos-horarios-grupos/`](../cortes/turnos/turnos-horarios-grupos/) | **gate-done** 2026-09-01 |
| **T4 generación grilla** | [`turnos-generacion-grilla/`](../cortes/turnos/turnos-generacion-grilla/) | **gate-done** 2026-09-04 (G6 + e2e) |
| **T5 operación agenda** | [`turnos-agenda-otorgar/`](../cortes/turnos/turnos-agenda-otorgar/) | **gate-done** 2026-09-07 (e2e + smoke ops **PASS** 2026-09-08) |
| **T5.1 ficha paciente** | [`turnos-agenda-ficha-paciente/`](../cortes/turnos/turnos-agenda-ficha-paciente/) | **gate-done** 2026-09-08 |
| **T5.1b info popups** | [`turnos-agenda-info-popups/`](../cortes/turnos/turnos-agenda-info-popups/) | **gate-done** 2026-09-10 |
| **T5.1c elegibilidad** | [`turnos-agenda-elegibilidad-cobros/`](../cortes/turnos/turnos-agenda-elegibilidad-cobros/) | **gate-done** 2026-09-10 (P-ORA-010 abierto) |
| **T5.1d cobros agenda** | [`turnos-agenda-cobros/`](../cortes/turnos/turnos-agenda-cobros/) | **gate-done** 2026-09-10 (montos `diferido(fixture)`) |
| **T6.1 reasignar agenda** | [`turnos-agenda-reasignar/`](../cortes/turnos/turnos-agenda-reasignar/) | **gate-done** 2026-09-10 (G6 smoke ops **PASS**) |
| **T6.2 cola reasignar** | [`turnos-agenda-cola-reasignar/`](../cortes/turnos/turnos-agenda-cola-reasignar/) | **gate-done** 2026-09-17 (G6 smoke ops **PASS**) |
| **T6.2-print cola PDF** | [`turnos-agenda-cola-reasignar-print/`](../cortes/turnos/turnos-agenda-cola-reasignar-print/) | **gate-done** 2026-09-17 (G6 visual **PASS**) |
| **T6.2-excel cola Excel** | [`turnos-agenda-cola-reasignar-export/`](../cortes/turnos/turnos-agenda-cola-reasignar-export/) | **gate-done** 2026-09-17 (G6 visual **PASS**; «se ve bien ahora») |
| **T5.1e infoTurno chrome** | [`turnos-agenda-info-turno/`](../cortes/turnos/turnos-agenda-info-turno/) | **gate-done** 2026-09-10 (G6 smoke ops **PASS**) |
| **T5.2 sobreturno agenda** | [`turnos-agenda-sobreturno/`](../cortes/turnos/turnos-agenda-sobreturno/) | **gate-done** 2026-09-10 (G6 smoke ops **PASS**) |
| **Persist infoTurno** | [`turnos-agenda-info-turno-persist/`](../cortes/turnos/turnos-agenda-info-turno-persist/) | **gate-done** 2026-09-10 (G6 smoke ops **PASS**) |
| **Prep/req infoTurno** | [`turnos-agenda-info-turno-prest/`](../cortes/turnos/turnos-agenda-info-turno-prest/) | **gate-done** 2026-09-10 (G6 smoke ops **PASS**) |
| **T5.3 turnos repetidos** | [`turnos-agenda-repetidos/`](../cortes/turnos/turnos-agenda-repetidos/) | **gate-done** 2026-09-11 (G6 smoke ops **PASS**) |
| **T5.3-b cambiar horario** | [`turnos-agenda-repetidos-cambiar/`](../cortes/turnos/turnos-agenda-repetidos-cambiar/) | **gate-done** 2026-09-11 (G6 smoke ops **PASS**) |
| **T5.4 turnos múltiples** | [`turnos-agenda-multiples/`](../cortes/turnos/turnos-agenda-multiples/) | **gate-done** 2026-09-14 (G6 smoke ops **PASS**) |
| **T5.4-b cambiar horario múltiples** | [`turnos-agenda-multiples-cambiar/`](../cortes/turnos/turnos-agenda-multiples-cambiar/) | **gate-done** 2026-09-14 (G6 smoke ops **PASS**) |
| **T5.5 consulta agenda** | [`turnos-agenda-consultas/`](../cortes/turnos/turnos-agenda-consultas/) | **gate-done** 2026-09-15 (G6 smoke ops **PASS**; Excel → hijo **gate-done**; PDF valores → hijo **gate-done**) |
| **T5.5-pdf consulta agenda** | [`turnos-agenda-consultas-pdf/`](../cortes/turnos/turnos-agenda-consultas-pdf/) | **gate-done** 2026-09-15 (G6 visual **PASS**; T6 / D-TUR-17 siguen diferidos) |
| **T5.5-excel consulta agenda** | [`turnos-agenda-consultas-export/`](../cortes/turnos/turnos-agenda-consultas-export/) | **gate-done** 2026-09-16 (G6 visual **PASS**; «ok el excel esta saliendo bien») |
| **T5.6 pre-agenda** | [`turnos-agenda-preagenda/`](../cortes/turnos/turnos-agenda-preagenda/) | **gate-done** 2026-09-15 (G6 smoke ops **PASS**) |
| **T6.3 historial turnos** | [`turnos-agenda-historial/`](../cortes/turnos/turnos-agenda-historial/) | **gate-done** 2026-09-18 (G6 «ya veo registros»; info «si se ve ok»; Excel hijo **gate-done**) |
| **T6.3-excel historial** | [`turnos-agenda-historial-export/`](../cortes/turnos/turnos-agenda-historial-export/) | **gate-done** 2026-09-18 (G6 visual **PASS**; «ok se ve bien el excel») |
| **T6.4 suspender grilla** | [`turnos-grilla-suspender/`](../cortes/turnos/turnos-grilla-suspender/) | **gate-done** 2026-09-18 (G6 LIBRE→SUSPENDIDO→LIBRE id `17255154`; otorgado→cola) |
| **T6.5 reemplazo profesional** | [`turnos-grilla-reemplazo/`](../cortes/turnos/turnos-grilla-reemplazo/) | **gate-done** 2026-09-21 D-TUR-74 (G6 OTORGADO `17255210` / LIBRE `17255283` / parcial / hist) |
| **T6 ciclo de vida (vencidos)** | [`turnos-ciclo-vida/`](../cortes/turnos/turnos-ciclo-vida/) | **gate-done** 2026-09-21 D-TUR-75 (G6 «ok» `id_turno_vencido=19999001`; hoy `17255155` intacto) |
| **T7 avisos de turno** | [`turnos-avisos/`](../cortes/turnos/turnos-avisos/) | **gate-done** 2026-09-22 D-TUR-76 (G6 «ok si veo bien las consultas» `id_mensaje_turno_vencido=36222854`; `19999101` en `turno_vencido`) |
| **T5 hijo PDF turno Agenda** | [`turnos-agenda-imprimir-turno/`](../cortes/turnos/turnos-agenda-imprimir-turno/) | **gate-done** 2026-09-22 D-TUR-77 (G6 «joya sale bien el pdf»; coseguro SP diferido) |
| **Paciente-Web** (portal) | [`paciente-web/`](../cortes/plataforma/paciente-web/) | **diferido** — no es Hospital-Web; Identity `PACIENTE` |
| **Paridad orientación Web** | [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) | **gate-done** W6; hijo [`paridad-recepcion-gate/`](../cortes/recepcion/paridad-recepcion-gate/) |
| **Buscadores hab** | [`turnos-hab-buscadores/`](../cortes/turnos/turnos-hab-buscadores/) | **active** Clarify propuesto |
| **UX shell** | [`ux-shell-primefaces/`](../cortes/plataforma/ux-shell-primefaces/) | según tasks |

## Reorden vigente

1. ~~AGI…CU medidor~~ — **código en `ts`** (no evidencia con seed)
2. ~~Paridad Cola M1–M4~~ — **gate-done**
3. ~~Apagar Node N1–N5 (dev)~~ — **gate-done**
4. ~~Cutover persistencia → `ts`~~ — **hecho** (V27–V33)
5. ~~Ciclo vida `LLAMAR` núcleo TV~~ — **gate-done** (P0)
6. ~~P3 ABM anunciador/terminal~~ — cobro parcial; C5 `diferido(anunciador-agi-config-abm-c5)`
7. ~~**M1a ABM centro**~~ — **gate-done** 2026-09-18 ([`maestros-m1a-centro/`](../cortes/maestros/maestros-m1a-centro/))
8. ~~**M1b catálogo servicio**~~ — **gate-done** 2026-09-18 ([`maestros-m1b-servicio/`](../cortes/maestros/maestros-m1b-servicio/))
8b. ~~**M1c catálogo especialidad**~~ — **gate-done** 2026-09-18 ([`maestros-m1c-especialidad/`](../cortes/maestros/maestros-m1c-especialidad/))
8c. ~~**M1c hijo especialidad_serv**~~ — **gate-done** 2026-09-18 ([`maestros-m1c-especialidad-serv/`](../cortes/maestros/maestros-m1c-especialidad-serv/))
8d. ~~**M1a hijo grp 10217**~~ — **gate-done** 2026-09-18 ([`maestros-m1a-grp/`](../cortes/maestros/maestros-m1a-grp/))
8e. ~~**M1a hijo logos west**~~ — **gate-done** 2026-09-23 ([`maestros-m1a-centro-west/`](../cortes/maestros/maestros-m1a-centro-west/); resto [`maestros-m1a-centro-west-resto/`](../cortes/maestros/maestros-m1a-centro-west-resto/))
9. Gate recepción / Cola B — HIS, no bloquea M1  
   ~~Llamar fila Cola B / atención médica~~ — **gate-done** 2026-09-16 ([`cola-b-llamar/`](../cortes/recepcion/cola-b-llamar/) · [`cu-llamar-atencion-medica/`](../cortes/recepcion/cu-llamar-atencion-medica/))
10. R3.1 UAT impresora (P2, carril on-site)
11. GENERAL / BIRT R5 / FKs diferidas bajo demanda de CU

Paralelo: [`trabajo-paralelo-equipo.md`](../planificacion/trabajo-paralelo-equipo.md) ·
[`relevamiento-his-inventario-global/arbol-dependencias.md`](../relevamiento/relevamiento-his-inventario-global/arbol-dependencias.md)
(tablero 16 streams) ·
[`ajustes-prioridad-migracion.md`](../planificacion/ajustes-prioridad-migracion.md) ·
[`gobierno-migracion.md`](../canon/gobierno-migracion.md).

**Anti-fork:** no nuevas tablas `public` de dominio ni `*_agi`.

## Cómo retomar (DEV / IT — no es evidencia de paridad)

Seed y DNI demo **solo** para CI y humo local. El E2E de producto usa filas
**nacidas de CUs** en `ts`. Copia Oracle = bootstrap de padres, no prueba de ABM
([`criterio-avance-e2e-datos.md`](../canon/criterio-avance-e2e-datos.md)).

```bash
# Stack: Identity :8080 · Api :8081 · Web :4200 · PG (schema ts)
bash tools/dev-stack-up.sh   # si aplica
# Display: /display/anunciadores/{id} + API Key demo-api-key
# Flujo: /agi/recepcion → /agi/espera → Llamar → TV
```

UI recepción demo: DNI `30111222` (atención) · `30999888` (recepción humana)
· `30777888` (credencial) · `30666888` (token).
