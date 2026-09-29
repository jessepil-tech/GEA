# Maestros y seguridad — Nutrición

`phase_id:` **`sdd.hospital.relevamiento-nutricion.maestros`**  
Fecha: **2026-09-07**  
Padre: [`README.md`](README.md)

Tabla anti-omisión (Fase C). **Censo de internados ≠ paridad de configuración dietética.**

---

## Inventario

| Maestro / permiso | ¿Bloquea operación si falta? | ¿Existe CU/SDD? | Estado migración |
|-------------------|------------------------------|-----------------|------------------|
| Usuario + perfil menú `NUTRICION` (130000) | Sí (no entra al tile) | Shell Web `moduleOnly` | **Parcial** — tile disabled |
| Rol funcional `NUTRICIONISTA` | Sí — `BBInicioNutricion` rechaza otro rol | ninguno Identity | **No migrado** |
| `ts.personal` + `personal_servicio` (internación) | Sí — lista de centros del gate | Turnos T1 lookup login; ABM personal diferido | **Parcial** (no el vínculo internación) |
| `ts.centro_atencion` | Sí (gate Aceptar) | V28 | **DDL**; ABM diferido |
| `ts.servicio` / servicio-centro internación | Sí para filtrar sector/servicio en listados | V30 | **DDL**; ABM diferido |
| `ts.sector_int` / `sector_int_centro_ate` | Sí planilla y listados | ninguno Api (ocupación) | **No migrado** |
| `ts.nutricion` | Sí para prescribir/ajustar ítems de catálogo (indicación libre puede existir) | ninguno | **Inventario PG; Flyway Api no** |
| `ts.nutricion_no_compatible` | Condicional (valida pares incompatibles) | ninguno | **Igual** |
| Internación + cama + `tmp_censo_cama` | Sí — sin internado no hay A7 | Api solo `InternacionIdService` (ids) | **No migrado** (bloqueante de ops) |
| `ts.indicacion_int` + `det_indica_nutricion_int` | Sí para vigentes/ajustes; planilla puede arrancar de censo | ninguno | **Inventario PG; Api no** |
| `ts.paciente` / alergias | Box advertencia en listados | V31 AGI | **DDL paciente**; alergias internación **no** |
| Consultas altas/censo (130301/130302) | No para dietética | ninguno nutrición | **N/A este módulo** (admisión) |

---

## Evidencia ABM / config legacy

| Capacidad | Legacy | Destino hoy |
|-----------|--------|-------------|
| Catálogo dietas/ítems | `pages/configuracion/nutricion/nutricion.xhtml` · `BBNutricion` · `datosNutricion.xhtml` · Hibernate `ImpBusNutricion` | Ausente |
| Incompatibilidades | `nutricionNoCompatible.xhtml` · `BBNutricionNoCompatible` | Ausente |
| Buscador maestro | `buscadorNutricion.xhtml` · `BBBuscadorNutricion` | Ausente |
| Gate módulo | `inicioNutricion.xhtml` · solo nutricionista + centro | Ausente |
| Planilla | `planillaNutricion.xhtml` · `BBDietaInternado` | Ausente |
| Internados / vigentes | `nutricion.xhtml` · `indicacionesVigentes.xhtml` | Ausente |

ABM **no** es package `TS.NUTRICION`: CRUD Hibernate sobre `ts.nutricion`.  
DDL canónico: [`inventario-ddl-oracle-pg/`](../inventario-ddl-oracle-pg/) (`NUTRICION`, `NUTRICION_NO_COMPATIBLE`, …).  
Regla: no inventar `public.nutricion_agi`.

---

## Regla de cierre

No declarar el módulo NUTRICION **migrado** mientras A1–A4 (rol, gate, catálogo, internación) estén en seed o silencio.
Cada fila: **done / diferido(slug) / WAIVE**.

Slugs de deuda propuestos (aún **sin** SDD capa 4):

- `nutricion-identity-rol` — rol `NUTRICIONISTA` + menú 130000
- `nutricion-maestros` — Flyway `ts.nutricion` + ABM Config (11804)
- `internacion-censo` — ocupación/cama (prerrequisito compartido; **no** es este módulo)
