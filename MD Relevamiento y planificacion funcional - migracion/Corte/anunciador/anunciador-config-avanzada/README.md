# SDD — Vínculo anunciador ↔ servicio / triage (`anunciador-config-avanzada`)

`phase_id:` **`sdd.hospital.anunciador-config-avanzada`**  
Estado: **diferido** (pagaré) · 2026-09-08  
Padre: [`anunciador-agi-config-abm/`](../anunciador-agi-config-abm/)

**No es** `configuracionAvanzada.xhtml`. Esos flags (voz, multimedia, ocupación,
tipo de llamado, ctd chars, minutos, logo/fondo) **entran en v1** del padre
(Clarify fila 4 FIRME).

Este hijo cubre las **otras hojas** del menú config anunciador:

| Hoja legacy | Qué es |
|-------------|--------|
| `anunciadorServ.xhtml` | Vínculo anunciador ↔ servicio/centro |
| `anunciadorTriage.xhtml` | Vínculo triage |
| `anunciadorEspServ.xhtml` | Especialidad/servicio |

**Gatillo de cobro:** primera sala que filtre TV por servicio/triage, o al
retocar esas hojas. Hasta entonces P5 del satélite **no** se declara cerrado.

Spec/plan/tasks: no abiertos (sin código).
