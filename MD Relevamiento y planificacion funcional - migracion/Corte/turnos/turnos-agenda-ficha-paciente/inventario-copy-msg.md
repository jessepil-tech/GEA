---
title: Inventario copy — T5.1 ficha paciente / west agenda
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-07
phase_id: sdd.hospital.turnos-agenda-ficha-paciente.copy
---

# Inventario copy — T5.1 (G0)

Fuente UI: `Resources.properties` (keys lowercase).  
XHTML in-scope:

- `turnos/asignacionTurnos/agenda.xhtml` (north paciente/convenio + west ficha/otros centros)
- `turnos/asignacionTurnos/asignacionTurnos.xhtml` (popups buscadores)
- `buscadores/buscadorPaciente.xhtml`
- `buscadores/buscadorConvenio.xhtml`

Web destino: extender `Hospital-Web/src/app/turnos/pages/turnos-agenda/turnos-agenda-labels.ts`.

**Herencia T5:** keys ya mapeadas en [`turnos-agenda-otorgar/inventario-copy-msg.md`](../turnos-agenda-otorgar/inventario-copy-msg.md) — no duplicar; esta tabla = **delta T5.1**.

| msg.key | Valor típico | Constante Web | En UI T5.1 |
|---------|--------------|---------------|------------|
| `apellido` | Apellido | `apellido` | sí (dialog paciente filtro) |
| `nombre` | Nombre | `nombre` | sí (dialog paciente filtro + col) |
| `apellido_soltera` | Apellido Soltera | `apellidoSoltera` | sí (col dialog) |
| `tipo_documento` | Tipo Documento | `tipoDocumento` | sí (col + ficha west) |
| `tipo_paciente` | Tipo Paciente | `tipoPaciente` | sí (col dialog) |
| `estado_datos_paciente` | Estado Datos Paciente | `estadoDatosPaciente` | sí (col dialog) |
| `observaciones_convenio` | Observaciones Convenio | `observacionesConvenio` | sí (popup info conv) |
| `observaciones_plan_convenio` | Observaciones Plan Convenio | `observacionesPlanConvenio` | sí (popup info conv) |
| `documentacion_requerida` | Documentación Requerida | `documentacionRequerida` | **diferido** (tabla doc req popup conv) |
| `doc_requerido` | Doc. Requerido | `docRequerido` | **diferido** |
| `punto_vigentes` | ● Vigentes | `puntoVigentes` | sí (leyenda buscador conv) |
| `punto_no_vigentes` | ● No vigentes | `puntoNoVigentes` | sí |
| `punto_atencion_suspendida` | ● Atención suspendida | `puntoAtencionSuspendida` | sí |
| `nuevo_paciente` | Nuevo Paciente | `nuevoPaciente` | **diferido** ABM (botón oculto v1) |
| `cancelados` | Cancelados | `cancelados` | sí (leyenda calendario west — distinto footer grilla) |

## Geometría copy-adjacent (labels en misma fila)

| Zona | Legacy | Web DoD |
|------|--------|---------|
| North paciente | label + input + 🔍 + info + limpiar en **una fila** | grid 4 cols fila 1 T5 |
| North convenio/plan | convenio + 🔍 \| plan + info conv | filas 2–3 T5 |
| West Desde/Hasta | `h:panelGrid columns="4"` — labels + timePicker **45px** | misma fila horizontal |
| West leyenda cal | `columns="3"` × 2 filas (6 estilos) | grid 3×2 bajo calendario |
| Dialog paciente | 1200×550 · header `buscar_paciente` | modal ancho ~1200 |
| Dialog convenio | 1200×550 · header `buscar_convenio` | modal ancho ~1200 |

## Fuera de T5.1 (placeholder / no labels.ts)

- Accordion tab Paciente 170px → `turnos-asignacion-shell`
- Botón *Nuevo paciente* en dialog → diferido ABM
- Icono elegibilidad (fa-check/times) → oculto v1; copy `PENDIENTE` en hijo elegibilidad

## MessageBundle (toast — no msg.*)

Reusar [`turnos-agenda-otorgar/inventario-validaciones.md`](../turnos-agenda-otorgar/inventario-validaciones.md).  
T5.1 no agrega toasts nuevos salvo errores de búsqueda vacía (reusar `no_se_encontraron_registros` / WARN genérico).
