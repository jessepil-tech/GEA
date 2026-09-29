---
title: Orden de trabajo recomendado
status: active
owner: grupogea
last_updated: 2026-09-28
phase_id: sdd.hospital.backlog-orden-2026-08-14
---
# Orden de trabajo recomendado (post-VPN 2026-08-14)

`phase_id:` **`sdd.hospital.backlog-orden-2026-08-14`**  
Actualizado: **2026-09-28** (D-TUR-17 tramo 2 `turnos-agenda-equipo` **gate-done**; writer `ts.turno` liberado. Abierto `turnos-hab-equipo` D-TUR-12, tramo 1 de D-TUR-17; T7 avisos **gate-done** D-TUR-76; T6 vencidos **gate-done** D-TUR-75; T6.5 reemplazo **gate-done** D-TUR-74; T6.4 suspender **gate-done** D-TUR-72; M1a/M1b/M1c **gate-done**; tronco M1 cobrado; P3 C5 `diferido(anunciador-agi-config-abm-c5)`)

**Alcance (15-sep):** el cliente definió qué proyectos se migran
([`alcance-proyectos-migracion.md`](alcance-proyectos-migracion.md)). No mueve las filas de
esta semana —los 16 streams están todos dentro— pero suma dos carriles sin owner: **Seguridad**
(perfiles y menú por perfil) y el **disparador de procesos programados**, del que dependen
Recepción, Turnos y el Anunciador ya cerrados.

Criterio: [`criterio-avance-e2e-datos.md`](../canon/criterio-avance-e2e-datos.md) —
cerrar E2E del **circuito ya migrado** con filas **nacidas en `ts`** (CUs que
escriben). Copia Oracle = bootstrap de padres, no prueba de ABM. No seed.
No abrir tile nuevo como “siguiente piloto”.

El dossier (fases 0–4, DDL §6.5) sigue valiendo. El §6.4 “piloto” ya se cobró
como código; no es el modo de trabajo de aquí en adelante.


---

## Principio

| Tipo de trabajo | Rol |
|-----------------|-----|
| **Bootstrap padres** | Carga Oracle→PG **solo** de tablas que esta oleada no ABMea (paciente/centro/…) |
| **Escritura cobrada** | T2/T3/CU-A/cola/llamar: INSERT desde UI/API en `ts`, no seed ni dump |
| **T4 / T5 Turnos** | Oferta `LIBRE` y otorgar — no se sustituyen copiando `turno` |
| **IDs (`GENERAL`)** | Excepción de package: precondición de inserts reales (Fase 1/4) |
| **BIRT** | Corte con reportes vivos; R3.1 impresora no bloquea el E2E funcional |

---

## Orden recomendado (de aquí en adelante)

| # | Trabajo | Por qué ahora | Fase dossier |
|---|---------|---------------|--------------|
| **✓** | AGI + CU-A/B/B.1/C | Código cobrado en `ts` — **no** evidencia con seed | Fase 3 |
| **✓** | **Cutover persistencia → `ts`** (V27–V33) | Hecho 2026-08-25; dominio anunciador/cola/AGI/catálogo en `ts` | §6.5 |
| **✓** | **Ciclo vida `LLAMAR` (núcleo TV)** | gate-done 2026-08-26; enganche anulación → SDD hijo | P0 |
| **✓** | **Paridad orientación Web** (tile→módulo, hab TURNOS, satélites) | **gate-done** 2026-08-31; hijo recepción-gate | [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) |
| **✓** | **M1a ABM centro** [`maestros-m1a-centro/`](../cortes/maestros/maestros-m1a-centro/) | **gate-done** 2026-09-18; hijos grp/west/auditoría | Maestros |
| **✓** | **M1b catálogo servicio** [`maestros-m1b-servicio/`](../cortes/maestros/maestros-m1b-servicio/) | **gate-done** 2026-09-18; hijos tabs/auditoría | Maestros |
| **✓** | **M1c catálogo especialidad** [`maestros-m1c-especialidad/`](../cortes/maestros/maestros-m1c-especialidad/) | **gate-done** 2026-09-18; hijo especialidad-serv cobrado | Maestros |
| **2** | **Bootstrap padres** (paciente / grp / provincia si el corte no los ABMea) | FK; **no** cierra ABM de centro/servicio | [`criterio-avance-e2e-datos.md`](../canon/criterio-avance-e2e-datos.md) |
| **✓** | **T4 generación grilla** | **gate-done** 2026-09-04 | [`turnos-generacion-grilla/`](../cortes/turnos/turnos-generacion-grilla/) |
| **✓** | **T5 otorgar / agenda** | **gate-done** 2026-09-07 (e2e PASS; smoke ops pendiente) | [`turnos-agenda-otorgar/`](../cortes/turnos/turnos-agenda-otorgar/) |
| **5** | **E2E mostrador** [`e2e-mostrador/`](../cortes/recepcion/e2e-mostrador/) (AGI/Recepción → cola → Llamar → TV) sobre turnos nacidos en PG | CUs ya migrados; Llamar TV cobrado en 6b/6c; SDD abierto 2026-09-16 | P1 |
| **6** | Gate recepción + Cola B menú M5 | Paridad HIS; **después** de P3; no es Llamar | [`paridad-recepcion-gate/`](../cortes/recepcion/paridad-recepcion-gate/) · P1 |
| **✓** | **Llamar Cola B** [`cola-b-llamar/`](../cortes/recepcion/cola-b-llamar/) (fila `serv_amb` → TV) | **gate-done** 2026-09-16; menú Cola B **no** tiene Llamar | P1 |
| **✓** | **Llamar atención médica** (writer clínico) | **gate-done** 2026-09-16 E3; acceso `diferido(acceso)` | [`cu-llamar-atencion-medica/`](../cortes/recepcion/cu-llamar-atencion-medica/) |
| **7** | **R3.1 UAT impresora térmica** | **Pospuesto** hasta UAT + impresoras (on-site) | reportes · P2 |
| **P** | Carril paralelo — [ajustes prioridad](ajustes-prioridad-migracion.md) | Costo oculto / seguridad / corte | §11 + Fase 1 |
| **Diferido** | **Paciente-Web** (portal; sucesor HOS-APP **paciente**) | No mezclar con shell staff; **después** T5.1 / canal WEB | [`paciente-web/`](../cortes/plataforma/paciente-web/) |
| **8** | **Fase 0** higiene — **aplazada** | Higiene | Fase 0 |
| **9** | Más BIRT / GENERAL bajo demanda | Corte / CU | Fase 4 |

**Anti-patrón explícito:** seguir agregando pantallas/tablas `*_agi` o seeds “porque
el happy path funciona” → fork. Toda feature nueva = paridad legacy sobre **`ts`**
con filas **#1** (escritura del CU), o WAIVER de negocio. Bootstrap #2 no cierra ABM.
No hay segundo MVP.

**Tablero de streams** (capacidad A–C / BODY / próximo corte; no sustituye esta
tabla): [`relevamiento-his-inventario-global/arbol-dependencias.md`](../relevamiento/relevamiento-his-inventario-global/arbol-dependencias.md)
§ Tablero. Este archivo elige **cuál** fila esta semana.

**Cómo se ejecuta la fila elegida:** [`loop-migracion-corte.md`](../canon/loop-migracion-corte.md)
(paso 1 = esta tabla; reservar y contratar fixture **antes** de código).

**R3.1:** ingeniería (logo/DANI/params) ya hecha; **no** bloquear recepción/cola por UAT
impresora — requiere sitio + hardware.

### Deuda reportes (no borrar)

- [x] **R3.1** Logo institucional en `NroColaEsperaRecep`
- [x] **R3.1** Barcode recepción (font DANI + interleaved; no OnBarcode)
- [x] **R3.2** Harness de testing BIRT real (validación por reporte vs motor BIRT + PG)
- [x] **R3.3** Refactor `generic-report-engine` (motor genérico) — **hecho y commiteado** (habilitador R5; SDD archivado)
- [x] **R3.4** PoC AWS ECS Fargate — **hecho** (E2E PDF BIRT real)
- [ ] **R3.1** UAT impresora térmica — **pospuesto** (falta UAT + impresoras conectadas)
- [x] Primer reporte R5 — oleada [`r5-historia-clinica-epicurisis-gye/`](../cortes/plataforma/r5-historia-clinica-epicurisis-gye/) (`HistoriaClinica` + `EpicrisisGYE`) — scaffold + dialect; PDF E2E pendiente ports R4
- [ ] Barcode triage (`f_cod_barra_triage_amb`) cuando exista CU triage

Detalle: [`birt-runtime-destino.md`](../arquitectura/birt-runtime-destino.md) §4 ·
[`sidecar-reports-avance.md`](../estado/sidecar-reports-avance.md) ·
[`r5-historia-clinica-epicurisis-gye/`](../cortes/plataforma/r5-historia-clinica-epicurisis-gye/) ·
[`piloto-agi-g1-d-birt/`](../cortes/recepcion/piloto-agi-g1-d-birt/).

### Siguiente SDD (paridad)

**Cerrado (M1–M4):** [`paridad-recepcion-cola/`](../cortes/recepcion/paridad-recepcion-cola/) — Cola A + Cola B read/UI.  
**Cerrado (N1–N5 dev):** [`apagar-anunciador-node/`](../cortes/anunciador/apagar-anunciador-node/).  
**Cerrado (DDL):** [`plan-cutover-api-schema-ts.md`](plan-cutover-api-schema-ts.md) V27–V33.

**Checklist unificado:** [`cierre-paridad-agi-anunciador/`](../cortes/anunciador/cierre-paridad-agi-anunciador/).

Pendiente / diferido (con slug):

| Prioridad | Slug | Notas |
|-----------|------|--------|
| — | [`paridad-orientacion-web/`](../cortes/plataforma/paridad-orientacion-web/) | **gate-done** shell. Hijo: [`paridad-recepcion-gate/`](../cortes/recepcion/paridad-recepcion-gate/) |
| Alta | [`paridad-recepcion-gate/`](../cortes/recepcion/paridad-recepcion-gate/) | Gate centro/recepción/box; no reabre el shell |
| Hecho | [`maestros-m1a-centro/`](../cortes/maestros/maestros-m1a-centro/) | **M1a gate-done** 2026-09-18. Hijos: grp **gate-done** · west logos **active** · [`maestros-m1a-auditoria-centro`](../cortes/maestros/maestros-m1a-auditoria-centro/) |
| Hecho | [`maestros-m1b-servicio/`](../cortes/maestros/maestros-m1b-servicio/) | **M1b gate-done** 2026-09-18. Hijos: [`maestros-m1b-servicio-tabs`](../cortes/maestros/maestros-m1b-servicio-tabs/) · [`maestros-m1b-auditoria-servicio`](../cortes/maestros/maestros-m1b-auditoria-servicio/) |
| Diferido | [`maestros-m1b-servicio-tabs/`](../cortes/maestros/maestros-m1b-servicio-tabs/) | Tabs amb/int/lab del vínculo |
| Diferido | [`maestros-m1b-auditoria-servicio/`](../cortes/maestros/maestros-m1b-auditoria-servicio/) | `AUD_SERVICIO_CENTRO` / `TBL_AUD_*` |
| Hecho | [`maestros-m1c-especialidad/`](../cortes/maestros/maestros-m1c-especialidad/) | **M1c gate-done** 2026-09-18. Hijo: [`maestros-m1c-especialidad-serv`](../cortes/maestros/maestros-m1c-especialidad-serv/) |
| Hecho | [`maestros-m1c-especialidad-serv/`](../cortes/maestros/maestros-m1c-especialidad-serv/) | Vínculo `especialidad_serv` **gate-done** 2026-09-18 |
| Hecho | [`maestros-m1a-grp/`](../cortes/maestros/maestros-m1a-grp/) | ABM grupo 10217 **gate-done** 2026-09-18 |
| Hecho | [`maestros-m1a-centro-west/`](../cortes/maestros/maestros-m1a-centro-west/) | Slice logo `pack_logos` **gate-done** 2026-09-23 |
| Diferido | [`maestros-m1a-centro-west-resto/`](../cortes/maestros/maestros-m1a-centro-west-resto/) | Resto west (sector/caja/ambientes) |
| Diferido | [`maestros-m1a-auditoria-centro/`](../cortes/maestros/maestros-m1a-auditoria-centro/) | `AUD_CENTRO_ATENCION` / `TBL_AUD_*` |
| Diferido | [`anunciador-agi-config-abm-c5/`](../cortes/anunciador/anunciador-agi-config-abm-c5/) | P3 C5 smoke / Playwright; padre cobro parcial id=3 |
| Alta | [`anunciador-agi-config-abm/`](../cortes/anunciador/anunciador-agi-config-abm/) | **P3** cobro parcial 2026-09-15: ITs+id=3; C5 `diferido(anunciador-agi-config-abm-c5)` |
| Diferido | [`anunciador-config-avanzada/`](../cortes/anunciador/anunciador-config-avanzada/) | Serv/triage/esp-serv; **no** flags de `configuracionAvanzada.xhtml` |
| — | [`ciclo-vida-llamado-anunciador/`](../cortes/anunciador/ciclo-vida-llamado-anunciador/) | **gate-done** núcleo TV; enganche CUs Fase 4 |
| Hecho | [`cu-llamar-atencion-medica/`](../cortes/recepcion/cu-llamar-atencion-medica/) | **gate-done** 2026-09-16 E3; `diferido(acceso)` Identity; ampliar/norte abiertos |
| Media | Acciones menú Cola B (M5: anular, CI, print…) | P1; **no** Llamar — ver [`cola-b-llamar/`](../cortes/recepcion/cola-b-llamar/) |
| Hecho | [`cola-b-llamar/`](../cortes/recepcion/cola-b-llamar/) | **gate-done** 2026-09-16 writer `serv_amb` → TV Clarify B |
| Media | Bootstrap Oracle→PG **padres** (no ABM de esta oleada) | FK; no desbloquea T2/T4 ni E2E de escritura |
| UAT | R3.1 impresora térmica | P2 |
| Media | [`r5-historia-clinica-epicurisis-gye/`](../cortes/plataforma/r5-historia-clinica-epicurisis-gye/) | Otro frente (no bloquea cierre AGI sala) |
| Capa 3 | [`relevamiento-turnos/`](../relevamiento/relevamiento-turnos/) | T0 hecho; A4 T2 · A5 T3 · A6 T4 · **A7 T5 gate-done** 2026-09-07 |
| Alta | [`turnos-maestros-personal/`](../cortes/turnos/turnos-maestros-personal/) | T1 **gate parcial** |
| Hecho | [`turnos-config-hab-horarios/`](../cortes/turnos/turnos-config-hab-horarios/) | **T2 gate-done** 2026-08-27 |
| Hecho | [`turnos-hab-buscadores/`](../cortes/turnos/turnos-hab-buscadores/) | **gate-done** 2026-08-28 |
| Hecho | [`turnos-hab-buscadores-filtros/`](../cortes/turnos/turnos-hab-buscadores-filtros/) | **gate-done** 2026-08-28 — paridad dialog |
| Hecho | [`turnos-horarios-grupos/`](../cortes/turnos/turnos-horarios-grupos/) | **T3 gate-done** 2026-09-01 |
| Hecho | [`turnos-horarios-pers-shell/`](../cortes/turnos/turnos-horarios-pers-shell/) | Hijo T3 — west + inicio/fin reserva (2026-09-01) |
| Hecho | [`turnos-generacion-grilla/`](../cortes/turnos/turnos-generacion-grilla/) | **T4 gate-done** 2026-09-04 (G6 smoke + e2e consulta/generar/eliminar) |
| Hecho | [`turnos-agenda-otorgar/`](../cortes/turnos/turnos-agenda-otorgar/) | **T5 gate-done** 2026-09-07 (G2–G5 Api+Web · e2e · smoke ops **PASS** 2026-09-08) |
| Hecho | [`turnos-agenda-ficha-paciente/`](../cortes/turnos/turnos-agenda-ficha-paciente/) | **T5.1 gate-done** 2026-09-08 (buscadores north + west ficha · G6 ops) |
| Hecho | [`turnos-agenda-info-popups/`](../cortes/turnos/turnos-agenda-info-popups/) | **T5.1b gate-done** 2026-09-10 — PNG + chrome info convenio · auto-popup **WAIVE** D-TUR-39 |
| Hecho | [`turnos-agenda-elegibilidad-cobros/`](../cortes/turnos/turnos-agenda-elegibilidad-cobros/) | **T5.1c gate-done** 2026-09-10 — seed afiliado + filas doc req · P-ORA-010 abierto |
| Hecho | [`turnos-agenda-cobros/`](../cortes/turnos/turnos-agenda-cobros/) | **T5.1d gate-done** 2026-09-10 — rechazo → PARTICULAR HOSPITAL · montos `diferido(fixture)` |
| Hecho | [`turnos-agenda-reasignar/`](../cortes/turnos/turnos-agenda-reasignar/) | **T6.1 gate-done** 2026-09-10 — menú fila REASIGNAR · G6 smoke ops **PASS** |
| Hecho | [`turnos-agenda-info-turno/`](../cortes/turnos/turnos-agenda-info-turno/) | **T5.1e gate-done** 2026-09-10 — chrome `infoTurno.xhtml` 1200×520 · persist/prep diferidos |
| Hecho | [`turnos-agenda-sobreturno/`](../cortes/turnos/turnos-agenda-sobreturno/) | **T5.2 gate-done** 2026-09-10 — accordion 170 + popup Sobreturno · G6 smoke ops **PASS** |
| Hecho | [`turnos-agenda-info-turno-persist/`](../cortes/turnos/turnos-agenda-info-turno-persist/) | Persist obs + fecha prescripción al otorgar · **gate-done** 2026-09-10 · G6 smoke ops **PASS** |
| Hecho | [`turnos-agenda-info-turno-prest/`](../cortes/turnos/turnos-agenda-info-turno-prest/) | Prep / requisitos realización GET infoTurno · **gate-done** 2026-09-10 · G6 smoke ops **PASS** |
| Hecho | [`turnos-agenda-repetidos/`](../cortes/turnos/turnos-agenda-repetidos/) | **T5.3 gate-done** 2026-09-11 — TURNOS REPETIDOS · G6 smoke ops **PASS** |
| Hecho | [`turnos-agenda-repetidos-cambiar/`](../cortes/turnos/turnos-agenda-repetidos-cambiar/) | **T5.3-b gate-done** 2026-09-11 — `$popupCambiarTurno` · G6 smoke ops **PASS** |
| Hecho | [`turnos-agenda-multiples/`](../cortes/turnos/turnos-agenda-multiples/) | **T5.4 gate-done** 2026-09-14 — Turnos Múltiples · G6 smoke ops **PASS** |
| Hecho | [`turnos-agenda-multiples-cambiar/`](../cortes/turnos/turnos-agenda-multiples-cambiar/) | **T5.4-b gate-done** 2026-09-14 — `$popupCambiarTurno` swap in-memory · G6 smoke ops **PASS** |
| Hecho | [`turnos-agenda-consultas/`](../cortes/turnos/turnos-agenda-consultas/) | **T5.5 gate-done** 2026-09-15 — G6 smoke ops **PASS** · Imprimir cable; Excel diferido |
| Hecho | [`turnos-agenda-consultas-pdf/`](../cortes/turnos/turnos-agenda-consultas-pdf/) | **T5.5-pdf gate-done** 2026-09-15 — G6 visual **PASS** · «ok ahora se está visualizando información en los pdf» |
| Hecho | [`turnos-agenda-imprimir-turno/`](../cortes/turnos/turnos-agenda-imprimir-turno/) | T5 hijo **gate-done** 2026-09-22 D-TUR-77 — G6 «joya sale bien el pdf» |
| Hecho | [`turnos-agenda-consultas-export/`](../cortes/turnos/turnos-agenda-consultas-export/) | **T5.5-excel gate-done** 2026-09-16 — G6 visual **PASS** · «ok el excel esta saliendo bien» |
| Hecho | [`turnos-agenda-cola-reasignar-export/`](../cortes/turnos/turnos-agenda-cola-reasignar-export/) | **T6.2-excel gate-done** 2026-09-17 — Excel south cola Reasignación · G6 «se ve bien el excel y sale la info» · estilo «se ve bien ahora» |
| Hecho | [`turnos-agenda-cola-reasignar-print/`](../cortes/turnos/turnos-agenda-cola-reasignar-print/) | **T6.2-print gate-done** 2026-09-17 — PDF south cola Reasignación · G6 «listo ahi pude probar y sale info el reporte» |
| Hecho | [`turnos-agenda-cola-reasignar/`](../cortes/turnos/turnos-agenda-cola-reasignar/) | **T6.2 gate-done** 2026-09-17 — cola accordion Reasignación · G6 «ok ahora pude probar el flujo» · obs «listo probado funciona» |
| Hecho | [`turnos-agenda-historial/`](../cortes/turnos/turnos-agenda-historial/) | **T6.3 gate-done** 2026-09-18 — Historial accordion · G6 lista «ya veo registros» · info «si se ve ok» · Excel hijo gate-done |
| Hecho | [`turnos-agenda-historial-export/`](../cortes/turnos/turnos-agenda-historial-export/) | **T6.3-excel gate-done** 2026-09-18 — Excel south Historial · G6 «ok se ve bien el excel» |
| Hecho | [`turnos-grilla-suspender/`](../cortes/turnos/turnos-grilla-suspender/) | **T6.4 gate-done** 2026-09-18 — suspender + quitar grilla · D-TUR-72 · G6 LIBRE→SUSPENDIDO→LIBRE |
| Hecho | [`turnos-grilla-reemplazo/`](../cortes/turnos/turnos-grilla-reemplazo/) | **T6.5 gate-done** 2026-09-21 — reemplazar + quitar profesional · D-TUR-74 · G6 OTORGADO/LIBRE/parcial/hist · BODY TURNOS **liberado** |
| Hecho | [`turnos-agenda-preagenda/`](../cortes/turnos/turnos-agenda-preagenda/) | **T5.6 gate-done** 2026-09-15 — G6 smoke ops **PASS** · alta ATENCION y menú `consultaPreagenda` diferidos |
| Abierto | [`turnos-consultas-preagenda/`](../cortes/turnos/turnos-consultas-preagenda/) | Clarify **propuesto** 2026-09-28 — `consultaPreagenda.xhtml`. No es el Turnero. Sin writer. Sin rama |
| Diferido | [`turnos-consultas-operador/`](../cortes/turnos/turnos-consultas-operador/) | `consultaTurnos*.xhtml` menú TURNOS — no mezclar con T5.5 |
| Diferido | [`paciente-web/`](../cortes/plataforma/paciente-web/) | Portal paciente (HOS-APP cuenta web). Staff = Hospital-Web. **No** extraer AGI. Identity = mismo servicio, `subjectType=PACIENTE`. Capa 3 **antes** de spec. No subir sobre P3 ni E2E mostrador |
| Hecho | [`turnos-ciclo-vida/`](../cortes/turnos/turnos-ciclo-vida/) | **T6 gate-done** 2026-09-21 D-TUR-75 Camino 1 — migrar turno vencido · G6 «ok» `19999001` · BODY TURNOS **liberado** (T7 lo reserva de nuevo) |
| Hecho | [`turnos-avisos/`](../cortes/turnos/turnos-avisos/) | **T7 gate-done** 2026-09-22 D-TUR-76 Camino 1 — G6 «ok si veo bien las consultas» `36222854` · BODY TURNOS+GENERA_MAILS **liberado** |
| Hecho | [`turnos-hab-equipo/`](../cortes/turnos/turnos-hab-equipo/) | **D-TUR-12 gate-done** 2026-09-23 — hab turnos por equipo. Writer `hab_turnos_equipo_serv` liberado |
| Hecho | [`turnos-horarios-equipo/`](../cortes/turnos/turnos-horarios-equipo/) | **D-TUR-13 gate-done** 2026-09-23 — grupo, prestaciones, horario y reserva del lápiz. G6 «si funciona bien el flujo». Cinco tablas liberadas |
| gate-done | [`turnos-grilla-equipo/`](../cortes/turnos/turnos-grilla-equipo/) | **D-TUR-17 tramo 1** gate-done 2026-09-24. Writer `ts.turno` liberado |
| gate-done | [`turnos-agenda-equipo/`](../cortes/turnos/turnos-agenda-equipo/) | **D-TUR-17 tramo 2** gate-done 2026-09-28 — combo Equipo de Agenda. Writer `ts.turno` liberado |
| gate-done | [`turnos-agenda-historial-equipo/`](../cortes/turnos/turnos-agenda-historial-equipo/) | Combo Equipo de Historial. **gate-done** 2026-09-28. Sin writer. `diferido(perf-volumen)` |
| gate-done | [`turnos-grilla-suspender-equipo/`](../cortes/turnos/turnos-grilla-suspender-equipo/) | Radio Equipo de Suspender y Quitar. **gate-done** 2026-09-28. G6 «lo probe y funciono». `17255287`. `diferido(perf-volumen)` |
| gate-done | [`turnos-agenda-consulta-equipo/`](../cortes/turnos/turnos-agenda-consulta-equipo/) | Combo Equipo de Consulta. **gate-done** 2026-09-28. `diferido(perf-volumen)` |
| gate-done | [`turnos-agenda-cola-equipo/`](../cortes/turnos/turnos-agenda-cola-equipo/) | Combo Equipo de la cola. **gate-done** 2026-09-28. `diferido(perf-volumen)` |
| gate-done | [`turnos-agenda-sobreturno-equipo/`](../cortes/turnos/turnos-agenda-sobreturno-equipo/) | Combo Equipo del sobreturno. **gate-done** 2026-09-28. `id=17255333`. `diferido(perf-volumen)` |
| gate-done | [`turnos-agenda-multiples-equipo/`](../cortes/turnos/turnos-agenda-multiples-equipo/) | Filtro Equipo de turnos múltiples. **gate-done** 2026-09-28. `diferido(perf-volumen)` |
| Diferido | [`turnos-agenda-equipo-hermanas/`](../cortes/turnos/turnos-agenda-equipo-hermanas/) | Índice. Las hojas pasaron a su slug |
| Diferido | [`turnos-equipo-serv-centro/`](../cortes/turnos/turnos-equipo-serv-centro/) | ABM del vínculo sigue diferido. Hay una fila mock `EQDEMO1001` para la hab |
| Diferido | [`turnos-horarios-inhibiciones/`](../cortes/turnos/turnos-horarios-inhibiciones/) | Hijo T3 (D-TUR-14) |
| Diferido | [`turnos-horarios-especiales/`](../cortes/turnos/turnos-horarios-especiales/) | Hijo T3 (D-TUR-15) |
| Deuda | [`deuda-validaciones-pre-hab-turnos/`](../estado/deuda-validaciones-pre-hab-turnos.md) | Pantallas **antes de T2** (+ T2/buscadores pre-v1.4) sin inventario BB/MessageBundle — no WAIVE; cerrar al retocar |

- Evidencia: `cabeceraRecepcion.xhtml`, `colaEspera.xhtml`, `f_llamar_paciente_recepcion`
- WS sala: [`anunciador-ws/`](../cortes/anunciador/anunciador-ws/)
- Mapa UI: [`mapa-menu-hospital-web.md`](../relevamiento/mapa-menu-hospital-web.md)
- Gobierno / capas: [`gobierno-migracion.md`](../canon/gobierno-migracion.md)
- Criterio E2E / datos: [`criterio-avance-e2e-datos.md`](../canon/criterio-avance-e2e-datos.md)
- Proceso anti-gap: [`proceso-sdd-paridad-completa.md`](../canon/proceso-sdd-paridad-completa.md)
- Regla DDL: [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md)
- Destino físico colas/llamados = `ts.*`.

---

## Qué **no** subir de prioridad

- Seguir extrayendo VPN “por si acaso” (ya alcanza para este frente).
- EAN-13 (`CHAR_COD_BARRA` vacía).
- Abrir Internación / Nutrición / Lab como “siguiente vertical” antes del E2E mostrador.
- Abrir spec/implementación de **Paciente-Web** antes del relevamiento A–C y antes de cobrar T5.1 / E2E mostrador (la fila existe; no es el carril actual).
- Reescribir BIRT o migrar los 187 reportes antes de cobrar T4.
- Oracle 19 como destino (plan A cerrado).

---

## Estado ya cobrado (no rehacer)

| Entrega | Estado |
|---------|--------|
| Identity oleada A | Hecho |
| Piloto Anunciador CU#1 + display API Key | gate-done |
| Piloto G1 recepción v1 | gate-done |
| GM edades / interleaved / AFIP | PASS |
| Inventario next-id + seed CSV | Hecho |
| `NextIdService` + Flyway V10 | Hecho |
| SEC_ID_TABLA + InternacionIdService + V13 | Hecho |
| Consumo NextId en CU G1 (`COLA_ESPERA_RECEP`) | **Hecho** (V14 · plan §4.3) |
| BIRT helpers V11 (`persona_full`, edades) | Hecho (mínimo) |
| G1-b elegibilidad (V12 + puerto seed) | **gate-done** (IT + smoke PASS 2026-08-14) |
| G1-c credencial/token (V15) | **gate-done** |
| G1-d ticket PDF (camino reportes) | **gate-done** |
| G1-d.birt (BIRT 4.24 + diseño PG) | **gate-done** |
| R3.1 logo + barcode recepción (DANI) | **ingeniería done** (UAT impresora pendiente) |
| R3.2 harness testing BIRT real (`NroColaEsperaRecepBirtIT` + `ReportsResourceBirtIT`) | **hecho** (1/1 + 1/1 PASS vs `grupogea_migration`) |
| R3.3 refactor `generic-report-engine` | **hecho y commiteado** (Hospital-Reports `dev/dev`; SDD archivado) |
| R3.4 PoC AWS ECS Fargate | **hecho** (health + PDF BIRT real) |
| CU-A catálogo convenios | **gate-done** |
| CU-B post-recepción | **gate-done** |
| CU-B.1 Llamar → anunciador | **gate-done** (manual; no al confirmar recepción) |
| CU-C demanda espontánea | **gate-done** (medidor) |
| Paridad Cola A+B M1–M4 | **gate-done** |
| Anunciador WS `nuevos-llamados` | **gate-done** |
| Apagar Node N0–N5 | **gate-done DEV** |
| Cutover Api → schema `ts` (V27–V33) | **Hecho** (2026-08-25) |
| Ciclo vida `LLAMAR` (núcleo TV) | **gate-done** 2026-08-26 |
| Llamar Cola B (`serv_amb` → TV) | **gate-done** 2026-09-16 |
| Llamar atención médica (UI clínica) | **gate-done** 2026-09-16 |


---

## Lectura experta en una frase

**Este workspace cobró el tronco territorial (M1a/M1b/M1c **gate-done**).
P3 C5 smoke queda diferido. Seed/dump no cierran ABM.**

Refs: [`criterio-avance-e2e-datos.md`](../canon/criterio-avance-e2e-datos.md) ·
[`dossier-migracion.md`](../arquitectura/dossier-migracion.md) §6–7 ·
[`cierre-paridad-agi-anunciador/`](../cortes/anunciador/cierre-paridad-agi-anunciador/) ·
[`ajustes-prioridad-migracion.md`](ajustes-prioridad-migracion.md) ·
[`estado-piloto-vs-general.md`](../estado/estado-piloto-vs-general.md) ·
[`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md) ·
[`plan-cutover-api-schema-ts.md`](plan-cutover-api-schema-ts.md) ·
[`migracion-package-general.md`](../arquitectura/migracion-package-general.md) ·
[`trabajo-paralelo-equipo.md`](trabajo-paralelo-equipo.md) ·
[`mapa-menu-hospital-web.md`](../relevamiento/mapa-menu-hospital-web.md) ·
[`regla-waiver-paridad-legacy.md`](../canon/regla-waiver-paridad-legacy.md).
