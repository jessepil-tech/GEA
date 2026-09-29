# Migración de identidad, autenticación y autorización

Diseño del modelo de identidad destino. Complementa el capítulo 5.1 del
[dossier de migración](dossier-migracion.md), que describe el problema; este documento
resuelve cómo se reemplaza.

Todas las cifras están medidas contra la base (ver `tools/relevamiento/out/VERIFICACION.md`),
no inferidas del código.

---

## 1. Por qué este diseño va primero

Porque no es una funcionalidad, es un supuesto transversal. El sistema actual no tiene una
capa de autenticación que se pueda sustituir: **la identidad del usuario es la conexión a
la base**, y de ahí la derivan tanto el código de negocio como la auditoría.

La medida del acoplamiento: la función `TS.GENERAL.f_get_id_personal_logeado`, que traduce
el usuario de Oracle a un legajo, aparece **745 veces en 322 fuentes PL/SQL**. En el Java
aparece una sola vez. Es decir que el sistema no propaga la identidad hacia la base: la
base la deduce sola, en cualquier punto del código, sin que nadie se la pase.

Cualquier decisión sobre pooling de conexiones, sobre el esquema de PostgreSQL o sobre la
forma de los servicios Quarkus depende de haber resuelto esto antes. Es el condicionante
que no admite postergarse a una fase posterior.

---

## 2. Cómo funciona hoy

### 2.1 Tres modelos de identidad conviviendo

**Personal interno: usuario nativo de Oracle.** No hay tabla de usuarios con contraseñas.
Cada persona que usa el sistema es un usuario de la base. El alta, la baja, el bloqueo y
el cambio de contraseña se hacen con un package propio del esquema `SYSTEM`,
`dbms_user_security`, a través de `p_add_user`, `p_drop_user`, `p_lock_user`,
`p_unlock_user`, `p_change_user_password`, `p_is_user_locked` y `f_get_user_password`. Cada
sesión web mantiene su propia conexión con las credenciales de ese usuario.

Y el login, literalmente, **es un intento de conexión**. `SecurityManager.validateSecurity`
hace `DriverManager.getConnection(url, user, pass)`: si Oracle acepta, la credencial es
válida. No hay verificación de contraseña en la aplicación porque no hay contraseña en la
aplicación.

**Portales web: cuenta de servicio más contraseña de aplicación.** Los portales no
conectan con el usuario final. Usan una cuenta de servicio dedicada (`PACIENTE_TS`,
`PRESCRIPTOR_TS`, `TRIAGE_TS`, `AUTORECEPCION_TS`, `ANUNCIADOR_TS`) y validan al usuario
contra la columna `CONTRASENA_WEB` de su tabla, con el mail de `TS.MAIL_PERSONA` como
identificador y `CUENTA_WEB_VALIDADA` como habilitación. El servicio
`ANUNCIADOR/api_seguridad_nodejs` emite un JWT propio, HS256, con doce horas de vigencia.

**Equipos e integraciones: cuentas técnicas.** Interfaces de laboratorio, trazabilidad,
mensajería y trabajos programados tienen cuentas propias.

Conviene notar que el patrón que necesitamos para Quarkus, pool compartido más
autorización en la aplicación, **ya existe en el sistema**: es el de los portales. No hay
que inventarlo, hay que generalizarlo.

### 2.2 La base pregunta quién sos, y se responde sola

`f_get_id_personal_logeado` es el corazón del modelo:

```sql
if (user = 'TS' or user = 'HIBERNATE' or user like '%SCHEDULER%'
    or user in ('INTERFACES_TS', 'PACIENTE_TS', ... )) then
    ln_id_personal := 1;
else
    select p.id_personal into ln_id_personal
      from ts.personal p
     where lower(p.login_name) = lower(user);
end if;
```

Tres cosas para destacar. Primero, la identidad se obtiene de `USER`, la función de Oracle
que devuelve el usuario de la sesión: no hay parámetro, no hay contexto, no hay nada que
la aplicación pueda pasar. Segundo, las cuentas de servicio se resuelven a
**`ID_PERSONAL = 1`**, un pseudo-usuario del sistema, con lo cual toda la actividad de los
portales y las integraciones ya hoy queda sin atribución real. Tercero, la lista de
cuentas de servicio está **escrita a mano dentro de la función**.

### 2.3 La auditoría depende de la conexión

Este es el punto que hace el problema más grande de lo que parece. **1.430 triggers
estampan la atribución así:**

```sql
:new.fecha_last_update := sysdate;
:new.actualizado_por   := user;
```

No es un detalle de implementación: es el registro de quién modificó qué, sobre 283 tablas
de auditoría y 83 GB de datos. Si la aplicación pasa a usar un pool compartido sin resolver
esto, **todos los registros de auditoría van a decir el nombre del usuario técnico de la
aplicación** y el sistema pierde trazabilidad clínica. En un hospital eso no es deuda
técnica, es un problema regulatorio.

### 2.4 La autorización, en cambio, es portable y plana

Dos buenas noticias que acotan mucho el trabajo.

La autorización de negocio vive **en tablas**, no en privilegios del motor:
`PERFIL_ACCESO` con 169 perfiles, `MENU_APLICACION` con 1.240 ítems, `MENU_PERFIL_ACCESO`
con 15.666 asignaciones, `PERSONAL_PERFIL_ACCESO` con 6.940 y `ROL_FUNCIONAL_PERS` con
16.282. El package `TS.SEGURIDAD` que las consulta tiene 286 líneas y es hoja del grafo de
dependencias, así que se puede portar aislado del resto del PL/SQL.

Y en el motor, **la autorización es un solo rol plano**: `USUARIO_THINKSOFT` está otorgado
a los 4.091 usuarios de aplicación. No hay una jerarquía de roles de Oracle que replicar ni
privilegios por objeto por persona. Se reemplaza por un único rol de PostgreSQL para la
aplicación.

### 2.5 La receta exacta de las contraseñas de portal

Este dato es imprescindible para poder validar las credenciales existentes en el sistema
nuevo, y no se puede adivinar. Hubo que extraer el package `SYSTEM.DBMS_USER_SECURITY`, que
la extracción original no tomó porque vive fuera del esquema `TS` (652 líneas, ahora en
`out/plsql/package_body/SYSTEM.DBMS_USER_SECURITY.sql`).

La función `f_get_paciente_password`, contra lo que sugiere su uso, **no hashea nada**:

```sql
if (ls_case_sensitive = 'TRUE') then
    ls_password := 'THINKSOFT' || as_new_password;
else
    ls_password := 'THINKSOFT' || lower(as_new_password);
end if;
return ls_password;
```

El hash lo aplica después el llamador. En Java, `Utils.getHash(data, key, algorithm)`
resuelve `SHA-256(key || data)` y se invoca siempre con la clave `"THINKSOFT"`. En el
servicio Node es idéntico:

```js
let contrasena = `THINKSOFT${text.toLowerCase()}`
const textHash = crypto.createHash('sha256').update(contrasena).digest('hex');
```

O sea que el valor guardado en `CONTRASENA_WEB` es:

**`SHA-256("THINKSOFT" + minúsculas(contraseña))`, en hexadecimal.**

Eso explica los 64 caracteres uniformes y agrega tres datos que antes no teníamos:
`"THINKSOFT"` es un *pepper* global fijo, escrito en el fuente en PL/SQL, en Java y en
JavaScript; la contraseña se pasa a minúsculas, lo que reduce el espacio de claves de forma
drástica; y no hay sal por usuario.

**Y hay una inconsistencia que es un defecto en producción.** Todas las pantallas de login
bajan a minúsculas antes de hashear, pero `BBCambiarPassword` no lo hace:

```java
// BBLoginPrescriptor, BBLoginProveedor, BBLoginLaboratorio: minúsculas
Utils.getHash(contrasena.toLowerCase(), "THINKSOFT", "SHA-256")

// BBCambiarPassword: sin minúsculas
Utils.getHash(passwordAnterior, "THINKSOFT", "SHA-256")
```

Consecuencia: quien cambia su contraseña por esa pantalla usando mayúsculas queda con un
hash que la pantalla de login no puede reproducir, y no puede volver a entrar. Para la
migración importa mucho: **el conjunto de 146.312 hashes no es homogéneo**, así que la
verificación transparente va a fallar para un subconjunto desconocido y hay que preverlo
en lugar de descubrirlo en el corte.

Dos detalles menores del mismo orden. `MessageDigest.update(data.getBytes())` usa el
juego de caracteres por defecto de la JVM, así que las contraseñas con acentos dependen de
cómo esté configurado el servidor. Y las columnas de credencial son más que las cuatro
conocidas: además de `CONTRASENA_WEB` existen `CONTRASENA_PORTAL_FARMACIA` y
`PASSWORD_AUTOGESTION`, esta última usada por el login de laboratorio.

### 2.6 Cincuenta y siete cuentas de servicio

La base tiene 57 cuentas técnicas, de las cuales **29 son `SCHEDULER` numeradas**
(`SCHEDULER`, `SCHEDULER1` … `SCHEDULER29`), aparentemente una por trabajo programado para
poder correr en paralelo. El resto corresponde a portales, interfaces de laboratorio,
trazabilidad, imágenes, mensajería de WhatsApp, pagos y un gateway de comunicaciones.

Cada una de esas cuentas es un canal de integración que hay que inventariar y reemplazar
por un cliente autenticado en el sistema nuevo. Es, de hecho, el inventario más completo
de integraciones que tenemos.

---

## 3. Lo que hay que migrar, medido

### 3.1 El personal, con un dato incómodo

| Estado del legajo | Legajos | Con `LOGIN_NAME` | Usuarios Oracle vigentes |
|---|---:|---:|---:|
| ACTIVO | 2.906 | 2.791 | **1.376** |
| BAJA | 3.468 | 3.324 | **2.584** |
| SUSPENDIDO | 13 | 13 | 10 |
| Total | 6.387 | 6.128 | 3.970 |

Hay dos anomalías acá, y las dos importan.

**2.584 legajos dados de baja conservan su usuario de Oracle abierto.** El proceso de
egreso no elimina la cuenta. Son dos tercios de las cuentas vigentes del sistema
pertenecientes a gente que ya no trabaja en la institución. La migración no debe
recrearlas: es la oportunidad de cerrar eso de una vez.

**Solo 1.376 de los 2.906 legajos activos tienen cuenta.** Es esperable en parte, porque
`PERSONAL` incluye a todo el plantel y no todos usan el sistema. Pero contrasta con los
**1.629 a 1.665 usuarios distintos que se loguean por mes** según la auditoría de logins:
se conectan más usuarios distintos por mes que legajos activos con cuenta. La explicación
más probable es uso de cuentas de personas dadas de baja o cuentas compartidas. Hay que
preguntarlo antes del corte, porque define a quién se le da acceso en el sistema nuevo.

En cualquier caso, la población a recrear **no son 6.387 ni 3.970: son del orden de 1.400 a
1.700 cuentas**, y hay que decidir explícitamente el criterio.

### 3.2 Las poblaciones con contraseña de aplicación

| Población | Registros | Con contraseña | Cuenta validada |
|---|---:|---:|---:|
| Pacientes | 860.298 | 146.197 | **128.340** |
| Profesionales matriculados | 11.591 | 115 | **87** |
| Proveedores | 1.348 | 10 | — |
| Farmacias externas | 1 | 0 | — |

El portal de pacientes es el único con uso real. Los otros tres, con 87, 10 y ninguna
cuenta activa, deberían entrar al plan con peso mínimo o directamente discutirse si se
migran.

Sobre el algoritmo, los **146.312 hashes miden exactamente 64 caracteres, sin una sola
excepción**, consistente con la receta de 2.5. Eso habilita una migración transparente: el
sistema nuevo verifica la contraseña reproduciendo
`SHA-256("THINKSOFT" + minúsculas(contraseña))` y, si coincide, reemplaza el hash por uno
moderno en ese mismo login. Con la salvedad del defecto de mayúsculas: para el subconjunto
afectado hay que ofrecer recuperación por mail, que ya existe.

### 3.3 No hay política de contraseñas que preservar

El perfil `DEFAULT`, que usan las 4.091 cuentas de aplicación, tiene todo en `UNLIMITED`:

| Parámetro | Valor actual | Consecuencia |
|---|---|---|
| `FAILED_LOGIN_ATTEMPTS` | UNLIMITED | Se puede probar contraseñas indefinidamente |
| `PASSWORD_LIFE_TIME` | UNLIMITED | Las contraseñas no caducan nunca |
| `PASSWORD_LOCK_TIME` | UNLIMITED | Sin bloqueo temporal |
| `PASSWORD_REUSE_MAX` | UNLIMITED | Se puede reutilizar la misma siempre |
| `PASSWORD_VERIFY_FUNCTION` | NULL | Sin requisitos de complejidad |

Visto como riesgo, es grave: 4.091 cuentas sin caducidad, sin complejidad y sin límite de
intentos fallidos, de las cuales 2.584 son de gente que ya no está. Visto como migración,
simplifica: no hay reglas heredadas que replicar, hay que definirlas por primera vez.

---

## 4. Defectos a no copiar

El modelo actual tiene siete problemas que conviene enumerar explícitamente, porque la
tentación en una migración es reproducir el comportamiento observado.

**La suplantación reescribe la contraseña de la víctima.** `TS.SEGURIDAD.f_bypass_user_login`
lee el hash de la contraseña desde `SYS.USER$`, ejecuta un `ALTER USER ... IDENTIFIED BY`
con una contraseña temporal y **devuelve el hash original al llamador** para que después lo
restaure. Durante esa ventana el usuario legítimo no puede entrar y su hash está en manos
de la aplicación. Además la contraseña temporal se concatena en el DDL, con lo que hay
inyección. Y el manejador de excepciones es `when others`, que oculta el error real.

**La autorización falla abierta.** `f_tiene_permiso` verifica el perfil solo si la acción
está registrada en `MENU_APLICACION`; si no está, concede el acceso. Y compara con `LIKE`
sobre un patrón recibido por parámetro.

**La lista blanca de cuentas de servicio está desincronizada.** La función menciona seis
cuentas que no existen en la base (`API_TS`, `RECEPCION_TS`, `ENCUESTA_TS` y las tres
`API_LAB_EQ*_RESULT`), y hay siete cuentas reales que la función no contempla
(`ANUN_ESP_SERV_TS`, `ANUN_PAC_RECEP_TS`, `ANUN_TRIAGE_TS`, `ANUN_UNIFICADO_TS`,
`GATEWAY_COMM_TS`, `PARAMS_TS`, `INTERFACE`). Esas siete, si ejecutan código que llame a
`f_get_id_personal_logeado`, reciben un error de aplicación.

**Semilla criptográfica fija en el código.** `SecurityManager.java` de la consola de
seguridad lleva el valor `3183856184` escrito en el fuente.

**Las bajas conservan la cuenta.** 2.584 casos.

**Sin política de contraseñas.** Ver 3.3.

**Hash débil y contraseñas mutiladas.** `SHA-256` con *pepper* fijo y sin sal para las
146.312 contraseñas de aplicación, más el paso a minúsculas que reduce el espacio de
claves. Permite ataques por tabla precalculada y revela cuándo dos usuarios comparten
contraseña.

**Hashes inconsistentes según la pantalla que los generó**, por el defecto de mayúsculas
descrito en 2.5.

---

## 5. Diseño destino

**Decisión de proyecto: no se usa OIdentity ni ningún IdP externo.** Toda la autenticación
humana vive en la aplicación, con el modo `local` del starter Quarkus (JWT firmado por la
app + tablas de identidad propias + BCrypt). Las integraciones van por API Key. OIDC y el
perfil `oidc-oidentity` del starter quedan fuera de alcance.

**Gate pendiente (prioridad 2026-08-14):** confirmar con negocio/IT que producción
**no** exige IdP corporativo (AD/OIDC). Si basta el emisor propio, la oleada A es el
destino (no un puente barato a rehacer). Si exige IdP, planificar el puente **antes**
de escalar más clientes — no en Fase 6. Ver
[`sdd/ajustes-prioridad-migracion.md`](../planificacion/ajustes-prioridad-migracion.md) A2.

Apoyado en lo que ya traen los starters corporativos (Quarkus
`docs/development/guides/USO_AUTENTICACION_AUTORIZACION.md` y Angular
`docs/architecture/ARQUITECTURA_ANGULAR_STARTER.md`, ambos en
`/Volumes/External/Development/starters/`), no en un modelo inventado. Donde el hospital
necesita algo que el starter no trae, se dice explícitamente: es extensión, no convención
existente.

### 5.1 Qué se reutiliza tal cual

| Capacidad | Origen | Uso en Hospital |
|---|---|---|
| Modo `local` con JWT firmado por la app | Quarkus starter, perfil `local-jwt` | **Todas** las poblaciones humanas: personal y portales |
| Tablas tipo ASP.NET Identity + BCrypt | Quarkus starter (`users`, `roles`, `user_roles`, `user_claims`, `sessions`) | Almacén único de credenciales y claims |
| API Key (`X-API-Key`) | Quarkus starter, `ApiKeyAuthenticationMechanism` | Las 57 cuentas de servicio (`*_TS`, `SCHEDULER*`, interfaces) |
| Feature `auth/` Angular en modo `local` | Angular starter (`authMode=local`) | Shell de autenticación (login, refresh, logout, recuperar contraseña) |
| `@RolesAllowed` sobre roles del token | Quarkus starter | Protección gruesa de endpoints |
| Claims `Permission` en el JWT | Quarkus starter, `ClaimsProviderService` | Base para la autorización fina (hoy se emiten, no se enforcean) |

Tres reglas del diseño:

1. Modo humano único: `features.identity-mode=local`. No hay OIDC en este proyecto.
2. Las integraciones van por API Key y llevan el rol `ApiKeyUser`, que no se asigna a
   personas.
3. Un solo almacén de usuarios en la base de la aplicación; Oracle deja de ser el
   directorio de identidad.

### 5.2 Qué hay que agregar, porque el starter no lo trae

Estos gaps no son opcionales para un hospital. Sin ellos se pierde trazabilidad clínica o
se degrada el modelo de permisos actual.

| Gap | Por qué importa acá | Dónde se extiende |
|---|---|---|
| Propagar el **usuario actual** a la capa de datos | 1.430 triggers estampan `ACTUALIZADO_POR := USER`; con pool compartido se pierde la atribución | Quarkus: `SecurityIdentity` → contexto de request → `SET LOCAL app.current_user` en PostgreSQL (y equivalente en Oracle durante la convivencia) y `createdBy`/`updatedBy` en entidades nuevas |
| Actor en la auditoría | El `@Auditable` del starter registra *qué* cambió, no *quién* | Extender `AutomaticAuditLogService` / `AuditLogContent` con `actorId` y `actorLogin` |
| Autorización por **permiso de menú** | El hospital tiene 15.666 asignaciones `MENU_PERFIL_ACCESO`; el starter solo enforcea roles | Quarkus: `@PermissionsAllowed` o interceptor propio sobre claim `Permission`. Angular: guard + directiva `*hasPermission` (no existen hoy) |
| Suplantación auditada | Hoy reescribe la contraseña de la víctima | Claim `act` (actor) + `sub` (suplantado) en el token, sin tocar credenciales |
| Política de contraseñas | El motor actual no tiene ninguna | Implementarla en el modo `local` (longitud, complejidad, caducidad, bloqueo, historial) |
| Reforzado de hash en el primer login | 146.312 hashes SHA-256 con pepper fijo | Verificador dual: acepta la receta legacy y reescribe a BCrypt |
| Alta masiva de personal con restablecimiento | Las contraseñas de Oracle no son recuperables | Flujo de primer ingreso / reset forzado sobre la tabla `users` |

Ninguno de estos puntos se inventa en paralelo al starter: se agregan sobre sus puntos de
extensión (`infrastructure/`, `presentation-api/security/`, feature `auth/` en Angular,
tablas de claims del modo `local`).

### 5.3 Mapa de poblaciones al modelo nuevo

```
                    ┌─────────────────────────────────────────┐
                    │     Tablas de identidad de la app        │
                    │  users / roles / claims / sessions       │
                    │  (modo local del starter Quarkus)        │
                    └───────────────────┬─────────────────────┘
                                        │ JWT Bearer (RS256)
                    ┌───────────────────▼─────────────────────┐
                    │         Quarkus presentation-api         │
                    │         local JWT  +  API Key            │
                    └─┬───────────────┬───────────────┬───────┘
                      │               │               │
           personal Angular       portales web    integraciones
           (historia, internac.)  (pacientes,     (*_TS → API Key)
                                  profesionales)
```

| Población | Autenticación destino | Credencial en el corte | Notas |
|---|---|---|---|
| Personal ACTIVO con usuario Oracle vigente (~1.376) | JWT local | Restablecimiento obligatorio | Criterio fino: solo `ESTADO='ACTIVO'` y cuenta `OPEN` |
| Personal BAJA con cuenta abierta (2.584) | **No migrar** | Revocar en Oracle ahora | Limpieza previa, no parte del corte |
| Pacientes con cuenta validada (128.340) | JWT local | Verificación transparente + reforzado | Receta: `SHA-256("THINKSOFT"+lower(pass))` |
| Profesionales / proveedores / farmacias (~97) | JWT local | Igual que pacientes | Uso marginal; peso mínimo en el plan |
| 57 cuentas de servicio | API Key | Rotación al alta | Una clave por integración; rol `ApiKeyUser` |

La decisión de cuántos internos se dan de alta (1.376 vs ~1.650 activos mensuales) queda
abierta hasta confirmar con el cliente el origen de la diferencia (sección 3.1). Todos
entran al mismo almacén `users`; lo que cambia es el claim de tipo de sujeto
(`PERSONAL`, `PACIENTE`, `PROFESIONAL`, …) y los roles/permisos asociados.

### 5.4 Autorización: roles gruesos + permisos finos

El starter enforcea **roles**. El hospital necesita **permisos de menú**. La propuesta es
usar ambos, sin mezclarlos:

**Capa gruesa (lo que el starter ya sabe hacer).** Cada `PERFIL_ACCESO` se emite como rol
en el token (`role` / `groups`), respaldado por las tablas `roles` / `user_roles` del
modo `local`. Los endpoints se marcan con `@RolesAllowed`. En Angular, un guard nuevo
`hasRoleGuard` (hoy no existe) protege rutas por rol.

**Capa fina (extensión).** Los 15.666 `MENU_PERFIL_ACCESO` se cargan como claims
`Permission` vía `ClaimsProviderService` (el starter ya los emite en modo `local`, pero
**no los enforcea**). Hay que agregar:

- En Quarkus: un chequeo por permiso, preferentemente `@PermissionsAllowed` o un
  interceptor CDI que lea el claim. `f_tiene_permiso` se reimplementa fallando
  **cerrado**: si la acción no está registrada, se niega.
- En Angular: una directiva `*hasPermission` y un guard de ruta que lean el mismo claim.
  El feature `auth/` ya es el lugar natural; el perfil ya expone `roles: string[]` como
  dato, falta la lógica.

La consola `seguridad/` se reescribe como módulo Angular + API Quarkus sobre las mismas
tablas de negocio (`PERFIL_ACCESO`, `MENU_*`, `PERSONAL_PERFIL_ACCESO`,
`ROL_FUNCIONAL_PERS`) y sobre las tablas de identidad del starter (`users`, `roles`,
`user_roles`, `user_claims`), sin los dos subsistemas muertos. La administración deja de
tocar usuarios del motor Oracle.

### 5.5 Propagación de identidad hacia la base

Este es el gap más caro de no resolver. Hay dos horizontes alineados con la
convivencia **dual-runtime** del dossier (§6, 2026-08-13): legacy en Oracle 11.2,
código nuevo en PostgreSQL.

**Tráfico legacy (sigue en Oracle 11.2).** Quien aún conecta por usuario nativo
sigue con `USER` en triggers. No hay pool Quarkus escribiendo el schema clínico
completo en 11.2.

**Módulos Quarkus (PostgreSQL, Fases 3–6).** Pool con cuenta técnica. Al abrir la
transacción: `SET LOCAL app.current_user = '...'` con el `login` del JWT, y
`current_setting('app.current_user')` en defaults / funciones de compatibilidad. Un
solo rol de aplicación reemplaza a `USUARIO_THINKSOFT`.

**Puente excepcional a 11.2.** Si un servicio Quarkus debe invocar un package aún no
portado, puede usar JDBC/`CallableStatement` puntual. La atribución en ese camino
sigue siendo responsabilidad del PL/SQL legacy (`USER` o contexto que se defina en el
wrapper); no se diseña Panache sobre el schema 11.2.

**En el modelo nuevo (entidades Quarkus).** Extender `@Auditable` con `actorId` /
`actorLogin` tomados de `SecurityIdentity`, y agregar `createdBy` / `updatedBy` en
`BaseEntity`. El starter audita el *qué*; el hospital necesita también el *quién*.

### 5.6 Suplantación, sin reescribir credenciales

La función actual se elimina. El caso de uso legítimo (soporte que atiende como otro
usuario) se modela con un token emitido por la API en modo `local`:

- `sub`: el usuario suplantado
- `act` (claim estándar RFC 8693): el operador que inicia la suplantación
- permiso explícito `impersonate`, otorgado a muy pocos perfiles
- auditoría propia: alta, motivo, fin, cada acción bajo el token

Ni Oracle ni PostgreSQL cambian ninguna contraseña. El usuario legítimo sigue entrando
durante toda la ventana. La consola de seguridad deja de llamar a
`f_bypass_user_login` / `f_reestablecer_user_pass`.

### 5.7 Migración de credenciales

Hay dos caminos distintos, porque las contraseñas del personal **no están en la
aplicación**.

**Portales (hash recuperable).**

1. En el corte, las columnas `CONTRASENA_WEB` (y `PASSWORD_AUTOGESTION`,
   `CONTRASENA_PORTAL_FARMACIA`) se copian tal cual a la tabla `users` del modo `local`,
   con un marcador de esquema `legacy-sha256-thinksoft`.
2. El `UserAuthenticationService` verifica en dos pasos: si el esquema es legacy,
   reproduce `SHA-256("THINKSOFT" + lower(password))`; si coincide, reescribe a BCrypt
   (el que ya usa el starter) y limpia el marcador.
3. Si no coincide, se prueba **sin** `toLowerCase`, para cubrir el defecto de
   `BBCambiarPassword`. Si tampoco, se ofrece recuperación por mail, que ya existe.

**Personal interno (hash no recuperable).**

1. Se crea la fila en `users` con el `login_name` actual, sin contraseña usable, y un
   flag de *must change password*.
2. En el corte se dispara el flujo de restablecimiento (mail o entrega controlada) para
   los ~1.376 activos con cuenta vigente.
3. Hasta que cada uno complete el restablecimiento, no entra al sistema nuevo. El legacy
   sigue autenticando contra Oracle para quien aún no migró.

No hay segundo destino: personal y portales terminan en el mismo almacén `users`,
distinguidos por claims de tipo de sujeto.

### 5.8 Convivencia durante la transición

Mientras Hospital_2 y los WARs satélite sigan vivos, coexisten tres formas de entrar:

| Tráfico | Cómo autentica | Cómo se atribuye en la base |
|---|---|---|
| Legacy JSF (personal) | Conexión Oracle por usuario, como hoy | `USER`, sin cambios |
| Servicios Quarkus nuevos (personal y portales) | Bearer JWT local | Contexto de sesión (`p_set_usuario`) |
| Integraciones | API Key | Pseudo-usuario por clave, mapeado a la lista blanca |

La lista blanca de `f_get_id_personal_logeado` se reemplaza por una tabla
`CUENTA_SERVICIO` versionada, para no volver a desincronizar el código con la realidad.
Los 29 `SCHEDULER*` se consolidan en una o pocas claves de API Key con identidad de
trabajo, no una cuenta Oracle por job.

### 5.9 Política de contraseñas nueva

Como el perfil `DEFAULT` de Oracle no impone nada, y no hay IdP externo, la política se
implementa en la aplicación (validación en el `UserAuthenticationService` y en el cambio
de contraseña). Propuesta mínima:

| Regla | Valor propuesto |
|---|---|
| Longitud mínima | 10 |
| Complejidad | mayúscula + minúscula + dígito |
| Caducidad | 180 días (personal); sin caducidad (portales, se refuerza el hash) |
| Intentos fallidos | 5, bloqueo 15 minutos |
| Reuso | no las últimas 5 |
| Restablecimiento | solo por canal verificado (mail) |

### 5.10 Trabajo a entregar, en orden

1. **Limpieza previa en Oracle** (no es migración): revocar las 2.584 cuentas de bajas;
   opcionalmente aplicar una política mínima al perfil `DEFAULT` mientras el legacy viva.
2. **Extensiones al starter Quarkus**: actor en `@Auditable`, `createdBy`/`updatedBy`,
   chequeo de `Permission`, filtro que ejecuta `p_set_usuario`, verificador dual de hash
   legacy, política de contraseñas, emisión de token de suplantación, flujo de primer
   ingreso para personal.
3. **Extensiones al starter Angular**: `authMode=local` fijo, `hasRoleGuard`,
   `hasPermissionGuard`, directiva `*hasPermission`, pantallas de restablecimiento y de
   suplantación para soporte. Sin rutas OIDC.
4. **Carga inicial de `users`**: personal activo con reset forzado; portales con hash
   legacy marcado; plan de comunicación del restablecimiento.
5. **Reescritura de la consola `seguridad/`** como módulo del sistema nuevo, sin los
   subsistemas muertos y sin DDL sobre usuarios del motor.
6. **Inventario y rotación de las 57 cuentas de servicio** a API Keys.
7. **Oleada de triggers**: reemplazar `USER` por el contexto de sesión, empezando por las
   tablas del circuito ambulatorio (los módulos más usados, sección 3.5 del dossier).

### 5.11 Forma de despliegue: servicio de identidad, no IdP externo

Quedó acordado en la conversación de diseño:

1. **No se usa OIdentity.** El hospital tiene un emisor propio de tokens con el modo
   `local` del starter Quarkus.
2. Ese emisor es el **servicio de identidad** de la plataforma: login, refresh, users,
   roles, permisos, API Keys y suplantación. Centraliza identidad; no el negocio clínico.
3. **No se migra el WAR `seguridad/` para que haga de login.** Ese WAR es consola de
   administración. Se reescribe después como cliente Angular del servicio.
4. **`ANUNCIADOR/api_seguridad_nodejs` se absorbe** en este servicio (hoy ya es el
   login/JWT de portales).
5. El emisor vive en el repo/deploy **`Hospital-Identity`** (F2, 2026-08-13): login,
   refresh, users, API Keys, permisos en token. La solución de negocio **Hospital** y los
   fronts son clientes: validan JWT en local y no reinventan login. Oleada A es el primer
   entregable de esa plataforma.
6. Las APIs de negocio **no llaman a Identity en cada request**; solo verifican la firma
   del Bearer (JWKS / clave pública).

Contrato mínimo de la API: [`docs/contrato-api-identidad.md`](contrato-api-identidad.md).

### 5.12 Decisiones que este diseño no toma solo

Quedan para validar con el cliente:

- Criterio exacto de quién entra al almacén `users` como personal (1.376 activos con
  cuenta vs ~1.650 logueados por mes).
- Si los portales de profesionales, proveedores y farmacias se migran o se retiran, dado
  su uso residual.
- Canal y operador del restablecimiento masivo del personal (mail, entrega en mesa de
  ayuda, por servicio).
- Quién opera la suplantación (mesa de ayuda, jefatura por servicio) y con qué retención
  de auditoría.
- Inventario de consumidores externos (QlikView, Excel, scripts) y si se reapuntan a
  PostgreSQL o se reemplazan por API.

---

## 6. Relación con el resto del plan

Este diseño es prerequisito de la Fase 1 del
[dossier](dossier-migracion.md) y se implementa como cimiento del piloto AGI + ANUNCIADOR
(Fase 3): esos dos módulos ya usan el patrón de cuenta de servicio, así que son el lugar
natural para estrenar API Key + JWT local + contexto de sesión, con el personal interno
entrando al mismo modo `local` en cuanto el flujo de restablecimiento esté listo.
ANUNCIADOR, en particular, deja de depender de `api_seguridad_nodejs`.

El package `GENERAL` ([diseño propio](migracion-package-general.md)) aporta
`f_get_id_personal_logeado`, que es el punto de enganche de la sección 5.5. Se migra junto
con este trabajo, no después.
