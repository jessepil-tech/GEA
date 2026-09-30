---
title: Inventario DDL gaps — T5.1 ficha paciente
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-07
phase_id: sdd.hospital.turnos-agenda-ficha-paciente.ddl
---

# Inventario DDL gaps — T5.1 (G0)

Canon: [`regla-ddl-postgres-migrado.md`](../../../canon/regla-ddl-postgres-migrado.md).

| Objeto | ¿En Flyway Api? | ¿Bloquea T5.1 v1? | Acción G1 |
|--------|-----------------|-------------------|-----------|
| `ts.paciente` | **Sí** V31 | Sí lectura ficha | Usar |
| `ts.persona` | **Sí** V30 | Sí (nombre, doc, FN, sexo) | Join `id_paciente = id_persona` |
| `ts.convenio` | **Sí** V31 | Sí buscador | Usar |
| `ts.plan_convenio` | **Sí** V31 | Sí combo plan | Usar |
| Seed demo paciente | **Sí** V47 | IT/e2e | Ampliar columnas persona/paciente si ficha incompleta |
| `ts.telefono_paciente` / mails | **No** en V27–V47 | Parcial (tel/mails west) | **v1:** omitir tel/mails o null; **diferido** cuando exista DDL |
| `ts.hist_conv_pac` | **No** | No v1 | Popup historial conv → shell/ficha ABM |
| `ts.doc_req_conv` | **No** | No v1 | Popup doc requerida conv → elegibilidad |
| FK paciente→persona | Lógica legacy | No bloquea piloto | Join explícito en query |
| Query otros centros | Port Java | Sí | Golden vs `Turnos.selectTurnosOtrosCentros` — sin SP Oracle runtime |

## Decisión G1 (propuesta post-G0)

1. **V48 (opcional):** enriquecer seed V47 — `persona` con `tipo_doc_persona`, `nro_doc_persona`, `fecha_nacimiento`, `sexo`; `paciente` con `estado_datos_pac`, `nro_afiliado_ult_ate`, `observaciones` — solo si IT/e2e ficha lo exigen.
2. **No crear** tablas contacto solo para demo — documentar gap tel/mails.
3. Búsqueda paciente: join `persona` + `paciente` + left join `convenio`/`plan_convenio` última atención.

## Pendientes Oracle (referencia)

Ver [`pendientes-solo-oracle.md`](../../../estado/pendientes-solo-oracle.md) si la búsqueda legacy usa vistas/packages no portados — registrar en G2 si aparece gap.
