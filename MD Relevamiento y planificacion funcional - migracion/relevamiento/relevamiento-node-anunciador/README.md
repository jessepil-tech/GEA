---
title: Relevamiento — Anunciador (sala TV) + Tótem AGI
status: active
owner: grupogea
last_updated: 2026-09-17
phase_id: sdd.hospital.relevamiento-node-anunciador
---

# Relevamiento — Anunciador (sala TV) + Tótem AGI

`phase_id:` **`sdd.hospital.relevamiento-node-anunciador`**  
Estado: **active** · actualizado **2026-09-17**  
Capa 3: [`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md) ·
[`gobierno-migracion.md`](../../canon/gobierno-migracion.md)

Ejemplo **canónico** del entregable de relevamiento modular (retrofit sobre el
mapa Node→Api previo).

| Doc | Rol |
|-----|-----|
| [pipeline.md](pipeline.md) | Configuración → operación (A1–A9) |
| [maestros.md](maestros.md) | Maestros / seguridad / ¿bloquea si falta? |
| [inventario.md](inventario.md) | Capacidades config + ops |
| [matriz.md](matriz.md) | Node HTTP/WS ↔ Hospital-Api / Identity |
| [cortes.md](cortes.md) | Orden de CUs por dependencia |
| [decisiones.md](decisiones.md) | D-ANU-* (DDL canónico, apagar Node, …) |

## Contexto

| Pieza legacy | Rol |
|--------------|-----|
| `ANUNCIADOR/anunciador` | HTTP + Socket.IO (llamados, ocupación, logos, diccionario) |
| `ANUNCIADOR/api_seguridad_nodejs` | JWT propio → **Hospital-Identity** |
| `ANUNCIADOR/anunciadorVue` | Front sala → **Hospital-Web** (parcial) |
| `HOSPITAL_2` config anunciador / terminal AG | ABM maestros (aún no en destino) |
| `AGI` | Runtime tótem (`idTerminal`) |
| Oracle / PG `ts.llamado_anunciador` | Tabla canónica de llamados |

Padres / hermanos: piloto anunciador, anunciador-ws, paridad cola, apagar-node,
ciclo-vida, cutover `ts`, [`cierre-paridad-agi-anunciador/`](../../cortes/anunciador/cierre-paridad-agi-anunciador/).

## Veredicto (Fase E) — 2026-08-26

| Pregunta | Respuesta |
|----------|-----------|
| ¿Viable con reglas actuales? | **Sí**, perímetro **operativo** (TV + tótem + llamar) sobre `ts.*` |
| ¿Paridad de configuración? | **No** — ABM/maestros/permisos en seed o diferidos (`anunciador-agi-config-abm`) |
| Prerrequisitos bloqueantes para “perímetro cerrado” | P0 núcleo TV **cobrado**; writer clínico Llamar **cobrado** ([`cu-llamar-atencion-medica/`](../../cortes/recepcion/cu-llamar-atencion-medica/)); P3 config (ABM sala **si** hay que dar de alta en PG; copia ≠ prueba); P2 impresora |
| Primer CU si se retoma config | [`anunciador-agi-config-abm/`](../../cortes/anunciador/anunciador-agi-config-abm/) — SDD **abierto**; Clarify propuesto; **fila 4 FIRME** (flags v1) |
| Fuera de alcance inmediato | Pack logos (no migrado); vínculo serv/triage [`anunciador-config-avanzada/`](../../cortes/anunciador/anunciador-config-avanzada/) |

## Profundidad de análisis

Perímetro operativo (TV / tótem / llamar) sobre `ts.*`. Configuración y el
package `ANUNCIADORES` no están cerrados: P3 es escritura de maestros, no el grafo.

| Capa | Qué cerraría | Estado | Evidencia |
|------|--------------|--------|-----------|
| Pipeline A1–A9 | Configuración → operación | cerrado | [`pipeline.md`](pipeline.md) |
| Escritores cruzados | Recepción / atención / cola que insertan `llamado_anunciador` | muestra | Llamar clínico cobrado; `MigrarTurnoVencidoJob` apaga llamados — efecto, no inventario de writers |
| Procesos programados | Cada job se porta / difiere / N/A | muestra | Apagado de anunciador relevado en [`relevamiento-procesos-programados/`](../relevamiento-procesos-programados/); no `--jobs anunciador` cerrado en este A–C |
| Firmas del package | Universo `ANUNCIADORES` / `RECEPCIONES` del perímetro | muestra | [`matriz.md`](matriz.md) Node↔Api; GM de `f_set_paciente_anun_cola` es de un corte, no del BODY |
| Integridad referencial | FK anunciador / cola / recepción | muestra | Internas aplicadas; FKs a maestros diferidas (`pendientes-solo-oracle.md` P-ORA-009) |
| Reportes e integraciones | BIRT de este satélite | N/A | Sala TV + tótem; no hay `.rptdesign` de anunciador en el perímetro |

**Firma de proceso:** relevamiento A–C documentado en esta carpeta. Seed **no**
cuenta como cierre de maestros.
