# T1 — Maestros identidad turno (`turnos-maestros-personal`)

`phase_id:` **`sdd.hospital.turnos-maestros-personal`**  
Estado: **gate parcial** · **2026-08-27** — DDL+seed+API+UI picker; ABM diferido; IT requiere Docker.

Padre capa 3: [`relevamiento-turnos/`](../../../relevamiento/relevamiento-turnos/) (T0 hecho).  
Gobierno: [`gobierno-migracion.md`](../../../canon/gobierno-migracion.md).

| Artefacto | Rol |
|-----------|-----|
| [spec.md](spec.md) | Alcance, Clarify, RFs, fuera de alcance |
| [plan.md](plan.md) | Capas starter + Flyway + riesgos |
| [tasks.md](tasks.md) | Checklist implementación |
| [verify-report.md](verify-report.md) | Plantilla anti-gap (vacía hasta verify) |

**No es** el módulo TURNOS. Es el corte que desbloquea operador/médico/call center y motivos sobre `ts.*`.
