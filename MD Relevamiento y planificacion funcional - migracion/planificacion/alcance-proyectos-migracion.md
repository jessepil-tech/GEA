---
title: Alcance — qué proyectos del legacy se migran (recorte del cliente)
version: 1.2.0
status: canonical
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.alcance-proyectos-migracion
---

# Alcance de proyectos — el recorte del cliente, traducido

Capa 3 de [`gobierno-migracion.md`](../canon/gobierno-migracion.md).

**Qué es:** la lista de proyectos que el cliente definió como dentro y fuera de alcance
(2026-09-15), mapeada contra los proyectos que **existen** en `Hospital-Legacy`, con las
consecuencias que la lista deja abiertas.

**Qué no es:** no es el universo de un corte ([`loop-migracion-corte.md`](../canon/loop-migracion-corte.md)
§ paso 3), y **no es un recorte de capacidades**. Ese es el punto central de este documento.

## Principio

> La lista está en unidades de **código** (proyectos). El hospital funciona en unidades de
> **capacidad**. No hay correspondencia uno a uno: hay capacidades dentro de proyectos
> excluidos, y proyectos incluidos que dependen de excluidos.

Excluir un proyecto es una decisión válida y útil. Lo que **no** se sigue de ella es que la
capacidad que ese proyecto ejecutaba desaparezca del hospital sin costo. Cada caso así queda
abajo, con la pregunta que le corresponde a producto.

## Dentro de alcance

| Cliente nombró | Proyecto real | Java | XHTML | Nota |
|----------------|---------------|-----:|------:|------|
| HOSPITAL | `HOSPITAL_2` (`proyecto nombre="Hospital"`) | 2841 | 2915 | El monolito: el grueso del programa |
| HOSPITAL-BUSINESS | `HOSPITAL-BUSINESS` | 5338 | 0 | Capa de negocio; **acá viven las interfaces de laboratorio y PACS** |
| SEGURIDAD | `seguridad` (`proyecto nombre="Seguridad"`) | 154 | 51 | Perfiles, roles y menú por perfil |
| AGI | `AGI` | 62 | 59 | Tótem de recepción + anunciador de paciente |
| HOS-APP | `HOS-APP` | 93 | 73 | App de profesionales |
| AFIP | `AFIP` | 36 | 0 | **1 clase con lógica**; el resto son stubs generados |
| ANUNCIADORES Y TOTEMS | `AGI` (fila de arriba) + `ANUNCIADOR` | — | — | Cerrado 2026-09-16. Destino: **Hospital-Api** (deja de ser satélite de runtime); Identity absorbe `api_seguridad_nodejs`. **No** incluye `AGP/pages/totem-turno/`. `whatsapp/` es canal de turnos, no TV: ver A5. Cortes: [`apagar-anunciador-node/`](../cortes/anunciador/apagar-anunciador-node/) |

## Fuera de alcance

| Cliente nombró | Proyecto real | Java | Lo que este programa ya sabe de él |
|----------------|---------------|-----:|-------------------------------------|
| SCHEDULER | `SCHEDULER` | 90 | 67 procesos programados, **tres circuitos cerrados dependen de ellos** — consecuencia 1 |
| VALIDADORES | `VALIDADORES` | 311 | 17 clientes de obras sociales; elegibilidad de Turnos y Admisión — consecuencia 2 |
| RECETAS | `RECETAS` | 80 | Portal de prescriptores externos (no es una interfaz) |
| WS-HOSPITAL | `WS-HOSPITAL` | 61 | Servicios de entrada: recepción DNLab y Dosys |
| BIONEXO | `BIONEXO` | 23 | Marketplace de compras; hay circuito manual |
| ALFABETA | `ALFABETA` | 14 | Vademécum: **el job descarga y no procesa**; la carga real ya es manual |
| AXIS2 | (librería, no proyecto) | — | Runtime SOAP; en la plataforma nueva se usa otro cliente |

Coherente con lo relevado: ALFABETA y BIONEXO eran los dos frentes de menor criticidad de
[`relevamiento-integraciones-externas/`](../relevamiento/relevamiento-integraciones-externas/README.md), y
`RECETAS` ya estaba identificado como portal de usuario final, no como integración.

## Sin clasificar — necesitan decisión

Cinco proyectos del legacy no aparecen en ninguna de las dos listas:

| Proyecto | Java | XHTML | Qué es | Por qué importa |
|----------|-----:|------:|--------|-----------------|
| `ANMAT` | 65 | 0 | Trazabilidad de medicamentos y productos médicos | **Obligación regulatoria.** No es opcional para una farmacia hospitalaria |
| `AGH` | 100 | 106 | Portal extranet de terceros humanos: prescriptores, proveedores, entidades de liquidación | Se solapa con `RECETAS` y `PROVEEDORES` (excluido / sin clasificar) |
| `AGP` | 88 | 53 | Portal del paciente: perfil, grupo familiar, e-consulta, cancelar turno, **`totem-turno/`** | Es el `paciente-web` que el programa tiene **diferido**, y tiene un tótem |
| `PROVEEDORES` | 79 | 41 | Portal de proveedores (órdenes, cotizaciones, certificados) | Mismo caso que `AGH` |
| `REVENG` | 24 | 0 | Herramienta de ingeniería inversa de Hibernate | Fuera de alcance por naturaleza: es tooling, no producto |

Salvo `REVENG`, ninguno se puede dar por excluido por omisión.

## Consecuencias que requieren firma de producto

### 1. `SCHEDULER` fuera ≠ hospital sin procesos automáticos

Los jobs son cáscaras finas: `MigrarTurnoVencidoJob` solo llama a
`Interfaces.migrarTurnoVencido`, y la lógica real está en **PL/SQL de Oracle** y en
**`HOSPITAL-BUSINESS`**, que sí está dentro de alcance. Entonces la exclusión se lee bien
como «no porten ese proyecto», y deja pendiente **el disparador**.

Lo que hoy depende de ese disparador, ya verificado en
[`relevamiento-procesos-programados/`](../relevamiento/relevamiento-procesos-programados/README.md):

| Capacidad | Sin disparador |
|-----------|----------------|
| Purga de la cola de recepción y triage (6 h) | La cola **nunca se limpia**: pacientes que se fueron siguen como «próximo a llamar» |
| Apagado de llamados del anunciador (1 h) | La TV sigue anunciando pacientes viejos |
| Vigencia de habilitación de turnos | Una habilitación futura nunca se activa; una vencida nunca se apaga |
| Confirmación **y cancelación** de turno por SMS | El paciente pierde la autogestión (hoy escribe `ts.turno`) |
| Avisos de reprogramación, mails y SMS | Se generan en la base y nadie los despacha |

**Pregunta a producto:** ¿la plataforma nueva provee tareas programadas (y quién porta esas
capacidades), o el hospital acepta perderlas? Mientras no haya respuesta, los tres circuitos
afectados quedan con la deuda anotada en
[`estado-piloto-vs-general.md`](../estado/estado-piloto-vs-general.md).

### 2. `VALIDADORES` fuera → ¿qué pasa con la elegibilidad?

La validación online contra obras sociales se invoca **sincrónicamente desde pantalla** en
recepción, admisión y turnos. El programa ya tiene el diferido declarado (P-ORA-010 en
[`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md)) y los cortes de Turnos usan seed.

**Pregunta a producto:** ¿coexistencia (la plataforma nueva consume el `VALIDADORES` legacy
mientras siga en pie), o el hospital opera sin validación online? Son diecisiete convenios,
así que no es un interruptor.

### 3. `AFIP` dentro → decidir facade o WSAA

`AFIP` no habla con AFIP: invoca un **facade del proveedor del HIS** que resuelve WSFEv1. No
hay WSAA, certificados ni ticket de acceso en el proyecto (verificado: cero archivos), y cada
request incluye la contraseña de la base del HIS como parámetro de autenticación.

**Pregunta a producto:** ¿se sigue usando el facade (hay que confirmar que el proveedor lo
mantiene y sacar la credencial de la base del request) o se factura **directo** contra AFIP?
La segunda opción incluye autenticación WSAA, gestión de certificados y homologación:
trabajo **no relevado ni estimado** en ningún documento de este programa.

### 4. `SEGURIDAD` dentro → la paridad de acceso es alcance, no deseo

Buena noticia y confirmación: el modelo de perfiles, roles y menú por perfil entra en el
recorte, así que [`regla-paridad-acceso-auditoria.md`](../canon/regla-paridad-acceso-auditoria.md) es
exigible de verdad y no un diferido permanente. El rol funcional que el PL/SQL valida viaja
con `HOSPITAL-BUSINESS` y los packages, también dentro de alcance.

**Destino decidido:** no es un stream de negocio. Se absorbe en `Hospital-Identity` (que ya
tiene roles, claims y `subject_type`, pero **no tiene menú**) más un `Hospital-Identity-Web`
nuevo en Angular: [`destino-seguridad-identity.md`](destino-seguridad-identity.md). Ahí quedan
el mapeo tabla por tabla, lo que **no** se absorbe —el rol funcional del PL/SQL y la auditoría
de negocio— y dos defectos de autorización abiertos en `Hospital-Web`.

**B5 (2026-09-16):** Identity también absorbe `ANUNCIADOR/api_seguridad_nodejs`. Anunciadores
deja de ser satélite de runtime: se integra en `Hospital-Api`. El inventario origin puede
seguir diciendo «satélite» para el menú legacy (no está en `MENU_APLICACION`); el destino no.

## Ambigüedades a confirmar con el cliente

Hoja para la reunión, con el «por qué importa» de cada una:
[`preguntas-alcance-cliente.md`](preguntas-alcance-cliente.md).

### Cerradas

1. **«ANUNCIADORES Y TOTEMS»** (2026-09-16): `AGI` + `Hospital-Legacy/ANUNCIADOR`.
   Destino: Hospital-Api (no satélite de runtime). El tótem `AGP/pages/totem-turno/`
   **no** entra en ese nombre (sigue en B2).
5. **`api_seguridad_nodejs`** (2026-09-16): Identity lo absorbe. El JSF `seguridad` ya
   estaba en Identity + Identity-Web.

### Abiertas

2. **`AGP` (portal del paciente):** ¿queda fuera? El programa tiene
   [`paciente-web/`](../cortes/plataforma/paciente-web/) diferido, así que hoy la respuesta se contradice con el
   backlog. El tótem de turnos de AGP viaja con esta pregunta, no con anunciadores.
3. **`AGH` y `PROVEEDORES`:** si `RECETAS` sale por ser portal externo, estos dos son el mismo
   caso. ¿Salen los tres o entra el frente de portales completo?
4. **`ANMAT`:** ¿olvido u exclusión deliberada? Es trazabilidad regulatoria.
6. **Laboratorio e imagenología:** las interfaces de LIS, los autoanalizadores (ASTM) y
   PACS/DICOM viven en `HOSPITAL-BUSINESS`, que está **dentro** de alcance. ¿Se migran? Es el
   frente más crítico de todos y no aparece nombrado en ninguna de las dos listas.
7. **WhatsApp de turnos (A5):** el gateway Node en `ANUNCIADOR/whatsapp` no estaba en las
   listas. El canal sí existe: `GENERA_MAILS.f_whatsapp_*` + `EnvioWhatsappJob`. ¿Este
   hospital lo usa, y quién despacha cuando SCHEDULER está fuera y el Node de anunciador
   se apaga?

## Efecto en el canon

- El **techo del corte** y el universo firmado se acotan a los proyectos de alcance.
- El [índice del legacy](../relevamiento/indice-legacy/README.md) sigue indexando todo el legacy a propósito:
  necesitamos ver las dependencias hacia afuera del recorte, no ignorarlas.
- [`relevamiento-integraciones-externas/`](../relevamiento/relevamiento-integraciones-externas/README.md): de
  las cinco integraciones reales quedan **AFIP** (dentro) y **ANMAT** (sin clasificar).
- [`relevamiento-procesos-programados/`](../relevamiento/relevamiento-procesos-programados/README.md): el
  proyecto sale del alcance, las capacidades no. Ver consecuencia 1.
