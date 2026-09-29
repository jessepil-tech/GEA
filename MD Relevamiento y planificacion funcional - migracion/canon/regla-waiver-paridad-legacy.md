---
title: Regla — WAIVE vs paridad legacy
status: canonical
owner: grupogea
last_updated: 2026-08-20
phase_id: sdd.hospital.regla-waiver-paridad-legacy
indice_blurb: Waivers de paridad funcional — capa 1
---
# Regla de migración — WAIVE vs paridad legacy

`phase_id:` **`sdd.hospital.regla-waiver-paridad-legacy`**  
Fecha: 2026-08-14 · **ampliada 2026-08-18** · **aclarada 2026-08-20**  
**Estado:** **CANÓNICA** (aprobada 2026-08-14; reforzada 2026-08-18)

## Principio de producto (todo el proyecto)

La migración es **paridad funcional 1:1 con el legacy**: **no se pierden
funcionalidades** al pasar a Quarkus / Angular / Postgres.

| Pregunta típica en Clarify | Respuesta por defecto |
|----------------------------|------------------------|
| ¿Migrar feature X del legacy? | **Sí**, si el legacy la tiene |
| ¿Podemos omitirla en este slice? | Solo **diferir** con SDD propio + link; nunca “no hace falta” |
| ¿WAIVE por velocidad del piloto? | **Prohibido** si legacy la tiene |

“1:1” = **mismo comportamiento de negocio / misma capacidad de usuario**.  
No exige pixel de cada `.xhtml` ni el protocolo Socket.IO. Sí exige que el usuario
pueda hacer **lo mismo**.

**Paridad de schema (DDL)** es regla **aparte y prioritaria** desde 2026-08-20:
el proceso usa la estructura del **PostgreSQL migrado** (`ts`), no modelos
simplificados inventados. Ver
[`regla-ddl-postgres-migrado.md`](regla-ddl-postgres-migrado.md).

Mapa de capas (relevamiento modular = capa 3, antes de evaluar un módulo):
[`gobierno-migracion.md`](gobierno-migracion.md).

## Regla WAIVE (estricta)

Antes de marcar un RF / CA / ítem de verify como **WAIVE**, **opcional** o
**fuera de alcance**:

1. **Contrastar el legacy** (xhtml/JSF, bean, SP, `.rptdesign`, Anunciador, etc.).
2. **Dejar evidencia** (ruta de archivo o descripción del comportamiento).

| Hallazgo | Acción permitida |
|----------|------------------|
| Legacy **no** tiene la función | WAIVE OK (con evidencia) |
| Legacy **sí** la tiene | **Prohibido** WAIVE por comodidad del slice → **implementar ahora** **o** **diferir** con carpeta SDD propia (`…-b1-…`, etc.) linkeada desde spec/tasks/verify del padre |
| Solo hay enlace / navegación a otra pantalla | **No** cuenta como paridad del acto de negocio |

**Diferir ≠ descartar.** El slug diferido es deuda de paridad obligatoria hasta gate-done o WAIVER firmado por **negocio** (no por el equipo de desarrollo solo).

## Disciplina de diferidos (cola corta)

`diferido(slug)` es un pagaré: carpeta SDD hija + fila en verify del padre + fila en
[`backlog-orden-2026-08-14.md`](../planificacion/backlog-orden-2026-08-14.md) (o el orden vigente).

| Hacer ahora (no diferir) | Diferir con slug | Prohibido |
|--------------------------|------------------|-----------|
| Acto del slice (el usuario completa *esta* pantalla/flujo) | Hojas/CU de **otro** corte del mismo módulo | Silencio, “después”, WAIVE por velocidad |
| Mapa mental de lo que se entrega (padre de menú, tile→módulo, gate de contexto del módulo) | El resto del HIS (~1.200 hojas, 37 módulos) | Cementerio: padre PASS con >3 hijos abiertos sin fecha en backlog |
| Chrome xhtml de la pantalla que se declara hecha | Dependencias que aún no existen (SMS, BIRT turno, ABM equipo…) | Spec del módulo entero “por si acaso” |

**Volver:** el hijo se cobra **antes** de abrir otro módulo (o entra explícito al orden de la semana). Si no se toca en el plazo del backlog, producto decide: entra o deja de ser paridad (firma negocio — no el equipo solo).

No es regla de “atender el HIS entero en cada PR”. Es regla de **no perder el pagaré**.

## Ejemplo canónico

| Superficie | ¿Llamar/Anunciar? |
|------------|-------------------|
| AGI tótem | No (ticket + cola) |
| HOSPITAL_2 recepción `btnLlamar` | Sí → Anunciador |
| CU-B lista | gate-done |
| RF-4 | **Diferido** → [`cu-clinico-b1-llamar-recepcion/`](../cortes/recepcion/cu-clinico-b1-llamar-recepcion/) — **no** WAIVE |

## Ejemplo Anunciador Node (2026-08-18)

Apagar Node **no** autoriza perder ocupación, logos/fondo ni diccionario/TTS:
legacy Vue los tiene → cortes **N1–N3** en [`apagar-anunciador-node/`](../cortes/anunciador/apagar-anunciador-node/).

## Dónde aplica

**Todo** Hospital (piloto, recepción, anunciador, BIRT, identidad, Fase 4, …).

## Proceso anti-gap (Clarify + matriz)

Checklist de capacidades / ciclo de vida / slug hijo obligatorio:  
[`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md).

Mapa menú Hospital-Web ↔ SDD ↔ legacy:  
[`mapa-menu-hospital-web.md`](../relevamiento/mapa-menu-hospital-web.md).

Ejemplo deuda abierta (B.1/M2 solo cubrieron el INSERT):  
[`ciclo-vida-llamado-anunciador/`](../cortes/anunciador/ciclo-vida-llamado-anunciador/).

Ver también: [`trabajo-paralelo-equipo.md`](../planificacion/trabajo-paralelo-equipo.md) § WAIVE.
