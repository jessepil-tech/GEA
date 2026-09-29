---
title: Matriz — Administración General ↔ destino
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.relevamiento-maestros.matriz
---

# Matriz — legacy ↔ destino

`phase_id:` **`sdd.hospital.relevamiento-maestros.matriz`**  
Fecha: **2026-09-16**  
Padre: [`README.md`](README.md)

Estados: **Migrado** | **Parcial** | **Diferido(`slug`)** | **No migrado** | **WAIVE** | **N/A**.

---

## 1. Persistencia (`ts`)

| Concepto | Legacy | Destino | Estado |
|----------|--------|---------|--------|
| Centro | `CENTRO_ATENCION` | PG `ts.centro_atencion` | **DDL**; ABM **Diferido(`maestros-m1a-centro`)** capa 4 abierta |
| Servicio | `SERVICIO` | `ts.servicio` | **DDL**; ABM **Diferido(`maestros-m1b-servicio`)** |
| Vínculo | `SERVICIO_CENTRO` | `ts.servicio_centro` | **Parcial**; ABM **Diferido(`maestros-m1b-servicio`)** |
| Personal | `PERSONAL` | T1 | **Parcial** (no ABM 10208) |
| Paciente | `PACIENTE` | V31 + AGI | **Uso**; ABM **Diferido(M4)** |
| Convenio | `CONVENIO` | CU-A + V31 | **Parcial** |
| Prestación | nomenclador | V31 | **DDL**; ABM **Diferido(M6)** |
| Anunciador | `ANUNCIADOR` / terminal | P3 | **Parcial**; C5 **Diferido(`anunciador-agi-config-abm-c5`)** |
| Parámetros | `PARAM_*` | — | **No migrado** |
| Schema `public` de dominio | — | **Prohibido** | **N/A** |

El clone `Hospital-Api` de **esta** máquina lista Flyway hasta **V25**. Los Vnn de
Turnos/padres viven en el SDD y en otras copias del repo: no inferir “no existe DDL”
por este clone. Remedir al abrir M1.

## 2. UI / menú

| Concepto | Legacy | Destino | Estado |
|----------|--------|---------|--------|
| Tile | `ADMINISTRACION_GENERAL_NA` → `configuracion/inicio` | `hospital-menu.catalog.ts` misma clave | **Parcial** (3 hijos: Convenios on; terminales/params pending) |
| Árbol 223 hojas | `pv:menu` perfil | Hardcode + `pending()` | **No migrado** (M2 Identity) |
| Convenios | `convenio.xhtml` | `/catalogo/convenios` | **Parcial** CU-A |
| Hab/grilla turnos | bajo `modulos/turnos` de **este** tile | rutas `/configuracion/hab-*` colgadas del tile TURNOS | **Migrado** T2–T4 (orientación: W6 consciente) |

## 3. API / packages

| Firma / capacidad | Destino | Estado |
|-------------------|---------|--------|
| ABM centro/servicio | ningún Resource dedicado | **No migrado** → [`maestros-m1a-centro`](../../cortes/maestros/maestros-m1a-centro/) / [`maestros-m1b-servicio`](../../cortes/maestros/maestros-m1b-servicio/) |
| Catálogo convenios | `Hospital-Api` `/v1/catalogo/convenios` | **Parcial** |
| PERSONAS / GENERAL | helpers BIRT V11; `next_id` V10 | **On-demand** |
| Check hab turnos | T2 + job | **Parcial** / job diferido |

## 4. Jobs / print-path

| Capacidad | Destino | Estado |
|-----------|---------|--------|
| Vigencia `hab_turnos_*` | `CheckHabTurnosJob` | **Diferido**(jobs) |
| Mail/SMS desde servers del tile | `Envio*Job` / `ProcesarSmsJob` | **Diferido**(jobs) |
| BIRT de config | sidecar bajo demanda | **N/A** hasta CU con printReport |

## 5. WAIVE

Ningún ABM del tronco se WAIVE: el HIS los tiene. Consultas de facturación y
interfaces de migración se **diferirán**, no se waiven.
