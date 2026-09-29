---
title: Destino de la aplicación de Seguridad — Identity + Identity-Web
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.destino-seguridad-identity
---

# Dónde aterriza la Seguridad del legacy

**Decisión (15-sep-2026):** la aplicación `seguridad` del legacy (51 `.xhtml`, 154 clases, dentro
de alcance según [`alcance-proyectos-migracion.md`](alcance-proyectos-migracion.md)) **no** es un
stream de negocio: se absorbe en **`Hospital-Identity`** como backend y en un
**`Hospital-Identity-Web`** nuevo (Angular) como front de administración.

Regla que la gobierna: [`regla-paridad-acceso-auditoria.md`](../canon/regla-paridad-acceso-auditoria.md).
Este documento dice **qué se absorbe, qué no, y qué falta para que el modelo funcione**.

## Punto de partida, medido

`Hospital-Identity` (Java, hexagonal: `core` · `application` · `infrastructure` ·
`presentation-api`) ya tiene la mitad del camino hecho:

| Ya existe | Migración |
|-----------|-----------|
| `users` + `auth_sessions` | V1, V2 |
| `roles` + `user_roles` | V4 |
| `user_claims` + `role_claims` (`claim_type` / `claim_value`) | V5 |
| `users.subject_type` (default `PERSONAL`) + `users.legacy_id_personal` | V6 |
| `AuditLog` con `DbAction` y `Auditable` | core |

**Lo que no existe: el menú.** Cero menciones en todo el repo. Y el menú es *el* mecanismo de
control de acceso del legacy.

Del otro lado, `Hospital-Web` ya está cableado para recibirlo: el contrato es un claim
`Permission` (`ClaimTypes.PERMISSION`) con valor `menu:KEY`, y el filtro
`menu-profile.filter.ts` ya lo consume contra un catálogo estático
(`hospital-menu.catalog.ts`). Falta el **emisor**, que es justamente lo que Identity no tiene.

## Mapeo legacy → destino

| Legacy (`seguridad`) | Destino | Nota |
|----------------------|---------|------|
| `PerfilAcceso` | `roles` | El «cargo». Un perfil agrupa roles de acceso |
| `RolAcceso` | Conjunto de `role_claims` `Permission` | El legacy tiene **dos niveles** (perfil → rol → menú) y el destino tiene uno y medio: hay que decidir si el rol de acceso es otro `role` compuesto o un grupo de claims al emitir |
| `MenuRolAcceso` · `MenuPerfilAcceso` | `role_claims` con `menu:KEY` | La asignación es **dato administrable**, no código |
| `MenuAplicacion` | Catálogo de entradas **por aplicación** | Hoy vive en el front (`hospital-menu.catalog.ts`). Ver decisión pendiente 1 |
| `PersonalPerfilAcceso` | `user_roles` con `subject_type = PERSONAL` | `legacy_id_personal` ya está |
| `ProveedorPerfilAcceso` · `PersonalPerfilAccesoContable` | `subject_type` nuevo + eje contable | Ver «qué no se absorbe», punto 3 |
| `UsuarioPrestador` · `UsuarioPrestPerfilAcceso` · `Prestador` | `subject_type = PRESTADOR` | Prestadores externos: hoy `subject_type` solo contempla `PERSONAL` |
| `login.xhtml` · `loginByPass.xhtml` · `cambiarPassword.xhtml` | `presentation-api` + Identity-Web | `loginByPass` es un bypass: **relevar antes de portar**, no replicar por inercia |
| `consultaPerfiles*` · `consultaRoles` · buscadores | Identity-Web | Cuatro consultas y cinco buscadores |

**Cuatro tipos de sujeto, no uno.** El legacy administra personal, personal contable,
proveedores y usuarios prestadores. `subject_type` existe pero arranca con un solo valor: el
modelo de Identity tiene que crecer a los cuatro, y eso toca `presentation-api` y el front.

## Qué **no** se absorbe

1. **El rol funcional que valida el PL/SQL.** `ts.rol_funcional_pers` se consulta *dentro* de
   la operación y la aborta con un mensaje que es contrato. Eso es **regla de negocio**, no
   autorización de pantalla: vive junto al CU en `Hospital-Api`. Identity puede ser la fuente
   del dato (un claim más), pero si el Api solo lo mira en el borde y deja pasar, se pierde el
   comportamiento del legacy y el mensaje. La distinción está en
   [`regla-paridad-acceso-auditoria.md`](../canon/regla-paridad-acceso-auditoria.md).
2. **La auditoría de las 264 tablas de negocio.** Los `TBL_AUD_*` auditan campo por campo
   sobre el schema `ts`. El `AuditLog` de Identity audita **identidad**: no lo reemplaza ni
   pretende hacerlo.
3. **El eje contable de proveedores.** `ProveedorPerfilAcceso` y los perfiles contables son la
   puerta de los portales `AGH` y `PROVEEDORES`, que quedaron **sin clasificar** en el recorte
   del cliente. Si esos portales no entran, el eje contable entra solo como dato a preservar,
   no como funcionalidad. No se resuelve acá: se resuelve en
   [`preguntas-alcance-cliente.md`](preguntas-alcance-cliente.md) § B3.

## Dos defectos abiertos en `Hospital-Web`

Los encontré al revisar el encaje y conviene arreglarlos con esta oleada, porque son de
autorización:

**1. El filtro es fail-open.** Sin claims y sin `showAll`, `allowedSet()` devuelve `null` y se
muestra **el árbol completo**. El comentario lo declara a propósito («no vaciar el menú hasta
Identity oleada C»), lo cual es razonable como andamio de desarrollo y es lo contrario del
legacy: allá, si el perfil no tiene la entrada, la entrada no está. Cuando Identity empiece a
emitir `menu:KEY`, el default tiene que invertirse a **fail-closed**, y hasta entonces el
andamio no debería viajar a un ambiente con usuarios reales.

**2. Cualquier rol que contenga «admin» obtiene acceso total.** `isFullAccessRole()` usa
`v.includes('admin')`, así que un rol llamado `administrativo` —que en un hospital es el
personal de mostrador— pasa como acceso completo, igual que `admin_turnos` o
`administracion`. La comparación tiene que ser exacta contra una lista corta de roles
técnicos.

Ninguno de los dos rompe nada hoy (Identity todavía no emite claims y el único usuario es
`admin`), y los dos se vuelven un agujero el día que se cargue el primer perfil real.

## ¿Es solo del HIS o sirve a varias aplicaciones?

Medido, y la respuesta es **varias**, por diseño y no por accidente:

- Existe una tabla `APLICACION`, y el catálogo `MENU_APLICACION` tiene `ID_APLICACION`: el
  árbol de menú está **particionado por aplicación**. En el código de la app aparecen al menos
  dos, `HOSPITAL` y `CONTABLE`.
- **El HIS no administra su menú: lo consume.** `MenuBuilder` de `HOSPITAL_2` llama a
  `Seguridad.getMenuAccesoAll(idModulo, usuario, sesión)` y arma el árbol con lo que ese
  servicio le devuelve **ya filtrado por el acceso del usuario**.
- Ningún otro proyecto del legacy toca el modelo de perfiles: de los once revisados, solo la
  app `seguridad` lo referencia (73 archivos). Los demás son **clientes**, no administradores.
- Los sujetos que administra no son solo del HIS: hay proveedores y usuarios prestadores, que
  son de los portales.

O sea que el legacy ya trata a la seguridad como **servicio central con front de
administración propio**, y el HIS es uno de sus consumidores. Eso es exactamente la forma
`Hospital-Identity` + `Hospital-Identity-Web`, y es el argumento más fuerte para no meterlo
dentro de `Hospital-Web`: si viviera ahí, el HIS pasaría a ser dueño de la identidad de
aplicaciones que no son el HIS.

**Una precisión sobre «SSO».** Lo que el legacy tiene es **identidad y autorización
centralizadas**; no es single sign-on: cada aplicación presenta su propio login contra el
mismo padrón. Si el destino además va a ser SSO real (una sesión, varias apps), eso es una
decisión nueva —buena, pero nueva— y hay que diseñarla: hoy no existe en el origen y no se
obtiene sola por separar el repo.

## `Hospital-Identity-Web`: qué gana y qué cuesta

**A favor del repo aparte.** El legacy también la tiene separada, y por buenas razones: quien
administra perfiles no es quien usa el HIS, el ciclo de vida es distinto y el menú es **por
aplicación**, así que la administración tiene su propio árbol. Además evita que un bug del
front clínico exponga la pantalla que reparte permisos.

**Lo que cuesta, y hay que decidirlo antes de crear el repo:**

1. **El shell.** `Hospital-Web` tiene su layout, tema y componentes. O se extrae a una
   librería compartida, o `Identity-Web` lo duplica y las dos apps divergen visualmente —y hay
   una regla de orientación visual que aplica también acá.
2. **La sesión entre apps.** Dos front-ends con el mismo Identity: hace falta definir si
   comparten JWT, si hay SSO y qué pasa al expirar en una y no en la otra.
3. **Sexto repo en el programa.** Hay que registrarlo en el tablero de capacidad, en el
   reparto de owners y en las reservas de recursos. El Flyway de Identity (hoy en V6) pasa a
   ser un recurso disputado más: entra al paso 2 del
   [loop](../canon/loop-migracion-corte.md) como cualquier otro.

**Alternativa que descartaría solo con argumento:** un módulo lazy dentro de `Hospital-Web`
con guard de administrador. Es más barato en infraestructura y peor en aislamiento. Si el
motivo para no crear el repo es el costo del shell, conviene resolver el shell (librería
compartida) en vez de renunciar al aislamiento.

## Decisiones pendientes

1. **¿Dónde vive el catálogo de entradas de menú?** Hoy es código en el front
   (`hospital-menu.catalog.ts`); en el legacy es dato, y conviene ser preciso sobre **qué**
   se gana con eso, porque no es «crear pantallas sin programar».

   `MENU_APLICACION` guarda, por entrada: descripción, **entrada padre** (jerarquía que el
   HIS recorre hasta cuatro niveles), número de orden, **acción** (a dónde va) y aplicación.
   Lo que un administrador puede hacer sin deploy es, entonces:

   - **Asignar y desasignar** entradas a roles (`MENU_ROL_ACCESO`): el uso principal, y el que
     de verdad importa.
   - **Sacar una entrada del menú** para deshabilitar una función ya construida —así se apaga
     algo en el legacy sin tocar código— y volver a habilitarla.
   - **Renombrar, reordenar y mover** de rama, que es cómo el hospital adapta el árbol a su
     forma de trabajar.
   - **Dar de alta** una entrada que apunte a una pantalla **que ya existe**: el caso real es
     exponer algo que estaba construido y no estaba en el menú.

   Lo que **no** se gana es una pantalla nueva: si la acción no existe, el alta no sirve de
   nada. Así que el requisito verdadero es que sean dato la **asignación y el estado** de las
   entradas; el catálogo de claves puede seguir siendo código sin pérdida real, siempre que
   agregar una clave no obligue a un deploy del front para poder **asignarla**.
2. **¿Perfil y rol de acceso son dos niveles o uno?** Aplanarlos es tentador y rompe la
   administración: el legacy reusa roles entre perfiles.
3. **¿`loginByPass` existe en el destino?** Es un bypass de autenticación: hay que entender
   para qué se usa hoy antes de decidir, no portarlo por paridad.

**Cerrada:** `api_seguridad_nodejs` entra y lo absorbe Identity (2026-09-16, producto).
Anunciadores se integra en Hospital-Api (deja de ser satélite de runtime). No se mantiene
un JWT Node en paralelo. [`preguntas-alcance-cliente.md`](preguntas-alcance-cliente.md) § B5.

## Orden sugerido

1. **Relevamiento A–C de Seguridad** (docs): el modelo de cuatro sujetos, los dos niveles de
   perfil y el inventario de entradas de menú por aplicación. Sin esto no hay spec.
2. **Emisor de claims en Identity:** catálogo de menú + `menu:KEY` en `role_claims`. Con esto
   el front deja de estar en fail-open y se puede probar con un actor sin derecho, que es la
   evidencia que pide la regla de acceso.
3. **Arreglo de los dos defectos** de `Hospital-Web` junto con el punto 2, no antes: el
   fail-closed sin emisor deja el menú vacío.
4. **`Identity-Web`** con el ABM de perfiles, roles y asignación, una vez resuelto el shell.
5. **Sujetos no-personal** (prestador, proveedor) cuando el cliente responda por los portales.
