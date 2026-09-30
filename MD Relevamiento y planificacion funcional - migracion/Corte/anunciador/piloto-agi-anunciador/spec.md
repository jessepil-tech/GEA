# Spec — Piloto AGI + ANUNCIADOR

`phase_id:` `sdd.hospital.piloto-agi-anunciador`  
Estado: **reviewed** (2026-08-13) — alineado a inventario CU #1 + estrategia Core.

## Problema

La oleada A de Identity está hecha, pero hace falta el piloto de **negocio** en
Quarkus/Angular para **medir** esfuerzo (Fase 3 del dossier). No se trata de cablear
AGI.war / anunciadorVue legacy a Identity como migración terminada.

## Objetivos

1. API de negocio (`Hospital-Api`) que **valida** JWT de Identity (no emite identidad de producto).
2. Vertical mínimo medible: **anunciador de llamados** (CU #1 = A1+A2).
3. Front autenticado contra Identity y consumiendo Hospital-Api.
4. Datos en **PostgreSQL**; paridad con PL/SQL vía golden master desde 11.2 cuando el CU lo requiera.

## No objetivos (esta oleada del piloto)

- Cablear legacy AGI/ANUNCIADOR a Identity como “migrado”
- Migrar el monolito `HOSPITAL_2`
- Oleada B de Identity (hash legacy), salvo exigencia explícita de un CU
- Remotos ADO obligatorios (trabajo local aceptado)
- **ABM completo** de sectores / ambientes / configuración de anunciadores (va a feature
  `catalogo` posterior; ver [estrategia-core.md](estrategia-core.md))
- Socket.io / multimedia / voz del anunciador legacy (WAIVE; polling o estático en v1)

## Requisitos

| Id | Requisito |
|----|-----------|
| RF-1 | `Hospital-Api` valida Bearer `iss=hospital-identity` / `aud=hospital-clients` |
| RF-2 | Endpoint smoke autenticado `/api/v1/piloto/ping` |
| RF-3 | Login de humanos solo vía Identity (no IdP de producto en Hospital-Api) |
| RF-4 | CU #1: listar anunciadores + pacientes llamados en Quarkus/Postgres |
| RF-5 | Front consume Identity (auth) + Hospital-Api (negocio) |
| RF-6 | Catálogo cross (sector/ambiente) vive **en Hospital-Api** como datos compartidos; el piloto puede usar **seed + lectura**; ABM = feature `catalogo` (mismo deploy) |
| NFR-1 | Postgres Dev Services / local; sin Hibernate contra Oracle 11.2 |
| NFR-2 | Puerto API distinto de Identity (8081 vs 8080) |
| INV-1 | No microservicio “Core” aparte solo para catálogo |
| INV-2 | Identity no almacena sectores/ambientes/anunciadores de negocio |

## Criterios de aceptación

1. Token válido → `GET /api/v1/piloto/ping` 200; sin token → 401.
2. Token válido → `GET /api/v1/anunciadores` y `GET /api/v1/anunciadores/{id}/llamados` 200 con seed demo.
3. `Hospital-Api` documentado y levantable en local.
4. SDD con tasks verificables; verify-report PASS/WAIVED antes de ampliar a G1 (recepción).
5. Front (app-5) muestra listado/llamados contra Identity + Api, o WAIVE documentado.
6. Estrategia Core documentada ([estrategia-core.md](estrategia-core.md)): sin segundo backend de catálogo.
