---
title: Relevamiento — Administración General (maestros / ABM)
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.relevamiento-maestros
---

# Relevamiento — Administración General (maestros / ABM)

`phase_id:` **`sdd.hospital.relevamiento-maestros`**  
Estado: **active** · **T0 2026-09-16** · profundidad 2026-09-17  
Capa 3: [`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md) ·
[`gobierno-migracion.md`](../../canon/gobierno-migracion.md)

Entregable de relevamiento modular (**greenfield**). No sustituye SDD de implementación.
**No hubo demo de negocio** de este módulo: evidencia = menú O1 (`MENU_APLICACION`) +
árbol [`dump-menu-arbol-hospital.md`](../relevamiento-his-orientacion/dump-menu-arbol-hospital.md).
`HOSPITAL-develop` **no** está en este workspace: xhtml/beans se abrieron desde `Hospital-Legacy` al firmar M1a/M1b. Índice `indice-legacy` generado 2026-09-16.

| Doc | Rol |
|-----|-----|
| [pipeline.md](pipeline.md) | Configuración → consumo por otros módulos (A1–A9) |
| [maestros.md](maestros.md) | Tronco / seguridad / ¿bloquea si falta? |
| [inventario.md](inventario.md) | Familias de ABM (223 hojas) |
| [matriz.md](matriz.md) | Destino Api/Web ↔ legado |
| [cortes.md](cortes.md) | M0–M9 por dependencia (**no** spec) |

## Contexto

| Pieza | Rol |
|-------|-----|
| Tile menú `ADMINISTRACION_GENERAL_NA` **id=10000** | PNG origin: ADMINISTRACION GENERAL · `ACCION` `/pages/configuracion/inicio` |
| Hojas bajo el tile | **262** nodos · **223** con `ACCION` · **39** carpetas (conteo dump `dump-menu-aplicacion.csv`, 2026-09-16) |
| Walk filesystem | [`arbol-dependencias.md`](../relevamiento-his-inventario-global/arbol-dependencias.md): `pages/configuracion` **224** hojas / **562** beans — mismo orden de magnitud; no es el denominador de menú |
| Padres de 1.er nivel | `configuracion_general` (22) · `configuracion_operativa` (36) · `dominios_medicos` (23) · `dominios_enfermeria` (5) · `nomenclador` (6) · `facturacion` (24) · `interfaces_migracion` (6) · `modulos` (101) |
| Packages tronco | `TS.PERSONAS` · `TS.GENERAL` (on-demand por firma; **no** portar el BODY entero) |
| Destino Web | Tile `ADMINISTRACION_GENERAL_NA` → Convenios habilitado; resto pending. Hab/horarios/grilla Turnos cuelgan hoy del tile **TURNOS** (paridad de orientación: en HIS viven bajo `modulos/turnos` de este tile) |
| Destino Api | DDL `ts.*` de padres (centro/servicio/convenio/paciente/prestación) citado en SDD Turnos/CU-A; **ABM** salvo convenio y satélites **no** es paridad |

**Anti-sesgo:** este tile **es** A1–A5 del resto del HIS. No hay “operación del día” propia
salvo consultas de facturación, padrones y las hojas de agenda que **ya** relevó Turnos
(`modulos/turnos` = 101 hojas mezclan ABM de depósito/compras/admisión con T2–T4).

**Regla de corte:** no migrar las 223 hojas en un SDD. Tronco (centro / servicio /
personal / paciente / convenio / prestación) **sin** esperar el tile entero
([`dependencias-modulos.md`](../relevamiento-his-orientacion/dependencias-modulos.md)).

## Veredicto (Fase E) — 2026-09-16

| Pregunta | Respuesta |
|----------|-----------|
| ¿Viable con reglas actuales? | **Sí con prerrequisitos** — DDL de padres existe en el programa; Identity abre el tile; ABM 1:1 casi ausente |
| ¿Paridad de configuración? | **No** — seed/bootstrap ≠ ABM. Convenios = slice CU-A. Anunciador = P3. Hab turnos = T2 (otro stream) |
| Prerrequisitos bloqueantes | Perfil que ve `ADMINISTRACION_GENERAL_NA`; writers en `ts` con fila del CU; no dump Oracle como prueba |
| Primer CU recomendado | **M1a** ABM `centro_atencion` (desbloquea FK); luego M1b servicio + `servicio_centro` |
| Fuera de alcance inmediato | Las 101 hojas `modulos` de Farm/Compras/Cirugía/HC forms; interfaces de migración; padrones como lote |
| ¿Abrir spec de “migrar Administración General”? | **No** — cortes M1+; techo 8 xhtml |

## Profundidad de análisis

El pipeline A1–A9 está cerrado. El grafo no: PERSONAS/GENERAL se portan **on-demand**,
el índice no cierra el BODY, y las 223 hojas no son un call graph. Los hijos M1+ no
heredan esta tabla: cada corte cierra las capas que toca
([`loop-migracion-corte.md`](../../canon/loop-migracion-corte.md) paso 3).

| Capa | Qué cerraría | Estado | Evidencia |
|------|--------------|--------|-----------|
| Pipeline A1–A9 | Etapas configuración → consumo por otros módulos | cerrado | [`pipeline.md`](pipeline.md) |
| Escritores cruzados | Quién más escribe centro / servicio / convenio / personal / paciente | muestra | Turnos T1–T4 y CU-A leen (y a veces escriben) las mismas `ts.*`; AGI/recepción consumen; no inventario cerrado de writers |
| Procesos programados | Cada job se porta / difiere / N/A | muestra | Nombrados en [`inventario.md`](inventario.md) (`CheckHabTurnosJob`, `Envio*Job`, `MigraPersonalV8AV9Job`); decisión en [`relevamiento-procesos-programados/`](../relevamiento-procesos-programados/) — no `--jobs maestros` cerrado en este A–C |
| Firmas del package | Universo `TS.PERSONAS` / `TS.GENERAL` (no una selección) | muestra | [`inventario.md`](inventario.md) § Packages: on-demand por firma del CU; prohibido traducir el BODY |
| Integridad referencial | FK de centro/servicio/convenio y padres reales | muestra | Padres en [`maestros.md`](maestros.md); ABM M1+ cierra fila a fila; FKs de otros streams no levantadas aquí |
| Reportes e integraciones | BIRT RRHH e interfaces de migración (10701–10712) | muestra | A9 nombra BIRT de otra rama de menú; 6 hojas `interfaces_migracion` en inventario — M9 **N/A** como corte de este stream |

**Riesgo:** tratar el listado de convenios + seed de centros como “maestros migrados”.
Eso es un slice + bootstrap, no el pipeline (perfil → catálogos globales → centro →
vínculos → nomenclador/convenio → consumo operativo).

**Firma de proceso:** Fases A–C documentadas. Seed **no** cuenta como cierre.
Capa 4: **M1a/M1b/M1c + hijo especialidad-serv gate-done**. No hay spec del módulo entero.
