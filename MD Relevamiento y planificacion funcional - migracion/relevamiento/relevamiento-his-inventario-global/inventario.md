---
title: Inventario de objetos HIS (walk 14-sep-2026)
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-14
phase_id: sdd.hospital.inventario-global-his.objetos
---

# Inventario de objetos HIS

Walk sobre `Hospital-Legacy`, `Legacy-DB/Packages`, `MENU_APLICACION`,
sidecar `Hospital-Reports`. Fuentes complementarias:
[`listado-reportes-birt-legacy.md`](../listado-reportes-birt-legacy.md),
[`relevamiento-his-orientacion/`](../relevamiento-his-orientacion/).

## Tamaño bruto del legado

Suma de artefactos. No es esfuerzo 1:1 (XML BIRT y `TBL_AUD_*` inflan líneas).

| Capa | Archivos | Tamaño | Líneas |
|------|----------|--------|--------|
| Pantallas xhtml (9 WAR) | 3.347 | 31,3 MB | 636.145 |
| Java (WAR + BUSINESS) | 9.458 | 70,3 MB | 2.199.608 |
| — beans `BB*` | 2.510 | 32,5 MB | 1.000.645 |
| Reportes `.rptdesign` | 408 (329 únicos) | 101,0 MB | ~1.652.000 |
| Packages Oracle BODY | 325 (61 negocio) | 17,6 MB | 355.729 |
| **Aprox. total** | ~13.500 | ~225 MB | ~4,8 M |

Invocaciones medidas: **2.472** xhtml con `h:`/`p:` form; **86.598** EL `#{bb*.`;
**911** `ReportManager.printReport` en 427 archivos; **1.696** FUNCTION +
**1.713** PROCEDURE en BODYs; menú HOSPITAL=2: **1.240** filas, **1.032** hojas
con `ACCION`, **37** tiles origin.

## Reportes BIRT

408 archivos / 329 basename únicos. SoT de corte: `HOSPITAL_2`.
Invocación = string `Nombre.rptdesign` en java/xhtml/xml.

Universo **a migrar** (2026-09-21): **297** basename base+GEA /
**355** archivos. **32** basename (**53** archivos) son variante de otro
cliente (`N/A`): [`listado-reportes-birt-otro-cliente.md`](../listado-reportes-birt-otro-cliente.md).
No hay `.rptdesign` `*GEA*`.

| Conjunto | N | Notas |
|----------|---|--------|
| Con caller (usados) | 231 | Denominador histórico del carril print-path (incluye otro cliente) |
| Sin caller (huérfanos) | 98 | Anotar en listado-sin-uso; no portar salvo pedido |
| Sidecar PG (14-sep cierre) | **37** | `hospital.reports.registered` |
| Usados aún abiertos | **~194** | 231 − usados ya en sidecar |

KB diseño (walk mañana 14-sep, 36 sidecar): usados 48.841 · huérfanos 28.210 ·
sidecar 7.076. Tras FormularioPedidoMedicamentos el sidecar suma ~76 KB.

### Más invocados y aún no migrados (hits del basename)

| reportId | Hits | Archivos caller | KB | Líneas |
|----------|------|-----------------|----|--------|
| HTMLDocument | 43 | 28 | 40,5 | 591 |
| ConsentimientoRecepcion | 26 | 22 | 11,9 | 244 |
| InformePac | 25 | 8 | 172 | 2.694 |
| OrdenCompra | 19 | 17 | 401 | 6.066 |
| Recibo | 19 | 18 | 285 | 4.516 |
| Partograma | 18 | 15 | 918 | 13.252 |
| informeLaboratorio | 18 | 7 | 459 | 6.970 |
| BalanceHidrico | 18 | 14 | 222 | 3.761 |
| FormularioTransferenciaMedicamentos | 17 | 17 | 403 | 5.742 |
| Certificado | 17 | 9 | 155 | 2.416 |
| IndicacionPacPISO | 12 | 11 | 479 | 9.190 |
| FacturaTicket | 14 | 9 | 135 | 2.457 |

HTMLDocument es wrapper de informes HTML, no un layout clínico típico.

### Sidecar — matices del listado `[x]`

- En sidecar y no `[x]` (scaffold): `HistoriaClinica`.
- `[x]` huérfanos (sin sidecar): p.ej. `InformeEstudio`, `ResumenAtencionAmb`,
  `FormHcPacCirugia` (14-sep).
- `EsquemaTratamientoPac`: `[x]` / sin uso vigente; diseño sí en sidecar.

## Pantallas y forms

3.347 xhtml; HOSPITAL_2 = 2.915 (87%). **2.472** con form. El menú no es el
filesystem: 1.032 hojas vs 2.222 stems de pantalla en HOSPITAL_2 (buscadores,
popups, includes).

| WAR | xhtml |
|-----|------:|
| HOSPITAL_2 | 2.915 |
| AGH | 106 |
| HOS-APP | 73 |
| AGI | 59 |
| AGP | 53 |
| seguridad | 51 |
| RECETAS | 48 |
| PROVEEDORES | 41 |

HOSPITAL_2 `pages/` (top, archivos de pantalla). `in_menu` = basename en `ACCION`.

| Carpeta | Pantallas | En menú | KB | Líneas |
|---------|----------:|--------:|---:|-------:|
| configuracion | 607 | 161 | 3.755 | 79.319 |
| buscadores | 344 | 0 | 1.478 | 29.065 |
| ambulatoria | 174 | 12 | 2.490 | 48.809 |
| laboratorio | 169 | 84 | 2.148 | 42.188 |
| farmacia | 152 | 97 | 1.668 | 33.279 |
| admisionInternados | 112 | 48 | 1.294 | 24.770 |
| guardiaYEmergencias | 106 | 9 | 1.710 | 33.310 |
| guardiaConsultorio | 104 | 7 | 1.662 | 31.332 |
| compras | 97 | 55 | 1.659 | 30.436 |
| facturacion | 95 | 42 | 831 | 16.309 |
| cirugia | 85 | 31 | 850 | 17.146 |
| liquidacionHonorario | 79 | 35 | 878 | 19.421 |
| facturacionInternado | 74 | 26 | 854 | 16.377 |
| internacion | 67 | 15 | 910 | 17.733 |
| recepcion | 61 | 40 | 833 | 15.919 |
| turnos + atencionTurno | 50 | 34 | 688 | 12.793 |
| historiaClinica | 20 | 1 | 363 | 7.853 |

Clasificados “pantalla”: 2.566 · buscadores: 370 · HOSPITAL_2 con match de menú: 935.

### Hospital-Web en el clone medido (18-ago-2026)

| Ruta | Equivalente legacy | Notas |
|------|--------------------|-------|
| `/dashboard` | inicio.xhtml grilla | Parcial — tiles |
| `/agi/recepcion` | AGI tótem | Piloto G1 |
| `/agi/espera` | Post-ticket + Llamar | CU-B / B.1 |
| `/agi/demanda-espontanea` | DEMANDA_ESPONTANEA | Medidor CU-C |
| `/recepcion/cola` | cabeceraRecepcion.xhtml | M1–M3 |
| `/recepcion/espera-amb` | colaEspera.xhtml | M4 |
| `/catalogo/convenios` | convenio.xhtml | CU-A |
| `/anunciadores` + display | Node/Vue + sala TV | Piloto |
| `/turnos/*` | agenda.xhtml y familia | **En SDD, no en este clone** |

## Beans, Java, packages

“Objeto” = beans `BB*` + HOSPITAL-BUSINESS + packages PL/SQL.

| Métrica | Valor |
|---------|------:|
| Clases `BB*` | 2.510 |
| Bindings EL `#{bb*.` | 86.598 (2.458 beans referenciados) |
| FUNCTION+PROCEDURE en BODYs | 3.409 |
| `printReport` call sites | 911 |

### Java por proyecto (top)

| Proyecto | Archivos .java |
|----------|---------------:|
| HOSPITAL-BUSINESS | 5.338 |
| HOSPITAL_2 | 2.841 |
| VALIDADORES | 311 |
| HOS-APP | 93 |
| AGP | 88 |
| RECETAS | 80 |
| ANMAT | 65 |
| AFIP | 36 |

BUSINESS ≈ 775 k líneas (lógica compartida, no UI).

### Beans más referenciados desde xhtml

| Bean | EL hits | Líneas Java |
|------|--------:|------------:|
| BBSintesisGuardia | 927 | 1.964 |
| BBParteOperatorio | 908 | 3.103 |
| BBRecepcionPaciente | 753 | 12.968 |
| BBDatosPaciente | 708 | 2.833 |
| BBIndicaciones | 698 | 6.268 |
| BBEventoHC | 614 | 4.588 |
| BBInfirmary | 582 | 2.261 |
| BBInternacion | 554 | 2.025 |
| BBSessionData | 466 | 111 |
| BBValidarAnalisisLab | 425 | 3.616 |
| BBAgendaHosApp | 355 | 4.580 |
| BBAsignacionTurnos | 337 | 2.698 |

### Packages Oracle de negocio (top por tamaño)

326 BODY: **264** `TBL_AUD_*` + **62** dominio. SoT:
`Legacy-DB/Packages` (nunca `Hospital-Legacy/RDBMS`).
Ports PG al walk: **30** `.sql` / 264 KB / 5.666 líneas — subset de reportes,
no el package entero.

| Package BODY | KB | Líneas | Fn | Proc |
|--------------|---:|-------:|---:|-----:|
| LABORATORIO | 1.585 | 32.369 | 162 | 92 |
| FARMACIAS | 1.296 | 24.487 | 153 | 10 |
| ATENCION | 1.233 | 25.020 | 115 | 16 |
| FACTURACION | 1.071 | 21.326 | 107 | 44 |
| TURNOS | 992 | 16.766 | 77 | 22 |
| LIQUIDACION_HONORARIO | 866 | 18.468 | 68 | 40 |
| CAJAS | 755 | 16.140 | 51 | 17 |
| INTERFACES | 715 | 13.940 | 80 | 13 |
| FACTURACION_INTERNADO | 658 | 10.675 | 23 | 9 |
| PERSONAS | 617 | 14.721 | 54 | 12 |
| COMPRAS | 613 | 12.168 | 63 | 25 |
| RECEPCIONES | 560 | 10.749 | 46 | 11 |

PERSONAS / GENERAL aparecen en **153** y **142** diseños BIRT: tronco, no un módulo.

### Destino migrado (líneas de producto nuevo, walk)

| Repo (src) | Archivos | KB | Líneas |
|------------|---------:|---:|-------:|
| Hospital-Reports Java | 57 | 139 | 4.036 |
| Hospital-Reports diseños PG | 36→37 | 7.076+ | 114.620+ |
| packages-pg | 30 | 264 | 5.666 |
| Hospital-API Java | 402 | 675 | 17.759 |
| Hospital-Web ts/html/scss | 250 | 428 | 12.466 |
| Hospital-Identity Java | 285 | 461 | 12.161 |

## Esfuerzo restante (1 stream, honesto)

Ritmo BIRT sep-2026: ~2 reportes usados/día agente+humano; outliers 1–2 días.
**No** extrapolar UI con ese número.

| Carril | Hecho | Resta | Likely 1 stream | Notas |
|--------|-------|-------|-----------------|-------|
| Reportes usados | 37 / 231 (~16%) | ~194 diseños · ~41 MB XML | 6–8 meses | Saltar 98 huérfanos salvo pedido |
| Pantallas menú | Piloto + SDD Turnos | 1.032 hojas · 37 módulos | 2–4 años / 12–18 meses ×3 | Denominador = menú |
| Packages negocio | 30 scripts PG (subset) | 61 BODY · 3.409 firmas | On-demand por CU | Prohibido portar `TBL_AUD` de una |
| Plataforma | Identity, ts, BIRT, ECS PoC | UAT impresora, sync colas, P1–P5 | Semanas | No bloquea backlog de módulos |

Optimistic / likely / pessimistic (días-persona, **no sumar** — van en paralelo):

| Carril | Opt. | Likely | Pes. |
|--------|-----:|-------:|-----:|
| BIRT usados restantes (~194) | 80 | 160 | 290 |
| Packages on-demand | 40 | 90 | 180 |
| 1 módulo tipo Turnos (resto T6) | 15 | 30 | 60 |
| 36 módulos HIS sin A–C | 540 | 1.080 | 2.160 |

Likely BIRT ≈ 0,8 d/reporte usado; módulos HIS ≈ 6–12 semanas/módulo × 36.
