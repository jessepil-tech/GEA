---
title: Plan — Piloto AGI G1
description: Cómo implementar el slice mínimo de recepción autogestión.
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-13
phase_id: sdd.hospital.piloto-agi-g1
---

# Plan — Piloto AGI G1

Estado: **reviewed** (2026-08-13).

## Enfoque

1. Reusar cimiento Identity + Hospital-Api (:8081) + Hospital-Web.
2. Feature `agi` en `Hospital-Api` (junto a `anunciador`); tablas propias + seed.
3. **Un** camino feliz: identificar → turnos → confirmar → ticket (sin ValidadorWS).
4. Front: wizard corto en Hospital-Web; medir horas.
5. Diferir golden master / PL/SQL a **G1-b** cuando el camino feliz esté verde.

## Decisiones

| Tema | Decisión | Evidencia |
|------|----------|-----------|
| Vertical | G1 recepción (inventario) | inventario-cu.md |
| Datos v1 | Seed Postgres | mismo patrón CU #1 |
| Convenios/credencial | Colapsados al turno seed | BBRecepcionarPaciente DISPLAY amplio → slice |
| Hardware | Mock | inventario WAIVE |
| UI | `/agi/recepcion` en Hospital-Web | Q1 default |
| Auth v1 | JWT Identity | NFR-2; API Key terminal → G1-b |
| Core/catálogo | Sin ABM; FK opcionales a sector seed si hace falta | estrategia-core |

## Módulos

```text
Hospital-Api
├── features/agi              ← G1 (este plan)
│     identificar / turnos / confirmar-recepcion
├── features/anunciador       ← CU #1 (hecho)
└── features/catalogo         ← ABM diferido (no bloquea G1)

Hospital-Web
└── /agi/recepcion            ← wizard 3 pasos
```

## Modelo datos (v1)

| Tabla | Rol |
|-------|-----|
| `terminal_ag` | Seed terminal demo |
| `paciente_agi` | Paciente demo (tipo_doc, nro_doc, nombre) |
| `turno_agi` | Turnos del paciente (fecha, servicio, lugar_espera) |
| `recepcion_agi` | Recepción confirmada + ticket_codigo |

Flyway: `V8__piloto_agi_g1.sql` (o siguiente libre en Hospital-Api).

## API prevista (borrador OpenAPI)

| Método | Ruta | Auth |
|--------|------|------|
| GET | `/api/v1/agi/terminales` | Bearer |
| POST | `/api/v1/agi/pacientes/identificar` | Bearer — body `{ tipoDoc, nroDoc }` |
| GET | `/api/v1/agi/pacientes/{id}/turnos` | Bearer |
| POST | `/api/v1/agi/recepciones` | Bearer — body `{ pacienteId, turnoId, terminalId }` → ticket |

## Alternativas descartadas

| Opción | Motivo |
|--------|--------|
| Cablear WAR AGI a Identity | No mide stack nuevo; dossier |
| G1 completo con ValidadorWS desde día 1 | Infla; NFR-3 |
| Microservicio AGI aparte | Contra F2 / una solución Hospital |
| Empezar por `catalogo` ABM | No aporta medición AGI |

## Riesgos

| Riesgo | Mitigación |
|--------|------------|
| Subestimar acoplamiento ambulatorio | Slice sin PL/SQL; documentar horas y gaps |
| Seed ≠ realidad | Aceptable para medir; G1-b sync/golden |
| UI tótem vs shell | Default Web; separar app después si UX lo exige |
| Confundir G1 (inventario) con un gate genérico “G1” | Usar solo `TSK-*` y `phase_id` |

## Verificación

- IT: identificar / turnos / confirmar / 401
- Smoke: `tools/smoke-piloto-agi-g1.sh` (Identity → Api) — **PASS** 2026-08-13
- UI: `/agi/recepcion` (DNI 30111222)
- verify-report post-implement — **PASS** / `gate-done`

## Dependencias

- Oleada A Identity **gate-done**
- Piloto anunciador CU #1 **gate-done**
- Spec: [spec.md](spec.md)
