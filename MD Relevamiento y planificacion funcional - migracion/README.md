---
title: Documentación del programa Hospital
status: active
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.docs-indice
---
# Documentación del programa Hospital

## Taxonomía (opción B, 2026-09-15)

| Carpeta | Qué hay |
|---------|--------|
| [`canon/`](canon/) | Reglas y procesos |
| [`planificacion/`](planificacion/) | Backlog, alcance, cutover |
| [`relevamiento/`](relevamiento/) | Inventarios del legacy |
| [`estado/`](estado/) | Snapshots y deudas |
| [`arquitectura/`](arquitectura/) | Dossier, Identity, BIRT, despliegue |
| [`cortes/`](cortes/) | SDD de trabajo, agrupados por stream (turnos · anunciador · recepción · plataforma) |
| [`spikes/`](spikes/) | Golden master y spikes |
| [`sdd/`](sdd/) | Punteros a las rutas viejas (no editar) |

Palabras (corte ≠ stream ≠ carril; gate ≠ compuerta): [`canon/glosario-migracion.md`](canon/glosario-migracion.md).  
Cifras (un dueño por magnitud): [`canon/datos-canonicos.md`](canon/datos-canonicos.md).

## Canon (generado)

<!-- CANON-INDICE:BEGIN -->

Catálogo **generado** desde el front-matter de `docs/canon/*.md`. No editar a mano. Regenerar: `python3 tools/generar-indice-docs.py`.

| Documento | status | Qué es |
|-----------|--------|--------|
| [`canon/criterio-avance-e2e-datos.md`](canon/criterio-avance-e2e-datos.md) | `canonical` | E2E permanente; escritura cobrada en ts; copia Oracle ≠ ABM |
| [`canon/cursor-team-rules.md`](canon/cursor-team-rules.md) | `active` | Texto para pegar en Team Rules + mapa de rules/skills |
| [`canon/datos-canonicos.md`](canon/datos-canonicos.md) | `canonical` | Registro de magnitudes — un dueño por cifra; los demás citan |
| [`canon/glosario-migracion.md`](canon/glosario-migracion.md) | `canonical` | Corte / stream / carril / gate / compuerta; dónde se escribe cada cosa |
| [`canon/gobierno-migracion.md`](canon/gobierno-migracion.md) | `canonical` | Espina: capas de gobierno (paridad → relevamiento → SDD → backlog) |
| [`canon/loop-migracion-corte.md`](canon/loop-migracion-corte.md) | `canonical` | Loop de migración de un corte (runbook de ejecución) |
| [`canon/proceso-sdd-paridad-completa.md`](canon/proceso-sdd-paridad-completa.md) | `active` | Proceso — SDD con paridad completa (anti-gap) |
| [`canon/regla-birt-columnas-minusculas.md`](canon/regla-birt-columnas-minusculas.md) | `canonical` | Bindings BIRT / columnas JDBC en minúsculas (PG) |
| [`canon/regla-ddl-postgres-migrado.md`](canon/regla-ddl-postgres-migrado.md) | `canonical` | DDL = schema ts; sin public ni *_agi de dominio |
| [`canon/regla-evidencia-ejecutable.md`](canon/regla-evidencia-ejecutable.md) | `canonical` | Regla — evidencia ejecutable (verify verificable, no narrado) |
| [`canon/regla-instalacion-referencia.md`](canon/regla-instalacion-referencia.md) | `canonical` | Regla — instalación de referencia (paridad ¿con cuál de los 16 clientes?) |
| [`canon/regla-migracion-reportes-birt.md`](canon/regla-migracion-reportes-birt.md) | `canonical` | Migración incremental de reportes BIRT (R5+) |
| [`canon/regla-no-funcionales-migracion.md`](canon/regla-no-funcionales-migracion.md) | `canonical` | Regla — no funcionales en el gate (tiempo, volumen, concurrencia) |
| [`canon/regla-page-chrome-hospital-web.md`](canon/regla-page-chrome-hospital-web.md) | `active` | Regla — Page chrome Hospital-Web (breadcrumb, título, dark mode) |
| [`canon/regla-paridad-acceso-auditoria.md`](canon/regla-paridad-acceso-auditoria.md) | `canonical` | Regla — paridad de acceso y trazabilidad (quién puede y quién hizo) |
| [`canon/regla-paridad-logica-plsql.md`](canon/regla-paridad-logica-plsql.md) | `canonical` | Regla — paridad de lógica PL/SQL → servicio (golden master o diferido) |
| [`canon/regla-paridad-orientacion-visual.md`](canon/regla-paridad-orientacion-visual.md) | `active` | Regla — Paridad de orientación y visual (mapa mental + look) |
| [`canon/regla-paridad-ui-legacy.md`](canon/regla-paridad-ui-legacy.md) | `active` | Regla — Paridad UI con legacy (contrato + criterio) |
| [`canon/regla-playwright-migracion.md`](canon/regla-playwright-migracion.md) | `canonical` | Regla — Playwright en la migración Hospital |
| [`canon/regla-waiver-paridad-legacy.md`](canon/regla-waiver-paridad-legacy.md) | `canonical` | Waivers de paridad funcional — capa 1 |
| [`canon/relevamiento-modular-funcional.md`](canon/relevamiento-modular-funcional.md) | `canonical` | Relevamiento modular (config→maestros→operación) — capa 3 |

<!-- CANON-INDICE:END -->

## Índice rápido

### Programa y estado

| Documento | Descripción |
|-----------|-------------|
| [repos.md](arquitectura/repos.md) | Repos ADO, roles y puertos |
| [sdd/criterio-avance-e2e-datos.md](canon/criterio-avance-e2e-datos.md) | **Criterio:** E2E permanente; INSERT del CU cobra escritura; copia Oracle ≠ ABM |
| [sdd/estado-piloto-vs-general.md](estado/estado-piloto-vs-general.md) | Snapshot de lo cobrado (nombre histórico “piloto”) |
| [sdd/relevamiento-node-anunciador/](relevamiento/relevamiento-node-anunciador/) | **Ejemplo dorado** relevamiento modular (Anunciador + Tótem) |
| [sdd/relevamiento-turnos/](relevamiento/relevamiento-turnos/) | Relevamiento modular **Turnos** (capa 3; T0 hecho 2026-08-27) |
| [sdd/relevamiento-nutricion/](relevamiento/relevamiento-nutricion/) | Relevamiento modular **Nutrición** (capa 3; T0 hecho 2026-09-07) |
| [sdd/relevamiento-maestros/](relevamiento/relevamiento-maestros/) | Relevamiento modular **Administración General / ABM** (capa 3; T0 hecho 2026-09-16) |
| [sdd/turnos-maestros-personal/](cortes/turnos/turnos-maestros-personal/) | **T1** spec maestros identidad turno (active) |
| [sdd/anunciador-agi-config-abm/](cortes/anunciador/anunciador-agi-config-abm/) | **P3** ABM anunciador + terminal AG (cerrar satélite; Clarify propuesto) |
| [sdd/cierre-paridad-agi-anunciador/](cortes/anunciador/cierre-paridad-agi-anunciador/) | **Checklist cierre** tótem + TV vs legacy |
| [sdd/cu-llamar-atencion-medica/](cortes/recepcion/cu-llamar-atencion-medica/) | Writer clínico anunciador (**gate-done** 2026-09-16) |
| [sdd/backlog-orden-2026-08-14.md](planificacion/backlog-orden-2026-08-14.md) | Orden de trabajo vigente |
| [sdd/mapa-menu-hospital-web.md](relevamiento/mapa-menu-hospital-web.md) | Mapa de menú / UI (v1.3) |
| [sdd/arbol-mapeo-menu-legacy-web.md](relevamiento/arbol-mapeo-menu-legacy-web.md) | Árbol menú Legacy ↔ Web (M1/M2) |
| [sdd/trabajo-paralelo-equipo.md](planificacion/trabajo-paralelo-equipo.md) | Carriles paralelos de equipo |
| [sdd/ajustes-prioridad-migracion.md](planificacion/ajustes-prioridad-migracion.md) | Ajustes de prioridad |
| [sdd/gobierno-migracion.md](canon/gobierno-migracion.md) | **Espina:** capas de gobierno (paridad → relevamiento → SDD → backlog) |
| [sdd/glosario-migracion.md](canon/glosario-migracion.md) | **Glosario:** corte / stream / carril / gate / compuerta; dónde se escribe cada cosa |
| [sdd/datos-canonicos.md](canon/datos-canonicos.md) | **Cifras:** un dueño por magnitud; los demás citan |
| [sdd/cursor-team-rules.md](canon/cursor-team-rules.md) | Rules/skills Cursor + texto para Team |
| [sdd/relevamiento-modular-funcional.md](canon/relevamiento-modular-funcional.md) | Relevamiento modular (config→maestros→operación) — capa 3 |
| [sdd/proceso-sdd-paridad-completa.md](canon/proceso-sdd-paridad-completa.md) | SDD de implementación (anti-gap) — capa 4 |
| [sdd/loop-migracion-corte.md](canon/loop-migracion-corte.md) | **Runbook:** ocho pasos de un corte (reservas, universo firmado, fixture, evidencia) |
| [../tools/verificar-sdd.sh](../tools/verificar-sdd.sh) | **Linter del canon:** audita un slug o los 72; `FAIL` = el gate no cierra |
| [../tools/indice-legacy.sh](../tools/indice-legacy.sh) | **Índice del legacy:** xhtml → beans → packages → BIRT; `--semilla` acota el corte |
| [../tools/hook-verificar-sdd.sh](../tools/hook-verificar-sdd.sh) | Hook `stop`: exige el linter en los slugs tocados |
| [../tools/instalar-hook.sh](../tools/instalar-hook.sh) | Registra el hook si se abre una carpeta padre (`--estado` / `--user`); abriendo este repo como raíz no hace falta |
| [sdd/indice-legacy/](relevamiento/indice-legacy/) | Índice generado (TSV fuera de git; README con fuentes y métricas) |
| [sdd/regla-evidencia-ejecutable.md](canon/regla-evidencia-ejecutable.md) | **Regla:** ledger de evidencia; afirmación sin artefacto = silencio |
| [sdd/regla-waiver-paridad-legacy.md](canon/regla-waiver-paridad-legacy.md) | Waivers de paridad funcional — capa 1 |
| [sdd/regla-paridad-orientacion-visual.md](canon/regla-paridad-orientacion-visual.md) | **Regla:** mapa mental HIS (menú/tile/contexto); look Verona no se clona |
| [sdd/relevamiento-his-orientacion/](relevamiento/relevamiento-his-orientacion/) | Relevamiento orientación HIS (capa 3b; O1 dump **hecho** 31-ago: árbol de menú por perfil) |
| [relevamiento-his-orientacion/cobertura.md](relevamiento/relevamiento-his-orientacion/cobertura.md) | Scorecard módulos: no hay % del HIS; **3/37 origin** con A–C (Turnos + Nutrición + Administración General) + satélite Anunciador |
| [relevamiento-his-orientacion/dependencias-modulos.md](relevamiento/relevamiento-his-orientacion/dependencias-modulos.md) | Roadmap: circuitos HIS y dependencias conocidas |
| [sdd/relevamiento-integraciones-externas/](relevamiento/relevamiento-integraciones-externas/) | Interfaces con terceros (AFIP · ANMAT · 17 validadores · Bionexo · Alfabeta · laboratorio · PACS): esfuerzo real vs. conteo bruto |
| [sdd/relevamiento-procesos-programados/](relevamiento/relevamiento-procesos-programados/) | **82 jobs** del legacy y el cruce con lo declarado cerrado: tres circuitos `gate-done` incompletos |
| [sdd/alcance-proyectos-migracion.md](planificacion/alcance-proyectos-migracion.md) | **Alcance:** proyectos dentro y fuera (recorte del cliente), sin clasificar y consecuencias a firmar |
| [sdd/preguntas-alcance-cliente.md](planificacion/preguntas-alcance-cliente.md) | Hoja para la reunión: 4 decisiones + 6 aclaraciones, con qué cambia según la respuesta |
| [sdd/destino-seguridad-identity.md](planificacion/destino-seguridad-identity.md) | **Destino:** la app de Seguridad se absorbe en Identity + `Identity-Web`; mapeo, qué no se absorbe y 2 defectos abiertos |
| [../tools/consultas-relevamiento.sql](../tools/consultas-relevamiento.sql) | Consultas a la copia Oracle: jobs activos, validadores, roles funcionales, volumen real |
| [sdd/regla-paridad-ui-legacy.md](canon/regla-paridad-ui-legacy.md) | Paridad chrome de pantalla (xhtml / labels / buscadores) |
| [sdd/regla-ddl-postgres-migrado.md](canon/regla-ddl-postgres-migrado.md) | **Regla prioritaria:** DDL = PG migrado (`ts`) |
| [sdd/regla-paridad-logica-plsql.md](canon/regla-paridad-logica-plsql.md) | **Regla:** cálculo portado = golden master contra Oracle 11.2, o diferido |
| [sdd/regla-no-funcionales-migracion.md](canon/regla-no-funcionales-migracion.md) | **Regla:** tiempo, volumen y concurrencia en el gate (no PASS en base vacía) |
| [sdd/regla-instalacion-referencia.md](canon/regla-instalacion-referencia.md) | **Regla:** paridad ¿con cuál de los 16 clientes? (313 ramas `esClienteX` en el código) |
| [sdd/regla-paridad-acceso-auditoria.md](canon/regla-paridad-acceso-auditoria.md) | **Regla:** quién puede ejecutar el CU y qué queda registrado (264 tablas auditadas) |
| [sdd/regla-birt-columnas-minusculas.md](canon/regla-birt-columnas-minusculas.md) | **Regla:** bindings BIRT / columnas JDBC en minúsculas (PG) |
| [sdd/regla-migracion-reportes-birt.md](canon/regla-migracion-reportes-birt.md) | **Regla:** migración incremental de reportes BIRT (R5+) |
| [sdd/playbook-migracion-reporte-birt.md](arquitectura/playbook-migracion-reporte-birt.md) | Playbook por `reportId` + bitácora de pitfalls (viva) |
| [sdd/catalogo-tipos-oracle-pg.md](relevamiento/catalogo-tipos-oracle-pg.md) | Únicos cambios de tipo Oracle→PG permitidos |
| [sdd/pendientes-solo-oracle.md](estado/pendientes-solo-oracle.md) | Objetos aún solo en Oracle |
| [sdd/inventario-ddl-oracle-pg/](relevamiento/inventario-ddl-oracle-pg/) | Inventario/diff tablas-columnas (2026-08-20) |
| [sdd/plan-cutover-api-schema-ts.md](planificacion/plan-cutover-api-schema-ts.md) | Cutover Api → `ts` (**hecho** V27–V33) |
| [sdd/retiro-tablas-piloto-public.md](arquitectura/retiro-tablas-piloto-public.md) | Plan retiro `public.*` / `*_agi` (nivel B) |

### Arquitectura y migración

| Documento | Descripción |
|-----------|-------------|
| [dossier-migracion.md](arquitectura/dossier-migracion.md) | Dossier general |
| [mapa-productos-destino.md](arquitectura/mapa-productos-destino.md) | Productos destino |
| [contrato-api-identidad.md](arquitectura/contrato-api-identidad.md) | Contrato Identity ↔ consumidores |
| [migracion-identidad.md](arquitectura/migracion-identidad.md) | Migración de identidad |
| [analisis-identity-separado.md](arquitectura/analisis-identity-separado.md) | Identity como repo separado |
| [despliegue-hospital.md](arquitectura/despliegue-hospital.md) | Despliegue |
| [despliegue-hospital-reports-aws.md](arquitectura/despliegue-hospital-reports-aws.md) | AWS ECS Fargate — sidecar Reports (PoC hecha) |
| [plan-migracion-packages-cqrs.md](arquitectura/plan-migracion-packages-cqrs.md) | Packages PL/SQL → CQRS |
| [migracion-package-general.md](arquitectura/migracion-package-general.md) | Package GENERAL / IDs |
| [analisis-oracle19-vs-postgres.md](arquitectura/analisis-oracle19-vs-postgres.md) | Oracle vs Postgres |
| [birt-runtime-destino.md](arquitectura/birt-runtime-destino.md) | Runtime BIRT / reportes (R0–R7) |
| [sdd/register-report-sidecar.md](arquitectura/register-report-sidecar.md) | Cómo registrar un reporte en el sidecar |
| [sdd/sidecar-reports-avance.md](estado/sidecar-reports-avance.md) | Snapshot avance sidecar |
| [sdd/r5-historia-clinica-epicurisis-gye/](cortes/plataforma/r5-historia-clinica-epicurisis-gye/) | R5 oleada HistoriaClinica + EpicrisisGYE |
| [hallazgos-golden-master-vpn-2026-08-14.md](arquitectura/hallazgos-golden-master-vpn-2026-08-14.md) | Hallazgos golden master |

### Cortes (`docs/cortes/`)

`docs/sdd/` queda como **punteros** (opción B).

Ver listado vivo en [estado-piloto-vs-general.md](estado/estado-piloto-vs-general.md).
Carpetas típicas: `identidad-oleada-a/`, `piloto-agi-anunciador/`, `piloto-agi-g1*/`,
`cu-clinico-*`, `paridad-recepcion-cola/`, `piloto-agi-g1-d-birt/`, etc.

## Relación con repos producto

Cada servicio mantiene un `docs/sdd/README.md` **local** con su alcance y un enlace a este
índice. El detalle de slices que cruzan Api + Web + Reports + Identity vive **aquí**.
