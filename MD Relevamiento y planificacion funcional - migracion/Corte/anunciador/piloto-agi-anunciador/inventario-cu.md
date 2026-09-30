# Inventario CU — Piloto AGI + ANUNCIADOR

`phase_id:` `sdd.hospital.piloto-agi-anunciador` · **TSK-ops-2** (2026-08-13)

Una página: qué hay en legacy, qué conviene atacar primero para **medir** el stack nuevo
(Identity + Hospital-Api + Angular), sin hinchar el alcance al monolito.

---

## AGI (WAR JSF · ~62 clases · tótems)

| Id | Caso de uso | Evidencia legacy | Dependencias | Peso |
|----|-------------|------------------|--------------|------|
| **G1** | Autogestión recepción (tótem centro): identificar paciente → convenio/credencial → turnos/prestaciones → ticket | `termAutoGestCentro/recepcion/*`, `BBRecepcionarPaciente` | Ambulatorio, Convenios, VALIDADORES/WS, impresora, lectora | **Alto** |
| **G2** | Triage autogestión / triage ambulatorio | `termAutoGestTriage/*`, `triageAmb/*` | Config triage, sesión terminal | Medio–alto |
| **G3** | Selector de anunciador (pantalla auxiliar AGI) | `anunciadorPaciente/inicio`, `BBInicioAnunciadorPaciente` | `Pacientes.selectAnunciadorMin` | **Bajo** |
| **G4** | Login / cambiar password / índices (utilitarios) | `login`, `cambiarPassword`, `indices` | Seguridad Oracle | Bajo (absorbe Identity) |

Flujo dominante G1 (estados internos del bean): turnos, lector, credencial, convenio, token, ticket, errores.

---

## ANUNCIADOR (Vue + API Node · ya fuera de JSF)

| Id | Caso de uso | Evidencia legacy | API | Peso |
|----|-------------|------------------|-----|------|
| **A1** | **Pantalla de llamados (sala/TV)** — lista pacientes + lugar; full-bleed sin menú | `AnunciadorSimple/Compuesto`, `Llamados.vue` | `GET .../pacientesAnunciador` | **Bajo–medio** |
| **A2** | Listado / selección de anunciadores (operación) | `Anunciadores.vue` | `GET .../anunciador` | Bajo |
| **A3** | Ocupación de ambientes | `OcupacionAmbientes.vue` | `GET .../ocupacionAmbientesActual` | Bajo |
| **A4** | Auth propia Node (`api_seguridad_nodejs`) | `/autenticacion`, `/validatetoken`, API Key | Sustituir por **Identity** | Bajo (ya cubierto oleada A) |

---

## Prioridad recomendada (primer vertical)

### **CU #1 = A1 + A2** — Anunciador de llamados (lectura)

**Por qué primero**

1. Ya es satélite (Vue + Express); no arrastra JSF ni VALIDADORES.
2. API chica y mayormente **lectura** (listar anunciador + pacientes llamados).
3. Encaja con Identity (API Key de servicio / JWT) sin oleada B.
4. Mide punta a punta: Identity → Hospital-Api → front, sin tótem físico.
5. Es solidario con AGI (G3) pero no bloquea por recepción/autogestión.

**Fuera del CU #1 (siguiente oleada del piloto)**

- G1 recepción autogestión (demasiado acoplado al núcleo ambulatorio).
- G2 triage.
- Hardware (lectora, impresora, audio avanzado) → mock / diferir.
- Reemplazo completo de sockets en tiempo real: v1 puede ser **polling**; socket después.

### Orden sugerido

```text
A4 (Identity ya) → A2 listar anunciadores → A1 pantalla llamados
                 → A3 ocupación (opcional mismo sprint)
                 → G3 puente selector (si hace falta)
                 → G1 recepción (segundo vertical, con golden master)
```

---

## Criterio de “hecho” del CU #1

| Check | Descripción |
|-------|-------------|
| API | `Hospital-Api`: listar anunciadores + pacientes llamados (contrato OpenAPI) |
| Auth | Bearer Identity o API Key; sin `api_seguridad_nodejs` |
| Datos | Postgres (seed/mock aceptable en v0; sync desde 11.2 después) |
| UI sala (**A1**) | Ruta **display** full-bleed (sin shell): `/display/anunciadores/:id` |
| UI operación (**A2**) | Shell `/anunciadores` (+ detalle llamados opcional) |
| Medición | Horas reales documentadas vs estimación (objetivo Fase 3) |

---

## Decisión

| Campo | Valor |
|-------|--------|
| **Primer vertical** | **A1+A2 Anunciador de llamados** |
| Siguiente vertical | G1 Autogestión recepción — SDD [`piloto-agi-g1/`](../../recepcion/piloto-agi-g1/) (**gate-done** slice v1) |
| WAIVE ahora | G2 triage; multimedia avanzada; socket.io obligatorio |
