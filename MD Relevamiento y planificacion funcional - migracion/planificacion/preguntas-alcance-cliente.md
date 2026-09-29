---
title: Preguntas de alcance para el cliente (recorte de proyectos 15-sep-2026)
version: 1.2.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.preguntas-alcance-cliente
---

# Preguntas de alcance — para llevar a la reunión

Hoja de trabajo derivada de [`alcance-proyectos-migracion.md`](alcance-proyectos-migracion.md).
Cada punto está redactado para preguntarse tal cual, con **por qué importa** y **qué cambia
según la respuesta**. Ninguna es retórica: todas bloquean o mueven trabajo.

Orden por costo de equivocarse, no por comodidad. Las **A** son decisiones; las **B**
son aclaraciones del recorte.

---

## A. Decisiones que cambian el plan

### A1. Los procesos automáticos del hospital (`SCHEDULER` quedó fuera)

**Pregunta:** el proyecto no se migra, entendido. ¿Quién provee las tareas programadas en la
plataforma nueva, y quién porta las capacidades que hoy dependen de ellas?

**Por qué importa:** los jobs son cáscaras finas y la lógica está en PL/SQL y en
`HOSPITAL-BUSINESS`, que sí se migran. Lo que falta es el **disparador**. Verificado contra el
código de producción, hoy dependen de él:

| Capacidad | Si nadie la porta |
|-----------|-------------------|
| Purga de la cola de recepción y triage (6 h) | La cola nunca se limpia: pacientes que se fueron siguen como «próximo a llamar» |
| Apagado de llamados del anunciador (1 h) | La pantalla de TV sigue anunciando pacientes de horas antes |
| Vigencia de la habilitación de turnos | Una habilitación futura nunca se activa; una vencida nunca se apaga |
| Confirmación **y cancelación** de turno por SMS | El paciente pierde la autogestión (hoy modifica el turno) |
| Avisos de turno y de reprogramación | Se generan en la base y nadie los despacha |

**Qué cambia:** las tres primeras afectan a **Recepción, Turnos y Anunciador, que ya están
declarados cerrados**. Si la respuesta es «se portan», hay un carril nuevo con owner. Si es
«se acepta perderlas», queda como decisión firmada y el hospital lo sabe de antemano.

### A2. Elegibilidad de obras sociales (`VALIDADORES` quedó fuera)

**Pregunta:** ¿la plataforma nueva consume el `VALIDADORES` legacy mientras siga en pie
(coexistencia), o el hospital opera sin validación online?

**Por qué importa:** la validación se invoca **desde pantalla** en recepción, admisión y
turnos —circuitos que sí se migran— y no es un servicio: son **17 clientes distintos**, uno
por convenio, cada uno con su homologación.

**Qué cambia:** si es coexistencia, hay que diseñar el puente y mantenerlo (hoy está declarado
como P-ORA-010 y los cortes de Turnos usan datos de prueba). Si no, hay que decirle a
admisión que la validación pasa a ser manual.

### A3. Factura electrónica: facade o directo (`AFIP` quedó dentro)

**Pregunta:** ¿se sigue usando el facade del proveedor del HIS, o la plataforma nueva factura
**directo** contra AFIP?

**Por qué importa:** el proyecto `AFIP` tiene 36 clases y **una** con lógica: no habla con
AFIP, habla con un facade del proveedor que resuelve WSFEv1. No hay autenticación fiscal
(WSAA), certificados ni ticket de acceso en el proyecto.

**Qué cambia:** con facade, el trabajo es chico, pero hay que confirmar que el proveedor lo
mantiene y sacar la contraseña de la base del HIS de cada request (hoy va como parámetro).
Directo contra AFIP significa WSAA, gestión de certificados y homologación: **trabajo no
relevado ni estimado en ningún documento del programa**.

### A4. Laboratorio, autoanalizadores e imagenología (entran por arrastre)

**Pregunta:** ¿están en alcance? Ninguna de las dos listas los nombra, pero viven en
`HOSPITAL-BUSINESS`, que quedó **dentro**.

**Por qué importa:** son tres frentes distintos y ninguno estaba relevado:
laboratorios externos (Nextlab, DNLab, Kern, Medibase), **autoanalizadores** por ASTM sobre
TCP y puerto serie, e imagenología PACS/DICOM. El de autoanalizadores es el más crítico de
todo el conjunto: si cae, los resultados de los equipos se cargan a mano.

**Qué cambia:** si entran, hay que relevarlos antes de tocar el stream de Laboratorio, que
además de sus 83 pantallas y su package pasa a incluir esto.

### A5. WhatsApp de turnos (el directorio vino con `ANUNCIADOR`, el canal no está en las listas)

**Pregunta:** este hospital, ¿confirma y recuerda turnos por WhatsApp? Si sí: ¿se porta el
gateway, se deja el Node, o se acepta perder el canal?

**Por qué importa:** no es un proyecto del recorte. Es el **sender** de un canal que ya vive
en piezas de adentro y de afuera:

| Pieza | Dónde | En el recorte |
|-------|--------|----------------|
| Genera el mensaje (confirma / recuerda turno) | `GENERA_MAILS.f_whatsapp_*` en `HOSPITAL-BUSINESS` | Dentro |
| Dispara el envío | `EnvioWhatsappJob` en `SCHEDULER` | Fuera (misma familia que A1) |
| Gateway HTTP (Wassenger) | `ANUNCIADOR/whatsapp` · URL en `param_general.url_envio_whatsapp` | Vino con B1; no estaba nombrado |

Si Anunciadores se apaga al absorberse en Hospital-Api y nadie porta el gateway, los
mensajes se generan en la base y **nadie los despacha** —igual que el SMS de A1.

**Qué cambia:** si el hospital no lo usa, se documenta N/A y no se toca. Si lo usa, no es un
stream nuevo: es dueño del **canal** (disparador + gateway), no del zip.

---

## B. Aclaraciones del recorte

### B1. ¿Qué es «ANUNCIADORES Y TOTEMS»?

**Cerrada (2026-09-16, producto en sesión).** Es `AGI` + el proyecto Node
`Hospital-Legacy/ANUNCIADOR`. No incluye el tótem de `AGP/pages/totem-turno/`
(ese sigue en B2, con el portal del paciente).

El directorio `ANUNCIADOR/` no es solo la TV: tiene `anunciador`, `anunciadorVue`,
`api_seguridad_nodejs` y `whatsapp`. Destino (2026-09-16): Anunciadores se absorbe
en **Hospital-Api** (deja de ser satélite de runtime); Identity absorbe
`api_seguridad_nodejs` (B5). `whatsapp` no estaba en las listas: ver **A5**.

### B2. `AGP`, el portal del paciente

No figura en ninguna lista. Es perfil, grupo familiar, e-consulta, cancelación de turno y un
tótem de turnos. El programa tiene un SDD `paciente-web` **diferido**, así que hoy el recorte
y el backlog se contradicen: ¿queda fuera del alcance o es un diferido con fecha?

### B3. `AGH` y `PROVEEDORES`, los portales de terceros

`RECETAS` salió por ser un portal de usuarios externos. `AGH` (prescriptores, proveedores y
entidades de liquidación) y `PROVEEDORES` son el mismo caso y no están en ninguna lista.
¿Salen los tres, o entra el frente de portales completo?

### B4. `ANMAT` — trazabilidad

No figura en ninguna lista. Es trazabilidad de medicamentos y productos médicos: obligación
regulatoria, no una función opcional de farmacia. ¿Es un olvido o una exclusión deliberada?

### B5. ¿«SEGURIDAD» incluye el servicio Node?

**Cerrada (2026-09-16, producto en sesión).** Identity absorbe `api_seguridad_nodejs`.
Anunciadores se integra en **Hospital-Api**: deja de ser satélite de runtime. El JSF
`seguridad` sigue en Identity + Identity-Web
([`destino-seguridad-identity.md`](destino-seguridad-identity.md)).

### B6. Instalación de referencia

**Pregunta:** ¿contra qué instalación se define «igual al legacy»?

**Por qué importa:** el HIS tiene **313 ramas de comportamiento por cliente** sobre 16
instalaciones, y el código del repositorio viene marcado como genérico, así que **al leerlo o
ejecutarlo esas ramas están todas apagadas**: se ve el camino de fábrica, no el del hospital.
Hay ramas dentro de recepción y de agenda, que ya se migraron.

**Qué cambia:** si la plataforma nueva es para **una** instalación, las ramas de los otros
quince son código a no portar y hay que decir cuál es la nuestra. Si va a ser
multi-instalación como el legacy, eso se resuelve con configuración y no replicando el `if`
por nombre de cliente. También hace falta un ambiente de referencia marcado con la
instalación real, o el relevamiento sigue mirando el comportamiento equivocado.

---

## Nota sobre el tamaño del recorte

Conviene decirlo en la reunión para alinear expectativas: **excluir esos siete proyectos no
reduce el programa**. Saca el 6 % del código y el 1,5 % de las pantallas, porque cinco de los
seis excluidos no tienen interfaz de usuario y el 87 % del código está en el monolito y en la
capa de negocio, ambos dentro. Los 16 streams de trabajo quedan intactos
([`relevamiento-his-inventario-global/arbol-dependencias.md`](../relevamiento/relevamiento-his-inventario-global/arbol-dependencias.md)
§ alcance). El valor del recorte no es achicar: es **saber qué no se toca** y descubrir qué
quedó sin dueño.

## Cómo se cierra cada punto

La respuesta se anota acá con fecha y quién la dio, y de ahí baja al canon:
`alcance-proyectos-migracion.md` (alcance), el tablero de streams (carriles y owners) y el
`spec.md` del corte que la necesite. Una respuesta verbal que no llega a un documento no
existe para el gate.
