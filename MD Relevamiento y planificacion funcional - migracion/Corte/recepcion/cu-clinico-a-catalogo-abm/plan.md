---
title: Plan — CU clínico A
version: 0.1.0
status: active
owner: grupogea
last_updated: 2026-08-14
phase_id: sdd.hospital.cu-clinico-a-catalogo-abm
---

# Plan — CU-A

## Enfoque

| Capa | Decisión |
|------|----------|
| Puerto | `CatalogPort` + `JdbcCatalogAdapter` |
| API | `CatalogoConveniosResource` |
| CQRS | List/Get queries + Create/Update commands |
| UI | Página lista/form Angular |
| Flyway | No requerido (tabla ya existe) |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Pisarse con G1 | No modificar flujo recepción |
| Código duplicado | UNIQUE + DomainException DUPLICATE_ENTRY |
