# Dossier de migración: Hospital legacy → Quarkus + Angular + PostgreSQL

Documento de referencia para planificar la migración. Consolida el relevamiento de
la base Oracle de preproducción con el análisis del código del monorepo.

- **Fecha del relevamiento:** agosto 2026
- **Base relevada:** Oracle 11.2.0.4, esquema `TS` (copia diaria de producción; ver §2.0)
- **Destino:** Quarkus 3.20 sobre Java 21, Angular 21, PostgreSQL
- **Origen de los datos:** extracción de solo lectura del diccionario de datos
  (`tools/relevamiento/`) y análisis estático del código
- **Prioridades inmediatas:**  
  [`docs/sdd/gobierno-migracion.md`](../canon/gobierno-migracion.md) (capas de decisión) ·
  [`docs/sdd/backlog-orden-2026-08-14.md`](../planificacion/backlog-orden-2026-08-14.md) (orden diario) ·
  [`docs/sdd/estado-piloto-vs-general.md`](../estado/estado-piloto-vs-general.md) (**snapshot 2026-08-21**) ·
  [`docs/sdd/ajustes-prioridad-migracion.md`](../planificacion/ajustes-prioridad-migracion.md)
  (carril paralelo: v$sql, BI, inyección SQL, Identity IdP, lab) ·
  [`docs/sdd/regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md)
  (**DDL canónico = PG migrado `ts`**, 2026-08-20).

---

## 1. Resumen ejecutivo

El sistema es grande, pero **bastante menos grande de lo que sugieren las cifras
brutas**, porque una parte importante del volumen es infraestructura repetitiva y
no lógica que haya que decidir caso por caso.

| Dimensión | Cifra bruta | Lo que realmente hay que migrar |
|---|---|---|
| Líneas de Java | 2.184.729 | 1.939.311 escritas a mano (245.418 son stubs generados) |
| Líneas de PL/SQL | 397.580 | 347.759 en 61 packages de negocio (el resto es auditoría) |
| Triggers | 1.815 | 126 con lógica real (1.689 son auditoría mecánica) |
| Packages Oracle | 326 | 61 de negocio (264 son plomería `TBL_AUD_*`) |
| Tablas | 2.279 | 1.040 operativas con datos (1.012 vacías, 392 temporales) |
| Filas | 1.429 millones | 415 millones operativos (942 millones son auditoría) |
| Vistas JSF | 3.301 | 3.301, de las cuales 2.917 en `HOSPITAL_2` |
| Managed beans | 2.503 | 2.458 son `@ViewScoped` (estado de pantalla) |
| Volumen en disco | 215 GB | ~90 GB de negocio (83 de auditoría, 40 de colas de salida) |
| Módulos | 39 puntos de entrada | 20 concentran el 98% del uso medido |

**Los siete hallazgos que condicionan el plan** están en la sección 5. Dos técnicos
importan más que el resto. El más disruptivo para el usuario es que **la autenticación del
personal usa usuarios nativos de Oracle**, lo que obliga a reconstruir el modelo de
identidad y a que esos 3.970 internos cambien su contraseña en el corte; el resto de
las poblaciones, que ya usa contraseñas de aplicación, migra sin cambios. El más
disruptivo para el plan es que **el 84% del PL/SQL de negocio forma un solo bloque
mutuamente recursivo**, lo que descarta migrar dominio por dominio.

El sistema además **registra su propio uso** desde 2021, y eso da una base empírica poco
común para ordenar el trabajo: 9,7 millones de accesos a módulo permiten priorizar por lo
que el hospital realmente usa en lugar de por tamaño de código. Está en la sección 3.5 y
cambió el orden propuesto en Fase 4.

El séptimo condicionante es de **producto** (2026-08-13): un shell clínico, **un** core
de negocio Hospital, e **Identity (F2)** como servicio/repo aparte. Mapa en
[`docs/mapa-productos-destino.md`](mapa-productos-destino.md).

**Ajuste de runtime BD (2026-08-13):** Oracle **11.2 no es runtime viable** para
Quarkus 3 / Hibernate ORM 6 (mínimo soportado ~19). No se plantea upgrade 11→19 como
puente (sería otra migración de motor). El código nuevo corre sobre **PostgreSQL desde
el día uno**; el 11.2 sigue como referencia del legacy y como **oráculo de verdad** vía
golden master. **Go-live:** UAT en paralelo aislado → un corte (no strangler por
defecto). Detalle en §6. Análisis A/B/C:
[`analisis-oracle19-vs-postgres.md`](analisis-oracle19-vs-postgres.md).

**Ajuste DDL (2026-08-20/21):** el destino de datos de dominio no es un modelo
simplificado inventado en `public` (p.ej. `llamado_paciente`). La **estructura
canónica** es la del **PostgreSQL ya migrado**, schema **`ts`**: mismos nombres de
tablas/columnas y misma cantidad de columnas que Oracle; solo tipos pueden diferir
(catálogo documentado). Ver §6.5.

---

## 2. Estado actual: la base de datos

### 2.0 De dónde salen estas cifras

Todo lo que sigue está medido sobre la **copia** (`hosprod` / `srv-pora-db01`), no
sobre el host de producción en vivo.
Conviene tener presente su procedencia para interpretar las fechas.

**Actualización 2026-08-14 (ops):** la instancia a la que apunta la VPN es una
**copia diaria de producción**. Para golden master, fuentes PL/SQL e inventarios
(`sec_id_*`, firmas), los datos se tratan como **fiables y recientes**.

La instancia se llama `hosprod` y corre en `srv-pora-db01`, nombres heredados del clonado.
El relevamiento inicial (dossier) la identificó como copia por dos señales de entonces:
**arranque 2026-06-05 10:38** y DDL del diccionario alineado a esa fecha; además un
quiebre de logins (millones/mes → decenas de usuarios) coherente con un clon.
Si el refresh diario reescribe marcas, esas señales del corte de junio pueden quedar
obsoletas: priorizar el criterio ops (copia diaria) para frescura de **datos**.

Dos consecuencias prácticas. Primero, **datos y packages son oráculo válido** para
planificar y para golden master (plan A).
Segundo, **`v$sql` / caché de sentencias de esta copia no sustituye a producción**:
refleja lo ejecutado *en la copia* tras el restore (scheduler, EM, pruebas del equipo),
no el workload clínico real. Esa medición sigue haciendo falta en prod (o AWR exportado).

Un dato lateral valioso: esos **1.650 usuarios distintos por mes** son la dimensión real
de la base de usuarios, muy por debajo de los 6.387 legajos y de los 3.970 usuarios
Oracle vigentes.

### 2.1 Motor y codificación

| Parámetro | Valor | Consecuencia |
|---|---|---|
| Motor | Oracle 11.2.0.4 (64-bit, Linux) | Sin soporte; los drivers modernos ya no lo aceptan en modo *thin* |
| `NLS_CHARACTERSET` | **WE8MSWIN1252** | Es Windows-1252, **no ISO-8859-1** |
| `NLS_LENGTH_SEMANTICS` | BYTE | `VARCHAR2(n)` cuenta bytes; PostgreSQL cuenta caracteres (más permisivo) |
| `NLS_SORT` / `NLS_COMP` | BINARY | A favor: no hay semántica lingüística que replicar |

**Riesgo de codificación.** Windows-1252 define caracteres en el rango `0x80`-`0x9F`
(comillas tipográficas, guion largo, símbolo del euro) que ISO-8859-1 deja sin
asignar. Cualquier conversión a UTF-8 debe declarar `WINDOWS-1252`: hacerlo como
Latin-1 corrompe esos caracteres sin emitir error. El problema está en los **datos**,
no en los fuentes: de 15.049 archivos de texto del repo, 14.975 son ASCII o UTF-8
puro y solo 4 usan ese rango.

### 2.2 Qué hace falta migrar realmente

| Grupo | Tablas | Vacías | Con datos | Filas | GB aprox. |
|---|---:|---:|---:|---:|---:|
| Operativas | 1.576 | 536 | 1.040 | 415.058.335 | 94 |
| Auditoría `AUD_*` | 283 | 82 | 201 | 942.740.671 | 65 |
| Temporales `TMP_*` | 392 | 384 | 8 | 2.000.411 | 1 |
| Log / histórico | 28 | 14 | 14 | 69.397.450 | 7 |

Las cifras de GB de esa tabla son estimadas a partir de estadísticas del diccionario. La
ocupación real, medida sobre los segmentos, es de **215 GB**: 130,2 GB operativos en 1.481
tablas, 83 GB de auditoría en 283, 1,2 GB en las 123 tablas de respaldo con prefijo `O_` y
1 GB en las temporales.

Tres consecuencias de planificación:

- **La auditoría concentra el 66% de las filas** y 83 GB de los 215. No es dato
  operativo: es rastro de cambios. Tratarla como proyecto aparte (archivado histórico o
  carga posterior) saca dos tercios de las filas del camino crítico del corte.
- **Las 392 tablas `TMP_*` son el grupo más numeroso del esquema y solo 8 tienen
  filas.** Son tablas de trabajo de procesos batch; casi ninguna debería migrar.
- **Buena parte del volumen operativo tampoco es dato de negocio, sino cola de salida.**
  La tabla más grande de la base es `ENVIO_MAIL` con 24 GB, seguida por
  `ENVIO_MENSAJE_VALIDADOR` con 11,7 GB y `DET_RESPUESTA_WS_AFIP` con 3,5 GB. Son casi
  40 GB de mensajes enviados y respuestas de servicios externos que se acumularon sin
  política de purga y que no necesitan viajar a PostgreSQL. Conviene decidirlo
  explícitamente: es la diferencia entre migrar 215 GB y migrar del orden de 90.

### 2.3 Integridad declarada

| Tipo | Cantidad | Observación |
|---|---:|---|
| CHECK | 17.001 | Incluye los `NOT NULL`, que en Oracle son constraints CHECK |
| Foreign key | 4.128 | Todas con regla `NO ACTION` |
| Primary key | 1.708 | Quedan 571 tablas sin PK |
| Única | 5 | Llamativamente pocas |

De las **571 tablas sin clave primaria**, 367 son `TMP_*` y solo **91 son operativas
con datos**, varias con prefijo `O_` que sugiere versiones antiguas. La mayor es
`ITEM_FARM_MF` con 5,7 millones de filas. Es un problema acotado, pero hay que
resolverlo antes de migrar esos datos: sin PK no hay forma confiable de validar
equivalencia fila por fila ni de re-ejecutar una carga idempotente.

Hay además **5 constraints deshabilitados y 19 sin validar**. Un constraint
deshabilitado suele significar que ya existen datos que lo violan: si el esquema
PostgreSQL se crea con esas constraints activas, la carga falla.

### 2.4 Tipos de dato

La superficie es angosta y favorable, con una excepción grande.

| Tipo Oracle | Columnas | Tablas | Destino (plan dossier) |
|---|---:|---:|---|
| `NUMBER` sin precisión | **12.420** | 2.200 | Inferir `integer`/`bigint` por rango real |
| `BLOB` | 78 | 55 | Preferible `bytea`; **medido en PG migrado:** `TEXT` (ver catálogo) |
| `CLOB` | 74 | 40 | `text` |
| `TIMESTAMP(6)` | 5 | 4 | `timestamp` / `timestamptz` |
| `ROWID` | 2 | 2 | Reemplazar por la PK |

No hay `LONG`, `NCHAR`, `NVARCHAR2`, `XMLTYPE`, `BFILE` ni tipos `INTERVAL`. El
trabajo se concentra en las **12.420 columnas `NUMBER` sin precisión declarada**,
presentes en casi todas las tablas: traducirlas literalmente a `numeric` cuesta
rendimiento en cada operación aritmética y comparación, así que hay que inferir el
rango real de los datos para elegir un tipo entero.

**Actualización 2026-08-20:** el PostgreSQL migrado (`grupogea-hospital_dev` / `ts`)
ya materializó el mapeo medido (p.ej. mayoría `NUMBER`→`NUMERIC`, `DATE`→`TIMESTAMP`,
`BLOB`/`CLOB`→`TEXT`). Catálogo canónico de tipos y excepciones:
[`sdd/catalogo-tipos-oracle-pg.md`](../relevamiento/catalogo-tipos-oracle-pg.md) ·
inventario: [`sdd/inventario-ddl-oracle-pg/`](../relevamiento/inventario-ddl-oracle-pg/).

### 2.5 Dónde está la lógica

| Grupo | Objetos | Líneas |
|---|---:|---:|
| Packages de negocio | 61 | 347.759 |
| Packages de auditoría `TBL_AUD_*` | 264 | 7.656 |
| Triggers con lógica real | 126 | ~4.500 |
| Triggers de auditoría mecánica | 1.689 | ~22.600 |

**Los 1.689 triggers de auditoría no se traducen uno por uno.** Son de dos clases:
1.156 solo estampan `fecha_last_update` y `actualizado_por`, y 533 registran el
cambio columna por columna llamando al package `TBL_AUD_` de su tabla. En el
destino los reemplaza un único mecanismo transversal en la capa de persistencia.

Los **126 triggers restantes sí son reglas de negocio**: 66 validan y rechazan con
`RAISE_APPLICATION_ERROR` y 38 modifican otras tablas. Cada uno necesita una
decisión explícita sobre si la regla va al dominio o queda en la base, y una prueba
que demuestre comportamiento equivalente.

### 2.6 Los 61 packages de negocio en detalle

Análisis reproducible con `tools/relevamiento/analizar-packages.py`, que genera
`tools/relevamiento/out/PACKAGES.md`. Las cifras de acá son **líneas útiles**, sin
vacías ni comentarios: 308.138 sobre las 347.759 brutas.

| Métrica | Valor |
|---|---|
| Packages de negocio | 62 (61 con cuerpo, más `HIBERNATE_TYPES` que solo declara el tipo `RESULT_SET`) |
| Líneas útiles | 308.138 |
| Rutinas públicas y privadas | 1.870 |
| De ellas devuelven cursor (lectura) | 1.176 |
| Rutinas que escriben | 694 |
| `RAISE_APPLICATION_ERROR` | **5.443** |
| Concentración | **19 packages reúnen el 80% de las líneas** |

Las **5.443 validaciones explícitas** son la cifra más útil para dimensionar: cada
una es una regla de negocio con mensaje al usuario que hay que reimplementar y
probar. No depende de ninguna heurística de clasificación.

**Los diez packages más grandes** concentran el trabajo. `Valida` cuenta
`RAISE_APPLICATION_ERROR`; `Usos` son invocaciones desde el código Java:

| Package | Líneas | Rutinas | Lee | Escribe | Valida | Tablas | Usos |
|---|---:|---:|---:|---:|---:|---:|---:|
| `LABORATORIO` | 26.977 | 220 | 135 | 85 | 443 | 162 | 133 |
| `ATENCION` | 22.739 | 121 | 97 | 24 | 210 | 239 | 100 |
| `FARMACIAS` | 22.033 | 149 | 120 | 29 | 542 | 222 | 147 |
| `FACTURACION` | 19.030 | 120 | 71 | 49 | 351 | 221 | 184 |
| `TURNOS` | 15.371 | 81 | 61 | 20 | 103 | 127 | 59 |
| `LIQUIDACION_HONORARIO` | 14.999 | 72 | 47 | 25 | 158 | 109 | 54 |
| `CAJAS` | 14.337 | 60 | 44 | 16 | 170 | 83 | 47 |
| `PERSONAS` | 13.193 | 60 | 35 | 25 | **964** | **479** | 137 |
| `INTERFACES` | 12.645 | 73 | 62 | 11 | 222 | 244 | 44 |
| `COMPRAS` | 11.145 | 60 | 44 | 16 | 130 | 100 | 52 |

`PERSONAS` merece atención aparte: con 13.193 líneas toca **479 tablas** y tiene
**964 validaciones**, la mayor densidad de reglas de todo el esquema. Es el package
de identidad de pacientes y personal, y lo usan 27 packages más.

#### Los dos hubs

| Package | Lo usan packages | Lo usan triggers |
|---|---:|---:|
| `GENERAL` | 47 | 23 |
| `PERSONAS` | 27 | 1 |

`GENERAL` (3.481 líneas, 85 rutinas) es utilitario transversal e incluye la
generación de identificadores. Es el primer candidato a portar, porque hasta que no
exista su equivalente ningún otro package cierra.

#### Código sin referencias

**Nueve de los 62 packages no se invocan desde Java.** Se dividen en dos grupos:

- **Siete sin ninguna referencia**, ni de Java, ni de otros packages, ni de
  triggers: **11.792 líneas** candidatas a código muerto, encabezadas por
  `UNIFICACION` con 9.029 líneas y 678 validaciones.
- **Dos llamados solo desde la base** (810 líneas), que sí siguen vivos aunque el
  Java no los mencione.

Hay que confirmarlo con `v$sql` de producción antes de descartar nada, porque podrían
ser llamados por jobs o herramientas externas.

Además, siete packages terminan en `_`, el mismo patrón de retiro sin borrado que
las tablas con prefijo `O_`. Conviven `TRADUCCION` y `TRADUCCION_`, y hay que
determinar cuál está vivo antes de portar cualquiera de los dos.

---

## 3. Estado actual: el código

### 3.1 Módulos

19 módulos construidos con Ant. No hay Maven ni Spring. Los directorios `Servers`,
`dist`, `docs` y `tools` no son módulos: son configuración de Tomcat, salida de build y
documentación.

| Módulo | Tipo | Java | Vistas | Depende de |
|---|---|---:|---:|---|
| `HOSPITAL-BUSINESS` | Núcleo (JAR) | 5.338 | 0 | — |
| `HOSPITAL_2` | WAR principal | 2.841 | 2.915 | núcleo + AFIP, ALFABETA, BIONEXO, VALIDADORES |
| `VALIDADORES` | JAR | 311 | 0 | núcleo |
| `AGH` | WAR portal | 100 | 106 | núcleo |
| `HOS-APP` | WAR portal | 93 | 73 | núcleo + VALIDADORES, AFIP, BIONEXO |
| `SCHEDULER` | WAR sin UI | 90 | 0 | núcleo + VALIDADORES, AFIP, ALFABETA, BIONEXO |
| `AGP` | WAR portal | 88 | 53 | núcleo + VALIDADORES |
| `RECETAS` | WAR portal | 80 | 48 | núcleo + VALIDADORES |
| `PROVEEDORES` | WAR portal | 79 | 41 | núcleo + VALIDADORES, AFIP |
| `ANMAT` | WAR sin UI | 65 | 0 | núcleo |
| `AGI` | WAR portal | 62 | 59 | núcleo + VALIDADORES |
| `WS-HOSPITAL` | WAR REST | 61 | 1 | núcleo + VALIDADORES |
| `AFIP` | JAR integración | 36 | 0 | núcleo |
| `BIONEXO` | JAR integración | 23 | 0 | núcleo |
| `ALFABETA` | JAR integración | 14 | 0 | núcleo |
| `seguridad` | WAR de administración | 154 | 51 | **independiente del núcleo** |
| `ANUNCIADOR` | Node.js + Vue | 0 | — | independiente |
| `RDBMS` | 136 scripts SQL | 0 | — | independiente |
| `REVENG` | Utilidad de desarrollo | 24 | 0 | independiente |

Todo depende de `HOSPITAL-BUSINESS`, que es el núcleo real: 5.338 clases y 1.150
mapeos Hibernate. `ANUNCIADOR` ya está fuera del stack Java (Express + Vue).

`seguridad` es la excepción: su script Ant está anidado en
`seguridad/frontend/java/codigo_aplicacion/`, produce `SEGURIDAD.war` más
`SEGURIDAD-BUSINESS.jar`, no depende de `HOSPITAL-BUSINESS` y tiene sus propios 26
mapeos Hibernate. Es la **consola de administración de la autorización**, y aunque es
pequeña resulta imprescindible: ninguna otra pieza del sistema puede escribir las tablas
de perfiles y menús, y el sistema depende de ellas para autorizar.

#### 3.1.1 La consola de seguridad

Aplicación JSF independiente, versión 3.0.0.3, con el mismo stack que el resto:
JSF 2.2.13, PrimeFaces 6.1.4, Hibernate y `ojdbc6`. Trae dos versiones de Hibernate en
el classpath, 4.1.7 y 4.2.6, lo que conviene resolver antes de tocarla.

Administra la autorización de todo el sistema: aplicaciones y árbol de menús, perfiles
de acceso, asignación de menús a perfiles, asignación de perfiles al personal, roles
funcionales y sucursales de empresa. **No tiene cuenta de servicio**: conecta a Oracle
con las credenciales del propio usuario que inicia sesión, coherente con el modelo de
usuarios nativos descrito en 5.1.

**Está en uso activo, y está medido.** Los cambios de permisos no se detuvieron nunca: la
auditoría registra actividad sostenida desde 2019 hasta junio de 2026, el mes en que se
tomó la copia.

| Año | Cambios de menú a perfil | Altas y bajas de perfil al personal |
|---|---:|---:|
| 2019 | 3.403 | 973 |
| 2020 | 4.863 | 2.969 |
| 2021 | 7.722 | 3.239 |
| 2022 | 5.628 | 3.466 |
| 2023 | 11.324 | 3.334 |
| 2024 | 7.226 | 3.487 |
| 2025 | 5.186 | 2.726 |
| 2026 (hasta junio) | 3.274 | 1.594 |

El último cambio de permisos es del 2026-06-02 y la última asignación de perfil del
2026-06-17. Son del orden de **300 movimientos de permisos por mes**, o sea que la
administración de accesos es una tarea operativa cotidiana, no un acto de instalación.

Y ninguna otra pieza puede hacerla: **ningún otro mapeo Hibernate del monorepo declara
esas tablas** y ningún package PL/SQL escribe `MENU_PERFIL_ACCESO`. Del otro lado, el
sistema sí las consume: `MENU_APLICACION` aparece en 15 archivos del monorepo y 13
fuentes PL/SQL, y la autorización se resuelve llamando a `TS.SEGURIDAD.f_tiene_permiso`.
Conclusión: **la consola es una pieza portante y su reemplazo entra en el alcance**, no se
puede dejar afuera del plan.

**Un tercio de la aplicación es código muerto en esta instalación.** De sus 17 tablas
mapeadas, seis no existen, y se confirmó contra `ALL_OBJECTS` que **no existen en ningún
esquema de la base, ni como tabla, ni como vista, ni como sinónimo**: `ROL_ACCESO`,
`MENU_ROL_ACCESO` y `PERSONAL_ROL_ACCESO` por un lado, y `PRESTADOR`,
`USUARIO_PRESTADOR`, `USUARIO_PREST_PERFIL_ACCESO` y `PROVEEDOR_PERFIL_ACCESO` por el
otro. Son dos subsistemas completos, el de roles de acceso y el de usuarios de
prestadores y proveedores, que existen en el producto de Thinksoft pero no en este
cliente. Diez de las 51 vistas quedan atadas a ellos. No confundir con
`ROL_FUNCIONAL_PERS`, que es otro concepto y sí está en uso con 16.282 filas y 47.199
cambios auditados.

Dato adicional: `APLICACION` tiene una sola fila, así que la capacidad multiaplicación
de la consola tampoco se usa. Y su `hibernate.cfg.xml` conserva ocho hosts Oracle de
distintas instalaciones, que hay que limpiar antes de versionar cualquier derivado.

### 3.2 Arquitectura de capas

```
Vista JSF (BB*, 2.345 beans)
      ↓
Delegator (97 fachadas estáticas)
      ↓
BusinessFactory → ImpBus* (1.123 implementaciones)  ← orquestación y transacciones
      ↓
PersistanceFactory → Imp* (303 DAOs de entidad + 46 de stored procedures)
      ↓
Hibernate StatelessSession
      ↓
Oracle: packages PL/SQL  |  tablas vía Criteria y SQL nativo
```

**No hay capa de dominio.** Los 2.243 DTOs son entidades Hibernate planas sin
lógica; los `ImpBus*` orquestan llamadas y aplican validaciones livianas; la lógica
de negocio pesada vive en los packages Oracle. Esto es importante para el plan: la
migración del backend **no es refactorizar 1.123 clases de negocio, es portar la
lógica de 61 packages** y construir el dominio que hoy no existe.

### 3.3 Duplicación

- **No hay duplicación de fuentes** entre `HOSPITAL-BUSINESS` y los WAR: se comparte
  vía JAR. Solo 1 nombre de clase en común entre el núcleo y `HOSPITAL_2`.
- **Sí hay 78 grupos de archivos Java idénticos copiados entre WAR** (283 copias):
  scaffolding JSF, converters, validators e infraestructura de Quartz y BIRT.
- **328 JAR de terceros duplicados**: cada WAR lleva su propio `WEB-INF/lib` de 112
  a 217 archivos.

### 3.4 Presentación

| Métrica | Valor |
|---|---|
| Vistas `.xhtml` | 3.301 (633.954 líneas), 88% en `HOSPITAL_2` |
| Managed beans | 2.503, de los cuales **2.458 `@ViewScoped`** |
| Beans de sesión | 20 (patrón homogéneo: `BBSessionData`, `BBUserData`) |
| `p:ajax` | **16.864** |
| `p:dataTable` / `p:dialog` | 4.250 / 4.861 |
| `pe:layout` (paneles redimensionables) | 4.596 usos |
| Taglibs propias `ts:` | 861 usos (componentes de dominio clínico) |
| Reportes BIRT `.rptdesign` | **408** |
| Claves de i18n | ~46.503 en 32 archivos, un solo idioma |
| Navegación | Sin reglas declarativas: 1.873 redirects programáticos |

Fragmentación de versiones a unificar: PrimeFaces 6.1.2, 6.1.14, 7.0.6 y 7.0.11
conviven según el WAR; JSF 2.2.12 y 2.2.13; jQuery 1.9.1 y 3.3.1.

Las 20 vistas más grandes superan las 1.400 líneas cada una, encabezadas por
`validarAnalisisLab.xhtml` con 3.818. En el código, los monstruos son
`BBRecepcionPaciente.java` con **12.968 líneas** y el delegator `Configuracion.java`
con 11.442: no son pantallas, son subsistemas dentro de una clase.

### 3.5 Qué se usa realmente, medido

El sistema registra cada entrada a un módulo en `TS.LOGIN_MENU_APLICACION`, con usuario,
sesión y fecha. Son **9,7 millones de accesos de 3.419 usuarios desde febrero de 2021**,
y es el mejor insumo de priorización que tenemos.

Una aclaración necesaria para no sobreinterpretarlo: el log registra **39 puntos de
entrada de módulo**, no las 1.240 entradas de menú ni las 3.301 vistas. Todas las acciones
registradas son páginas `inicio*`. Así que esto ordena *módulos* por uso, y no permite
concluir nada sobre pantallas internas muertas.

El uso está muy concentrado: **los 20 módulos más usados acumulan el 98% de los accesos**
(9.556.589 de 9.742.280). Este es el ranking del último año de producción, junio 2025 a
junio 2026, con los usuarios distintos de cada uno.

| Módulo | Accesos | Usuarios |
|---|---:|---:|
| RECEPCION | 324.316 | 292 |
| HISTORIA_CLINICA | 278.382 | **1.132** |
| INTERNACION | 261.829 | 923 |
| ENFERMERIA_INTERNADOS | 219.118 | 283 |
| DEMANDA_ESPONTANEA | 199.039 | 367 |
| ATENCION_TURNOS | 168.517 | 332 |
| ATENCION_MEDICA | 132.986 | 567 |
| ADMISION_INTERNADOS | 123.083 | 285 |
| CENTRO_PROCEDIMIENTO (cirugía) | 116.389 | 565 |
| DEPOSITO (farmacia) | 98.716 | 357 |
| INFORMES | 51.888 | **1.058** |
| DIAGNOSTICO_POR_IMAGENES | 50.217 | 155 |
| OTROS_ESTUDIOS | 40.172 | 288 |
| ENFERMERIA_AMBULATORIA | 35.650 | 254 |
| FACTURACION_AMBULATORIA | 32.289 | 102 |
| ADMINISTRACION_GENERAL | 30.377 | 525 |
| OFTALMOLOGIA | 25.642 | 51 |
| FACTURACION_INTERNADO | 24.536 | 134 |
| CAJA | 16.823 | 205 |
| COMPRAS | 10.095 | 50 |

Y la cola, que importa tanto como la cabeza: COBRANZA_CONVENIO 7.610 accesos,
ACREDITACION_PROFESIONALES 6.912, **LABORATORIO 4.806 con solo 21 usuarios**, NUTRICION
3.756 con 16, LIQUIDACION_HONORARIO 3.594 con 25, PANEL_DE_CONTROL 2.447, ESTADISTICAS
2.259, AREA_ORGANIZACIONAL 2.183, SEGURIDAD 1.120 con 11 usuarios e INFECTOLOGIA 991.

Tres lecturas para el plan:

- **`HISTORIA_CLINICA` e `INFORMES` son los de mayor alcance**, con 1.132 y 1.058 usuarios
  distintos. Son los que toca todo el hospital, y por eso los de mayor riesgo político en
  un corte.
- **El circuito ambulatorio manda**: recepción, demanda espontánea, turnos y atención
  médica suman más de 800.000 accesos anuales. Es el núcleo funcional real, no facturación
  ni administración.
- **`LABORATORIO` con 21 usuarios contrasta con su complejidad técnica.** Es el módulo con
  las tres integraciones de motores, hardware serie y ASTM, y sin embargo lo usa un
  puñado de personas. Ese desbalance entre esfuerzo de migración y población afectada
  conviene discutirlo con el cliente antes de asignarle prioridad.

Un dato del mismo relevamiento que abre un frente no contemplado: **hay herramientas
externas conectándose directo a la base**. En los últimos seis meses de producción,
`QVODBCConnectorPackage.exe`, que es el conector ODBC de QlikView, acumuló 10.481 logins;
también aparecen Power Query de Excel (`Microsoft.Mashup.Container`), scripts de Python,
DBeaver y PL/SQL Developer. Es una capa de explotación de datos por fuera de la
aplicación, sin documentación, que se rompe entera al cambiar de motor. Hay que
inventariarla antes del corte.

---

## 4. El contrato entre el código y la base

Este es el activo más valioso del relevamiento, porque convierte una migración
difusa en una lista enumerable.

| Mecanismo | Ocurrencias | Dónde |
|---|---:|---|
| `<sql-query callable="true">` a packages | **1.202** | 56 archivos `.hbm.xml` en `persistance/storedprocedures/` |
| `getNamedQuery(...)` que las ejecuta | 1.150 | 66 clases, 1.097 en `Imp*` de stored procedures |
| `createSQLQuery` con SQL Oracle nativo | 415 | 125 archivos, casi todos en `HOSPITAL-BUSINESS` |
| `createCriteria` / `session.get` | 879 | 271 archivos |
| `prepareCall` JDBC directo | 46 | 10 archivos (IDs, seguridad, casos puntuales) |

**Los 1.202 `<sql-query>` son puntos de migración trazables uno a uno.** Cada uno
declara el package, el procedimiento, los parámetros y el DTO de retorno. La
convención real es `TS.<DOMINIO>.f_*` o `p_*`, no `PKG_*`.

**De los 62 packages de negocio de la base, el código Java invoca 53.** Ningún
package invocado desde Java queda fuera del inventario de la base, lo que confirma
por validación cruzada que **no hay lógica de negocio oculta en otro esquema**. Los
9 restantes son los candidatos a código muerto de la sección 2.6.

Las dos únicas excepciones fuera del esquema `TS` son `system.dbms_user_security`
(la administración de usuarios de Oracle, sección 5.1) y `sms.gestion_sms`.

Los más usados concentran el trabajo:

| Invocaciones | Package |
|---:|---|
| 131 | `LABORATORIO` |
| 116 | `FARMACIAS` |
| 94 | `ATENCION` |
| 66 | `FACTURACION` |
| 50 | `TURNOS` |
| 48 | `LIQUIDACION_HONORARIO` |
| 48 | `ADM_CIRUGIA` |
| 46 | `CAJAS` |
| 45 | `COMPRAS` |
| 44 | `INTERFACES` |

Hay una **segunda capa menos visible**: SQL Oracle armado como texto en el Java, que
mezcla dialecto nativo con llamadas embebidas a funciones PL/SQL (por ejemplo
`ts.turnos.f_is_excluido_convenio` dentro de un `WHERE`). No está declarada en ningún
XML, así que no aparece ni en el relevamiento de la base ni en el de mapeos. Ya está
inventariada, con el detalle completo en `tools/relevamiento/out/SQL-NATIVO.md`.

### La segunda capa: SQL nativo embebido en el Java

Son **451 puntos en 125 archivos**, y el primer dato relevante es dónde están: **445 en
`HOSPITAL-BUSINESS`**, es decir concentrados en la capa de persistencia y no dispersos
por la interfaz. Eso los vuelve abordables como un frente propio.

| Vía de ejecución | Puntos |
|---|---:|
| `createSQLQuery` de Hibernate | 415 |
| `prepareCall` JDBC | 24 |
| `prepareStatement` JDBC | 12 |

El 95% del texto de esas sentencias se pudo reconstruir estáticamente. Clasificadas por
lo que cuesta llevarlas a PostgreSQL:

| Costo | Qué implica | Puntos |
|---|---|---:|
| Portable | Corre sin cambios | 146 |
| Mecánico | Reemplazo de función por función | 29 |
| Reescritura | Hay que rehacer la consulta | 136 |
| Rediseño | Sin equivalente directo | 116 |
| Sin resolver | Se arma en tiempo de ejecución | 24 |

**Pero el conteo de puntos exagera el trabajo.** Las 427 consultas reconstruidas se
reducen a **348 plantillas distintas**, y 110 puntos son copias de un molde ya resuelto.
El caso más claro es el visor de pistas de auditoría: **57 puntos** que son la misma
consulta contra tablas `AUD_*`, todos marcados como complejos por un único motivo, que
invocan `ts.facturacion.f_get_descripcion_auditoria`. Resolver esa plantilla una vez
cierra el 49% de los puntos de rediseño.

Descontado eso, los puntos que exigen decisión propia son **59**, agrupados en 25 clases
`Imp*`. Y lo que hay que resolver en ellos no son 115 llamadas sino **37 rutinas PL/SQL
distintas** en 13 packages. Las que más pesan:

| Rutina | Puntos que la llaman |
|---|---:|
| `ts.facturacion.f_get_descripcion_auditoria` | 57 |
| `ts.personas.f_get_personal_matricula` | 15 |
| `ts.personas.f_get_tipo_paciente` | 10 |
| `ts.farmacias.f_get_descripcion_real_det_ped` | 9 |
| `ts.farmacias.f_is_item_trazable` | 7 |

Cuatro de esas rutinas son de `GENERAL` y ya están cubiertas por el diseño de
`docs/migracion-package-general.md`, incluidas las tres de generación de identificadores
que aparecen en `HibernateSessionFactory`.

En cuanto al dialecto, las construcciones no portables se concentran en dos: `DECODE` en
181 puntos y `ROWNUM` en 175. Ambas tienen traducción conocida, `CASE` y `LIMIT`
respectivamente, y buena parte cae dentro de la plantilla de auditoría, así que se
resuelven de forma mecanizable. El resto es menor: `NVL` en 44, `SYSDATE` en 42,
`TRUNC` en 32, `FROM DUAL` en 16 y el join `(+)` en apenas 9.

Lo genuinamente irreducible son **24 puntos** que construyen la sentencia en tiempo de
ejecución. De ellos, 16 están en los envoltorios `Conexion*.java`, que no contienen SQL
propio sino que ejecutan el que reciben, de modo que el trabajo real ronda los 8 casos.
Se verificó que esos envoltorios se usan en muy pocos archivos y que solo 9 clases de
negocio tienen literales SQL fuera de las vías ya relevadas, así que **el inventario
puede darse por completo**.

Aparece además una deuda de seguridad concreta: **8 puntos concatenan un valor recibido
desde Java directamente contra un comparador o una lista**, que es el patrón clásico de
inyección SQL. Cuatro están en `ImpCodPrestGrpCodPrest`. Otros 151 interpolan texto en
posiciones menos expuestas, como nombres de columna u ordenamientos, que igual conviene
parametrizar al reescribir.

### SQL Oracle no portable en el código Java

| Construcción | Ocurrencias | Archivos |
|---|---:|---:|
| `SYSDATE` | 567 | 203 |
| `DECODE(` | 452 | 109 |
| `ROWNUM` | 283 | 64 |
| `TRUNC(` | 174 | 57 |
| `NVL(` | 111 | 29 |
| Join antiguo `(+)` | 20 | 11 |
| `FROM DUAL` | 19 | 5 |

No hay `CONNECT BY`, `START WITH` ni `MINUS`, que son los casos verdaderamente
costosos. Todo lo listado tiene equivalente directo en PostgreSQL.

---

## 5. Los siete condicionantes de la arquitectura destino

Estos puntos no son deuda técnica a resolver después: definen decisiones de
arquitectura (y de producto) que hay que tomar antes de escribir la primera línea.

### 5.1 La autenticación del personal usa usuarios nativos de Oracle

> El diseño del modelo destino, con el diagnóstico completo y los defectos a no replicar,
> está en [`docs/migracion-identidad.md`](migracion-identidad.md). Resumen: **sin
> OIdentity**; JWT local del starter para personal y portales, API Key para las 57 cuentas
> de servicio, y tres extensiones obligatorias al starter (actor en auditoría, enforce de
> `Permission`, contexto de sesión en la base). El personal restablece contraseña en el
> corte porque las de Oracle no son recuperables; los portales migran con verificación
> transparente del hash legacy.

El sistema no tiene tabla de usuarios con contraseñas: **cada usuario de la
aplicación es un usuario de la base Oracle**. La administración se hace con un
package propio, `system.dbms_user_security`, mediante `p_add_user`, `p_drop_user`,
`p_lock_user`, `p_unlock_user`, `p_change_user_password`, `p_is_user_locked` y
`f_get_user_password`. Existe además `f_get_paciente_password`: los pacientes de los
portales también son usuarios de Oracle. Cada sesión web guarda su propia
`Conexion` con las credenciales del usuario, y `AUD_USERS_LOGON_USERS` acumula
88 millones de registros de login.

Pero **conviven dos modelos de identidad, y no cubren lo que uno supondría**. Las cifras
que siguen están medidas contra la base, no inferidas del código (ver
`out/VERIFICACION.md`).

El modelo de usuario nativo de Oracle rige para el **personal interno**. De 6.387
legajos, 6.128 tienen `LOGIN_NAME`, pero **solo 3.970 existen todavía como usuarios de
la base**: los 2.158 restantes son bajas cuyo usuario Oracle ya fue eliminado. En total
la base tiene 4.121 usuarios, así que apenas 151 son cuentas de sistema. Se ve
funcionando en la caché de sentencias: cada persona parsea su SQL bajo su propio esquema
(`FDENARI`, `VALMADA`, `JENFERMERIA`, `MCAPACITADOR` y decenas más).

El segundo modelo es el del servicio `ANUNCIADOR/api_seguridad_nodejs`, que autentica con
JWT propio (HS256, 12 horas) contra contraseñas guardadas en columnas `CONTRASENA_WEB`,
identificando a la persona por su mail en `TS.MAIL_PERSONA` y habilitando la cuenta con
`CUENTA_WEB_VALIDADA`. Acá la sorpresa es cuán poco se usa fuera del portal de pacientes.

| Población | Registros | Con contraseña | Cuenta validada |
|---|---:|---:|---:|
| Personal interno (usuarios Oracle) | 6.387 | 3.970 vigentes | — |
| Pacientes | 860.298 | 146.197 | **128.340** |
| Profesionales matriculados | 11.591 | 115 | **87** |
| Proveedores | 1.348 | 10 | — |
| Farmacias externas | 1 | 0 | — |

Dos lecturas importantes. Los pacientes con cuenta web son **128.340, no 860.000**: el
resto son pacientes sin portal, que no son usuarios. Y el portal de profesionales tiene
**87 cuentas activas** sobre 11.591 matriculados, o sea que está prácticamente sin uso;
lo mismo el de proveedores con 10, y el de farmacias externas con ninguna. Eso cambia la
prioridad de esos portales en el plan.

Se verificó además el algoritmo: los 146.312 hashes almacenados miden **exactamente 64
caracteres, sin excepción**, lo que confirma SHA-256 en hexadecimal, sin sal y sin
versionado de esquema.

Un matiz que el código no dejaba ver: los portales web **no conectan con el usuario
final** sino con cuentas de servicio dedicadas (`PACIENTE_TS`, `PRESCRIPTOR_TS`,
`TRIAGE_TS`, `AUTORECEPCION_TS`, `ANUNCIADOR_TS`, `INTERFACE`, `HIBERNATE`,
`SCHEDULER`). El modelo de conexión por usuario aplica solo a las aplicaciones JSF
internas. Es decir que el patrón de pool compartido ya existe en el sistema y hay de
dónde copiarlo.

Consecuencias:

- El modelo de identidad del personal **se reconstruye de cero** contra JWT local del
  starter (sin IdP externo). No es una adaptación.
- **El restablecimiento obligatorio de contraseña alcanza a los activos con cuenta
  vigente (~1.376)**, no a los 3.970 usuarios Oracle abiertos ni a las bajas. Los
  realmente conectados por mes son ~1.650; la diferencia hay que aclararla con el
  cliente. Para portales, el hash SHA-256 legacy se verifica y se refuerza a BCrypt en
  el primer login. Nadie fuera del personal interno nota el cambio.
- El modelo de conexión por usuario se reemplaza por un pool compartido más
  autorización a nivel de aplicación, siguiendo el patrón que ya usan los portales.

**La autorización sí es portable**: vive en tablas (`PERFIL_ACCESO` con 169 perfiles,
`MENU_APLICACION` con 1.236 ítems, `MENU_PERFIL_ACCESO` con 15.666 asignaciones,
`PERSONAL_PERFIL_ACCESO` con 6.940, `ROL_FUNCIONAL_PERS` con 16.282) y en el package
`TS.SEGURIDAD`, que con 286 líneas es hoja del grafo de dependencias y se puede portar
aislado.

Dos defectos de diseño a corregir al reimplementar, no a copiar. El primero: la
autorización **falla abierta**. `f_tiene_permiso` solo verifica el perfil si la acción
está registrada en `MENU_APLICACION`; si no lo está, concede el acceso. Además compara
con `LIKE` sobre un patrón que llega por parámetro. El segundo está en 5.1.1.

#### 5.1.1 La suplantación de usuarios reescribe credenciales en la base

`TS.SEGURIDAD` expone un par de funciones que implementan "entrar como otro usuario" de
la forma más invasiva posible. `f_bypass_user_login` lee el hash crudo de la contraseña
desde `SYS.USER$`, ejecuta `ALTER USER ... IDENTIFIED BY` con una contraseña temporal y
devuelve el hash original al llamador; después `f_reestablecer_user_pass` lo repone con
`ALTER USER ... IDENTIFIED BY VALUES`. Es decir que para suplantar a alguien **se le
cambia la contraseña real y se confía en poder restaurarla**.

Las dos armas el DDL concatenando los parámetros recibidos, sin ligarlos, de modo que
quien pueda invocarlas puede inyectar DDL arbitrario. Requieren además privilegios de
lectura sobre `SYS.USER$` y de `ALTER USER`. Y mientras la contraseña está reescrita, el
usuario legítimo no puede entrar y cualquiera que conozca la temporal puede hacerse
pasar por él. En el destino esto se reemplaza por suplantación con token acotado
(`act` + `sub`) y auditada, sin tocar credenciales.

### 5.2 Hay tres motores de base de datos, no uno

| Motor | Rol | Dónde se configura |
|---|---|---|
| **Oracle 11.2** | Base principal, esquema `TS` | `hibernate.cfg.xml` |
| **MySQL** (puerto 1457) | Laboratorio externo | **Dentro de la propia base Oracle**, tabla de `ParamLaboratorio` |
| **SQL Server** (1433) | Laboratorio: `192.168.150.30`, `192.168.190.6` | `interfacesSQLServer.cfg.xml` |

Además el esquema `SMS` (package `gestion_sms`) para mensajería, y `INTERFACE` como
esquema separado.

El caso de MySQL merece atención: sus credenciales **se leen de una tabla de Oracle
en un inicializador estático** y quedan en un campo `public static String PASS`. Al
migrar esa tabla a PostgreSQL se migran también esas credenciales, y conviene
decidir en el camino si pasan a un gestor de secretos.

### 5.3 Las interfaces de laboratorio necesitan hardware

`SCHEDULER` expone `SerialPortServlet` y `SerialPortResultServer`, y
`HOSPITAL-BUSINESS` implementa `SerialPortRXTXProtocol`: comunicación **por puerto
serie con instrumental de laboratorio** mediante RXTX, que usa librerías nativas.

Un contenedor Quarkus estándar no accede a un puerto serie. Estas interfaces
requieren un **componente de borde** desplegado en una máquina con acceso físico al
dispositivo, comunicándose con el resto por API o mensajería. No pueden vivir en el
mismo despliegue que los servicios de negocio.

En la misma familia, pero sin hardware: **HL7** (HAPI, 10 archivos, mensajería con
DNLab y Kern) y **DICOM** (dcm4che 5.11 con `GetSCU` y `FindSCU` contra PACS de
imágenes). Ambos son subsistemas especializados que sobreviven bien en Java 21.

### 5.4 La generación de identificadores está acoplada a PL/SQL

`CustomIdGenerator` y `HibernateSessionFactory` llaman a `ts.general.f_next_id_tabla`
(y variantes compuestas) **en cada insert de Hibernate**. La secuencia
`SEC_ID_DET_ING_UNIDAD_NEGOCIO` es el objeto más ejecutado de toda la base, con
1,19 millones de ejecuciones en la caché.

Hay que reemplazar este mecanismo por secuencias de PostgreSQL o identidad
generada antes de migrar cualquier entidad, y verificar que la asignación de
identificadores siga siendo correcta bajo concurrencia.

El diseño detallado está en `docs/migracion-package-general.md`. Lo relevante para el
plan general: de los 112 objetos generadores, **100 ya son secuencias de Oracle** y se
mapean 1 a 1, pero 11 son tablas contador con `SELECT ... FOR UPDATE` que serializan
cada inserción, y al menos un identificador (`ID_INTERNACION`) **tiene semántica
incrustada** en lugar de ser un subrogado opaco.

### 5.5 El PL/SQL de negocio es un solo bloque mutuamente recursivo

**29 de los 61 packages forman un ciclo de dependencias con 258.089 líneas, el 84%
del PL/SQL de negocio.** Están todos los pesados: `LABORATORIO`, `FARMACIAS`,
`FACTURACION`, `ATENCION`, `TURNOS`, `CAJAS`, `PERSONAS`, `COMPRAS`, `GENERAL`.

No es un artefacto del diccionario de datos. Verifiqué cada arista contra el texto
de los cuerpos exigiendo un sitio de llamada real (`destino.rutina(`), y el ciclo
sobrevive: hay **149 sitios de llamada confirmados** y **22 pares que se llaman
mutuamente**, entre ellos `CAJAS` y `FACTURACION` con 65 y 21 llamadas
respectivamente.

La consecuencia es concreta y contradice la intuición de migrar dominio por dominio:
**no existe un orden en el que portar un package sin que le falte otro.** Eso deja
dos caminos:

1. **Por caso de uso vertical**, tomando como frontera los 1.202 puntos de
   invocación declarados en los `.hbm.xml` en lugar de los límites de los packages.
   Durante la transición ambos mundos comparten tablas.
2. **Convivencia explícita**, donde el código nuevo sigue llamando al PL/SQL que
   todavía no se portó, reduciendo esa superficie de a poco.

La primera es la que **habilita apagar el PL/SQL** (y con ello el 11.2 operativo en
Fase 6). La segunda (puente JDBC a packages) puede usarse **acotada** mientras se
porta, pero no como arquitectura estable: obliga a mantener Oracle hasta que el
ciclo esté cubierto por golden master + implementación Quarkus/Postgres.

Las **6 hojas del grafo** son los únicos packages que el Java invoca y que no
llaman a ningún otro, así que son los únicos portables en aislamiento. La
coincidencia afortunada es que **`SEGURIDAD` es una de ellas** (286 líneas, 10
rutinas, 5 tablas): siendo la identidad el condicionante prioritario, su lógica de
autorización se puede portar sin arrastrar dependencias.

### 5.6 Los reportes son 408 diseños BIRT: se actualiza el motor, no se reemplaza

No hay un solo archivo de JasperReports. El motor es BIRT, con 408 `.rptdesign` (319
en `HOSPITAL_2`). La exportación a Excel es server-side vía Apache POI, referenciada
desde unas 648 clases.

**Conviven dos versiones del runtime**, ambas fuera de soporte: `4.2.1` de 2012 en
`AGH`, `AGI` y `AGP`, y `4.5.0` de 2015 en el resto. **En el destino no se
conservan:** se unifica a **Eclipse BIRT 4.24.0** (junio 2026) sobre JDK 21.

Plan de versión, ClassLoader e infra (sidecar vs máquina dedicada):
[`docs/birt-runtime-destino.md`](birt-runtime-destino.md).

#### BIRT no está discontinuado

Es la confusión habitual, y conviene despejarla porque cambia la decisión. Lo viejo
es la versión instalada, no el proyecto:

| | Instalado (legacy) | Destino |
|---|---|---|
| Versión | 4.2.1 (2012) y 4.5.0 (2015) | **4.24.0, junio de 2026** |
| Java | 6/7/8 | **JDK 21 (LTS), JVM** |
| Uso en Hospital | Report Engine in-process (`BirtEngine`) | Mismo modelo API, **proceso aparte** |
| Viewer (servlets) | No es el camino de este código | No es el producto destino |
| Namespace (viewer oficial) | `javax.*` | `jakarta.*` en viewer desde feb-2026 ([PR #2360](https://github.com/eclipse-birt/birt/pull/2360)); el **engine** puede seguir mixto |

#### Compatibilidad con Quarkus — aislamiento obligatorio

El bloqueo OSGi que se suele citar **no aplica** aquí: `BirtEngine` hace
`new ReportEngine(config)` (JARs en classpath, sin contenedor OSGi).

El bloqueo real con **Quarkus 3** es otro: meter el engine en el mismo ClassLoader
que Jakarta EE 10 / Hibernate 6 / Vert.x produce colisiones (`javax.*` residuales y
deps pesadas). **No** se declara BIRT en el `pom` de Hospital.

Además **BIRT no compila a imagen nativa GraalVM** (reflexión + motor JavaScript de
expresiones). El destino es un servicio JVM `hospital-reports` (sidecar o nodo
según carga), no un módulo dentro del jar del API clínico.

#### Por qué no se reemplaza por otro motor

Dos mediciones lo desaconsejan:

- **407 de los 408 reportes contienen expresiones o métodos en JavaScript.** Cambiar
  de motor obliga a reescribirlas todas, además de rehacer las maquetas.
- **283 de los 408 (69%) traen SQL específico de Oracle** en sus conjuntos de datos:
  `DECODE` en 235, `ROWNUM` en 146, `NVL` en 114, `TO_CHAR` en 92, `TRUNC` en 30,
  join antiguo `(+)` en 22 y `FROM DUAL` en 15.

Esa segunda cifra es la clave de planificación: **migrar el SQL de los reportes a
PostgreSQL hay que hacerlo con cualquier motor**, y es probablemente el costo
dominante de todo el frente de reportes. Elegir otro motor no lo evita, solo le suma
la reescritura de 407 reportes encima.

#### Riesgos concretos del upgrade 4.5 → 4.24

1. **Tres clases internas de BIRT están parcheadas** por sombreado de classpath, sin
   un solo comentario que documente el cambio: `ResourceLocatorWrapper`,
   `PageDeviceRender` (renderizado PDF) y `DataSourceQuery` (ejecución de SQL en el
   motor de datos). Son copias del fuente original con modificaciones enterradas.
   Están replicadas en cinco WAR, pero son idénticas entre sí: son 3 parches, no 15.
   Cada uno hay que diferenciarlo contra el fuente de 4.5 para saber si el problema
   que resolvían ya está corregido upstream.
2. **`com.onbarcode.barcode.birt` 2.2.1**, un plugin comercial de códigos de barra,
   cuya compatibilidad con 4.24 y cuyo licenciamiento hay que verificar.
3. La unificación de las dos versiones que hoy conviven.

#### Conclusión

Reescribir 408 reportes como componentes Angular no es viable. Se **mantiene la
generación server-side**, el frontend solo dispara descarga o impresión, el motor
**se actualiza a 4.24** (no se queda en 4.2.1/4.5) y corre en un **proceso JVM
aparte** (`hospital-reports`), no embebido en Quarkus Hospital.

### 5.7 Paridad UX, un core de negocio, Identity aparte (F2)

**Decidido 2026-08-13.** Mapa:
[`docs/mapa-productos-destino.md`](mapa-productos-destino.md). Análisis Identity:
[`docs/analisis-identity-separado.md`](analisis-identity-separado.md).

Tres reglas:

1. **UX del personal clínico:** un solo shell Angular (un menú, una sesión).
   Look ≈ PrimeFaces: SDD [`docs/sdd/ux-shell-primefaces/`](../cortes/plataforma/ux-shell-primefaces/)
   (draft; no es paridad pixel de cada JSF).
2. **Negocio:** **una** solución Quarkus Hospital (`core` / `application` features /
   `infrastructure` / `presentation-api`). AGI, anunciador, núcleo, AFIP… como features,
   no como N backends. Fronts satélite (tótem, etc.) contra ese API.
3. **Identity (F2):** repo/deploy **`Hospital-Identity`** separado — login, refresh, users,
   API Keys, permisos en token. Aísla carga de auth y sirve de emisor reutilizable para
   otras apps. El Hospital **valida** JWT en local; no llama a Identity en cada request.

| Pieza | Dónde vive |
|-------|------------|
| Login / refresh / admin users | `Hospital-Identity` |
| Lógica clínica, AGI, anunciador | Solución `Hospital` |
| Shell / tótem / anunciador UI | Angular(s); auth→Identity; API→Hospital |

---

## 6. Estrategia recomendada

### 6.1 Principio: lógica a Quarkus; oráculo en 11.2; Postgres para lo nuevo

**Decidido 2026-08-13** (reemplaza el enunciado anterior “Quarkus manteniendo el mismo
Oracle hasta el final”).

1. **Extraer la lógica de negocio (PL/SQL / reglas) directo a Quarkus** — dominio Java
   en la solución Hospital (e Identity donde corresponda). **No** pasar por un desvío
   “PL/SQL → Java legacy JSF → Quarkus” (doble reescritura sobre un stack que se
   abandona).
2. **Oracle 11.2** permanece como runtime del **legacy** y como **oráculo de verdad**
   para caracterizar comportamiento (golden master). No es el datasource Hibernate del
   stack Quarkus 3 (no soportado de forma oficial; dialectos “legacy” son frágiles).
3. **PostgreSQL** es el runtime de **todo código nuevo** (Identity, piloto, módulos
   estrangulados) **desde el día uno**, no solo en un “big bang” final.
4. **Fase 6** sigue siendo el **corte grande de datos/esquema** del mundo legacy hacia
   Postgres (charset, `NUMBER`, PK faltantes, constraints, consumidores ODBC, apagado
   del 11.2 operativo). No contradice que los módulos nuevos ya vivan en Postgres.
5. **DDL de dominio (2026-08-20):** el schema canónico es el **PostgreSQL migrado
   `ts`** (paridad de nombres/columnas con Oracle). Ver §6.5.

Aislar variables se logra así: la paridad funcional se demuestra contra **capturas del
11.2**, no exigiendo que Quarkus hable el dialecto Hibernate de esa versión.

**Descartado explícitamente**

| Opción | Por qué no |
|--------|------------|
| Quarkus + Hibernate contra 11.2 | Fuera de soporte (Quarkus/Hibernate ≥ ~Oracle 19) |
| Upgrade 11.2 → 19 como puente | Segunda migración de motor; luego igual a Postgres |
| PL/SQL → Java legacy → Quarkus | Doble extracción; invierte en el monolito que se retira |

Revisión de alternativas y criterios para reabrir Oracle 19:
[`analisis-oracle19-vs-postgres.md`](analisis-oracle19-vs-postgres.md).

### 6.2 El instrumento de validación: golden master

**Evidencia acumulada (VPN 2026-08-14):**  
[`hallazgos-golden-master-vpn-2026-08-14.md`](hallazgos-golden-master-vpn-2026-08-14.md) —
inventario `GENERAL` (85), top BIRT real (`PERSONAS.f_get_persona_full`), fantasmas
ORA-00904, golden masters edad/interleaved PASS, inventario `next-id`.

La base de preproducción (11.2) permite construir la red de seguridad **antes** de
dar por buena una reimplementación:

1. Para cada punto de invocación relevante: fijar entradas (parámetros + estado de
   tablas necesario).
2. Ejecutar el PL/SQL / flujo legacy en 11.2 y **persistir** salidas (resultado,
   filas afectadas, errores).
3. Cargar datos equivalentes en **PostgreSQL** y ejecutar la implementación Quarkus.
4. **Comparar** salidas (con tolerancias explícitas: fechas, orden, redondeo).

Sin esto, la migración de 347.759 líneas de PL/SQL a un dominio Java es una apuesta.
Con esto, es un proceso verificable **sin** runtime Quarkus sobre 11.2.

El inventario de los **1.202 puntos de invocación** (más SQL nativo / pantallas del
caso de uso) es la checklist de cobertura: “migrado” implica golden master verde o
WAIVER documentado.

**Regla de WAIVE / paridad de pantalla (2026-08-14, estricta):** no marcar un RF
como WAIVE “porque el slice es chico” si el **legacy ya tiene** esa función.
O se implementa, o se **diffiere** con slug SDD propio (deuda de paridad). Evidencia
legacy obligatoria. Canónico: [`docs/sdd/regla-waiver-paridad-legacy.md`](../canon/regla-waiver-paridad-legacy.md).

### 6.3 Dos runtimes en el *build*; corte al final (no convivencia de tráfico)

**Aclaración 2026-08-13 (producto):** no está planeado que usuarios usen a la vez el
HIS viejo y el nuevo en producción (strangler). El plan preferido es:

1. Construir el sistema nuevo **completo** sobre **PostgreSQL**.
2. Validar con **golden master** capturado en Oracle **11.2** (prod o, preferible, **copia**).
3. **UAT en paralelo aislado** — el nuevo se prueba **sin unirlo** al legacy.
4. **Un corte** cuando el alcance pactado esté listo.

“Dual-runtime” aquí significa solo: **dos entornos de datos durante el desarrollo**
(11.2 = referencia/oráculo; Postgres = destino). No es sync en caliente ni tráfico
compartido.

```
Legacy (JSF + PL/SQL)  ──► Oracle 11.2     (prod intacta o copia)
                                │
                                │ captura golden master (offline / copia)
                                ▼
Quarkus + Angular      ──► PostgreSQL      (sistema nuevo; UAT aislado → corte)
```

- **No** comparten la misma instancia como datasource primario del código nuevo.
- **No** se apunta Hibernate Quarkus 3 al 11.2.
- Datos para UAT/corte: carga / ETL / migración controlada hacia Postgres (Fase 6 =
  preparación y ejecución del corte de datos, no “primer contacto” si el nuevo ya
  nació en Postgres).
- **Variante opcional (strangler):** módulos nuevos tomando tráfico de a uno en prod.
  Queda documentada por si el hospital cambia de criterio; **no es el default**.
- Puente excepcional JDBC a un package 11.2 aún no portado: solo si un spike lo
  exige; no es arquitectura estable ni parte del UAT paralelo aislado.

### 6.4 Piloto

`AGI` (Tótems) junto con `ANUNCIADOR` es el piloto adecuado: son funcionalmente
solidarios, `AGI` es chico y acotado (62 clases Java, 59 vistas, depende solo del
núcleo y de `VALIDADORES`), y `ANUNCIADOR` ya está fuera del stack JSF. Sirve para
calibrar velocidad real de migración sin poner en riesgo el core clínico.

El piloto **demostró** el stack (gate-done 2026-08-17/18). **No ensanchar** seeds
`*_agi` ni tablas `public` simplificadas: el DDL canónico es §6.5.

### 6.5 DDL canónico = PostgreSQL migrado (`ts`) — 2026-08-20/21

**Decidido 2026-08-20** (prioridad de programa). Complementa §6.1: no basta “correr
en Postgres”; hay que usar la **estructura migrada desde Oracle**.

| Regla | Detalle |
|-------|---------|
| Nombres de tablas/columnas | Iguales a Oracle `TS` (fold a minúsculas en PG OK) |
| Cantidad de columnas | Igual; **no** agregar/quitar columnas de dominio |
| Tipos | Único cambio admitido — [`catalogo-tipos-oracle-pg.md`](../relevamiento/catalogo-tipos-oracle-pg.md) |
| Fuente de verdad | BD PG migrada (schema `ts`), no Flyway “inventado” en `public` |
| Gap solo-Oracle | Registrar en [`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md); no sustituir en silencio |

**Piloto / puente:** tablas como `public.llamado_paciente` y seeds `*_agi` fueron
útiles para medir el stack. **No son el diseño final.** D-ANU-01 (diferir mirror
1:1 de `LLAMADO_ANUNCIADOR`) queda **SUPERSEDED** para schema: `ts.llamado_anunciador`
ya existe en el PG migrado con paridad de columnas.

Documentación operativa:

| Doc | Rol |
|-----|-----|
| [`regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md) | Regla canónica |
| [`inventario-ddl-oracle-pg/`](../relevamiento/inventario-ddl-oracle-pg/) | Diff medido 2279 tablas / 35393 columnas |
| [`plan-cutover-api-schema-ts.md`](../planificacion/plan-cutover-api-schema-ts.md) | Cutover Hospital-Api → `ts` |
| [`retiro-tablas-piloto-public.md`](retiro-tablas-piloto-public.md) | **Plan de retiro** `public.*` / `*_agi` (nivel B) |

---

## 7. Plan por fases

Las fases 0 a 2 son requisito de todo lo demás y conviene no solaparlas.

### Fase 0 — Cimientos (en curso)

- [x] Versionado del legacy en Git con LFS
- [x] Relevamiento completo del esquema Oracle
- [ ] Rotación de credenciales expuestas en el repositorio
- [ ] Re-tomar `v$sql` / AWR **sobre producción** (código vivo): la copia diaria es
      fiable en datos/fuentes, pero su caché SQL no es el workload de prod
- [ ] Inventario funcional con usuarios: qué módulos se usan y cuáles no

### Fase 1 — Decisiones de arquitectura

Cerrar los condicionantes de la sección 5 antes de escribir código de negocio del núcleo.
**Ya cerrados:** identidad (5.1 + contrato/SDD oleada A); paridad UX / mapa de productos
(5.7); **runtime BD / Quarkus** (§6.1–6.3, 2026-08-13): Postgres para lo nuevo, 11.2
como oráculo + legacy, **UAT paralelo aislado → un corte** (strangler opcional), sin
puente 11→19 ni desvío por Java legacy; **DDL canónico `ts`** (§6.5, 2026-08-20):
paridad nombres/columnas con Oracle (piloto `public` = puente a retirar).

Siguen abiertos en esta fase: tratamiento de los tres motores, componente de borde para
puerto serie, generación de identificadores (`GENERAL`), unidad de corte del PL/SQL frente
al ciclo de 29 packages, y estrategia fina de reportes BIRT (el upgrade del motor ya está
decidido).

### Fase 2 — Red de seguridad

- Golden master de los 1.202 puntos de invocación PL/SQL (**captura en Oracle 11.2**,
  replay/comparación contra implementaciones Quarkus en **PostgreSQL**)
- Entornos reproducibles: clon/lectura 11.2 para capturas; Postgres para Identity y
  módulos nuevos (Identity ya nace en Postgres)
- Mecanismo transversal de auditoría en el starter de Quarkus, que debe cubrir de
  una sola vez lo que hoy hacen 1.689 triggers
- Harness mínimo documentado (formato de caso, diff de salidas, criterios PASS/WAIVE)

### Fase 3 — Piloto AGI + ANUNCIADOR

Objetivo real: **medir**. De acá salen las cifras de esfuerzo para el resto, que hoy
solo se pueden estimar por analogía.

Cimiento de identidad del piloto: oleada A del contrato
([`docs/sdd/identidad-oleada-a/`](../cortes/plataforma/identidad-oleada-a/) — `sdd.hospital.identidad-oleada-a`).
**Estado 2026-08-13: oleada A hecha** en repo
[`Hospital-Identity`](https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-Identity)
(rama `dev/dev`): JWT local + API Key + `/auth/me`, sin OIdentity. Compuerta local:
`tools/verify-oleada-a.sh`. Front piloto Angular: `Hospital-Web` (local). Negocio
piloto: **`Hospital-Api`** (local, puerto 8081) — resource server que valida JWT de
Identity; SDD [`docs/sdd/piloto-agi-anunciador/`](../cortes/anunciador/piloto-agi-anunciador/). Contrato:
[`docs/contrato-api-identidad.md`](contrato-api-identidad.md) §9. Sin ese cimiento,
AGI y ANUNCIADOR no comparten emisor de tokens. El piloto corre contra
**PostgreSQL** + Identity; la paridad con reglas aún en PL/SQL se demuestra con
golden master desde 11.2 (§6.2).
**Estado 2026-08-13:** CU #1 (anunciador de llamados A1+A2) **gate-done** —
[verify-report PASS](../cortes/anunciador/piloto-agi-anunciador/verify-report.md). Vertical **G1 recepción**
slice v1 también **gate-done** —
[verify-report PASS](../cortes/recepcion/piloto-agi-g1/verify-report.md) (`/agi/recepcion`, smoke
`smoke-piloto-agi-g1.sh`). Display sala API Key: hecho.
Relevamiento Node→Api (2026-08-18) + **DDL canónico `ts` (2026-08-20/21)**:
[`sdd/relevamiento-node-anunciador/`](../relevamiento/relevamiento-node-anunciador/) ·
[`sdd/regla-ddl-postgres-migrado.md`](../canon/regla-ddl-postgres-migrado.md).
`llamado_paciente` = **puente temporal**; canónico = `ts.llamado_anunciador`
(D-ANU-01 schema **SUPERSEDED**). Retiro de shapes piloto:
[`sdd/retiro-tablas-piloto-public.md`](retiro-tablas-piloto-public.md).
Cutover Api (plan): [`sdd/plan-cutover-api-schema-ts.md`](../planificacion/plan-cutover-api-schema-ts.md).

**Estado 2026-08-14:** CU #1 Anunciador **gate-done**. Vertical G1
recepción **gate-done** (G1 → G1-b → G1-c → G1-d → G1-d.birt). **R3.1** logo +
barcode recepción **ingeniería done** (UAT impresora on-site pendiente).

**Estado 2026-08-18 (snapshot):** CU-A/B/B.1/C **gate-done** (medidor). Paridad
Cola **M1–M4 gate-done**. **Apagar Node** anunciador **gate-done en DEV**
([`apagar-anunciador-node/`](../cortes/anunciador/apagar-anunciador-node/)). Ciclo vida `LLAMAR`
**parcial** ([`ciclo-vida-llamado-anunciador/`](../cortes/anunciador/ciclo-vida-llamado-anunciador/) —
C1 consume-on-read + ventana + safety 1h). Anuncio en TV **solo vía Llamar**
(no al confirmar recepción). Mapa UI:
[`mapa-menu-hospital-web.md`](../relevamiento/mapa-menu-hospital-web.md).

**Estado 2026-08-19 (shell menú):** Hospital-Web con **grilla de módulos** en
`/dashboard` (tiles ~112×112, iconos PNG legacy), **sidebar por módulos
expandibles**, hojas sin Angular **disabled**, filtro por perfil con bypass DEV
`menuShowAll` ([`arbol-mapeo-menu-legacy-web.md`](../relevamiento/arbol-mapeo-menu-legacy-web.md)
M1 + M2 parcial). Identity `GET /menus` sigue pendiente.

Orden vigente: [`sdd/backlog-orden-2026-08-14.md`](../planificacion/backlog-orden-2026-08-14.md) ·
estado: [`sdd/estado-piloto-vs-general.md`](../estado/estado-piloto-vs-general.md).
**Siguiente:** cutover persistencia anunciador/colas → `ts` (plan) · cerrar ciclo
vida (C2.1/C3) o menú Cola B; IDs/`GENERAL` y BIRT extras solo bajo demanda de
CU/corte. Trabajo en paralelo:
[`sdd/trabajo-paralelo-equipo.md`](../planificacion/trabajo-paralelo-equipo.md).

### Fase 4 — Núcleo por casos de uso

**No por dominio ni por package.** El ciclo de 29 packages descrito en 5.5 impide
cortar por límites de package, así que la unidad de trabajo es el caso de uso
vertical: una pantalla o proceso completo, con los puntos de invocación que usa, sus
tablas y sus reportes.

Guía operativa (CQRS del starter, tipología de rutinas, glosario CU/`_hr`/WAIVER/PG
compat, y veredicto “package como servicio”):
[`docs/plan-migracion-packages-cqrs.md`](plan-migracion-packages-cqrs.md).

Dos excepciones que sí conviene atacar primero por package, porque son
precondiciones de todo lo demás:

- **`GENERAL`** (3.481 líneas, 85 rutinas), usado por 47 packages y 23 triggers, e
  incluye la generación de identificadores. Diseño en
  `docs/migracion-package-general.md`. Un condicionante que surgió de ahí: **187 de
  los 408 reportes BIRT lo invocan dentro de sus consultas SQL**, así que necesita
  una capa de compatibilidad en PostgreSQL y no se puede reemplazar solo con Java.
- **`SEGURIDAD`** (286 líneas), hoja del grafo, junto con el modelo de autorización
  en tablas.

Después, el orden. Acá hay una tensión que conviene resolver explícitamente, porque las
dos métricas disponibles apuntan a lados opuestos. Por concentración de invocaciones y
volumen de código, el orden sería `LABORATORIO`, `FARMACIAS`, `FACTURACION`, `ATENCION`,
`TURNOS`. Por uso real medido (sección 3.5), `LABORATORIO` es de los últimos: 4.806
accesos anuales de **21 usuarios**, contra los más de 800.000 accesos del circuito
ambulatorio.

El criterio que se propone es el uso, no el código, por dos razones. Migrar primero lo que
más se usa es lo que antes libera valor y lo que antes expone los problemas de diseño con
volumen real. Y `LABORATORIO`, además de poco usado, es el módulo que concentra las tres
integraciones de motores, el hardware serie y el protocolo ASTM: es el peor candidato para
aprender. Orden sugerido, a validar con el cliente: el circuito ambulatorio
(`RECEPCION`, `DEMANDA_ESPONTANEA`, `ATENCION_TURNOS`, `ATENCION_MEDICA`), después
internación y enfermería, después farmacia y depósito, después facturación, y
`LABORATORIO` sobre el final o en convivencia prolongada.

`HISTORIA_CLINICA` e `INFORMES` merecen tratamiento aparte: son los de mayor alcance, con
más de 1.000 usuarios distintos cada uno, así que no son un caso de uso más sino
capacidades transversales. Lo mismo `PERSONAS` desde el lado de los datos (479 tablas,
964 validaciones): cimiento compartido, no un dominio más.

### Fase 5 — Portales satélite

`AGH`, `AGP`, `HOS-APP`, `RECETAS`, `PROVEEDORES`: 381 vistas en total y scaffolding
duplicado que se consolida en librería compartida.

### Fase 6 — Corte de datos legacy a PostgreSQL

Con la lógica de negocio ya en Java/Quarkus y validada por golden master. No es “el
primer contacto” con Postgres (los módulos nuevos ya viven ahí): es el **apagado
operativo del 11.2** y la consolidación del esquema/datos legacy (conversión
Windows-1252 → UTF-8, resolución de las 12.420 columnas `NUMBER` sin precisión, PK
para las 91 tablas operativas que no la tienen, constraints hoy deshabilitados,
reapunte de consumidores ODBC/QlikView).

### Fase 7 — Auditoría e histórico

942 millones de filas fuera del camino crítico, con el mecanismo nuevo ya operando.

---

## 8. Sobre las estimaciones de esfuerzo

Cualquier cifra de esfuerzo que se ponga hoy es una estimación por analogía, no una
medición. Lo honesto es explicitar las magnitudes y las incógnitas, y calibrar con
el piloto.

**Unidades de trabajo identificadas:**

| Unidad | Cantidad | Observación sobre su costo |
|---|---:|---|
| Puntos de invocación PL/SQL | 1.202 | Trazables uno a uno; muchos son variantes del mismo caso |
| Rutinas PL/SQL de negocio | 1.870 | 1.176 solo leen y devuelven cursor; 694 escriben |
| **Validaciones `RAISE_APPLICATION_ERROR`** | **5.443** | La mejor unidad de estimación disponible: cada una es una regla con mensaje al usuario, y no depende de heurísticas |
| Líneas útiles de PL/SQL de negocio | 308.138 | 84% en un ciclo que no se puede cortar por package |
| Vistas JSF | 3.301 | 344 son diálogos de búsqueda reutilizables y 608 son CRUD de configuración: ambos grupos admiten generación asistida |
| Beans `@ViewScoped` | 2.458 | Cada uno implica diseñar endpoints y estado en el cliente |
| Reportes BIRT | 408 | El motor se actualiza, no se reescribe. Pero **283 traen SQL Oracle** que hay que migrar sí o sí |
| Claves de i18n | ~46.503 | Con duplicación entre módulos; justifica un pipeline de extracción |
| Triggers con lógica | 126 | Decisión y prueba individual |
| Plantillas de SQL nativo | 348 | De 451 puntos; 110 son copias de un molde. Solo 59 puntos exigen decisión propia |

Conviene descontar lo que probablemente no haya que migrar: **11.792 líneas** en
siete packages sin ninguna referencia, más siete packages con sufijo `_` que parecen
retirados. Confirmarlo con `v$sql` de producción antes de presupuestar.

A eso se suma un descuento ya confirmado y sin necesidad de producción: en la consola de
seguridad, **seis de sus 17 tablas no existen en esta base** y con ellas caen 10 de sus
51 vistas. Son los subsistemas de roles de acceso y de usuarios de prestadores y
proveedores, que vienen en el producto pero no se usan en este cliente.

**Los tres factores que más pueden desviar la estimación:**

1. **El estado de los `@ViewScoped`.** 16.864 `p:ajax` indican pantallas muy
   interactivas contra el servidor. Trasladar ese estado al cliente y diseñar la API
   correspondiente es el trabajo dominante del frontend, y es donde las estimaciones
   por "cantidad de pantallas" fallan más.
2. **Las clases monstruo.** `BBRecepcionPaciente` con 12.968 líneas no se estima como
   una pantalla. Hay que descomponerla primero para saber qué contiene.
3. **La unidad de corte frente al ciclo de 29 packages.** Si se resuelve solo por
   puente prolongado a PL/SQL en 11.2 en vez de por caso de uso vertical + golden
   master, el apagado del Oracle (Fase 6) se posterga y el proyecto sostiene dos
   arquitecturas y dos motores mientras dure.

---

## 9. Riesgos y mitigación

| Riesgo | Impacto | Mitigación |
|---|---|---|
| Quarkus 3 / Hibernate contra Oracle 11.2 como datasource primario | Alto: fuera de soporte; SQL/paginación incorrectos | Runtime nuevo = PostgreSQL; 11.2 = legacy + golden master (§6) |
| Upgrade 11.2 → 19 “solo de puente” | Alto: segunda migración de motor | Descartado; ir a Postgres para lo nuevo |
| Pérdida silenciosa de reglas de negocio en los 126 triggers con lógica | Alto: los síntomas aparecen semanas después en producción | Golden master por trigger y decisión documentada caso por caso |
| Corrupción de caracteres al convertir a UTF-8 | Alto y difícil de detectar | Declarar `WINDOWS-1252`, nunca Latin-1; verificar el rango `0x80`-`0x9F` |
| Degradación de rendimiento por `numeric` | Medio, transversal | Inferir rangos reales de las 12.420 columnas antes de definir el esquema |
| Corte de identidad de los 3.970 usuarios internos | Medio, organizativo | Plan de comunicación y restablecimiento acotado al personal, de los que ~1.650 son activos por mes; el resto de las poblaciones migra con reforzado de hash en el primer login |
| Las interfaces con instrumental no funcionan en contenedor | Alto: afecta operación de laboratorio | Componente de borde con acceso al dispositivo, definido en Fase 1 |
| Planificar por dominio cuando el PL/SQL no se corta así | Alto: el plan se descubre inviable en ejecución | Unidad de trabajo por caso de uso vertical, frontera en los 1.202 puntos de invocación |
| Los 3 parches a clases internas de BIRT bloquean el upgrade | Medio: aparece tarde, cuando ya se decidió conservar los reportes | Diferenciarlos contra el fuente de 4.5 antes de comprometer el upgrade |
| Subestimar el SQL Oracle de los 283 reportes | Alto: es costo del frente de reportes en cualquier escenario de motor | Contarlo como trabajo propio, no como parte del upgrade |
| Consumidores directos de la base por fuera de la aplicación (QlikView, Excel, scripts) | Medio, y sorpresivo: se descubre roto después del corte | Inventariarlos **ya** (carril paralelo al piloto) con el ranking de logins por herramienta; definir si se reapunta a PostgreSQL o se reemplaza por API. Ver [`ajustes-prioridad-migracion.md`](../planificacion/ajustes-prioridad-migracion.md) A3 |
| Estimar sin haber medido | Alto para el negocio | El piloto de Fase 3 existe para esto |
| Vulnerabilidades de las dependencias durante la transición | Medio | Ver sección 10 |

---

## 10. Deuda de seguridad

Vigente mientras el legacy siga en producción.

### 10.1 En el código

**Ocho puntos con inyección SQL viable.** Concatenan un valor que llega desde Java
directamente contra un comparador o una lista de valores dentro de la sentencia, en
lugar de pasarlo como parámetro. Cuatro están en `ImpCodPrestGrpCodPrest`, y el resto
en `BBRegistroPrescriptor`, `ImpControlDatosPersonal`, `ImpFacturacionInternado` e
`ImpInterfaces`. El detalle con archivo y línea está en `SQL-NATIVO.md`. Se corrigen
parametrizando, sin esperar a la migración, y conviene hacerlo antes porque el legacy
va a seguir en producción durante todo el proyecto.

Hay otros 151 puntos que interpolan texto en posiciones menos expuestas, como nombres
de columna o cláusulas de ordenamiento. No son explotables del mismo modo, pero
conviene parametrizarlos al reescribir.

**Inyección de DDL en el par de suplantación de usuarios.**
`TS.SEGURIDAD.f_bypass_user_login` y `f_reestablecer_user_pass` construyen sentencias
`ALTER USER` concatenando los parámetros recibidos, y corren con privilegios de lectura
sobre `SYS.USER$` y de modificación de usuarios. Quien pueda invocarlas puede ejecutar
DDL arbitrario. El detalle está en 5.1.1. A diferencia de los ocho puntos anteriores,
esto no se arregla parametrizando: hay que rediseñar el mecanismo.

**Autorización que falla abierta.** `f_tiene_permiso` concede el acceso cuando la acción
solicitada no está registrada en `MENU_APLICACION`, en lugar de negarlo, y compara con
`LIKE` sobre un patrón recibido por parámetro.

**Semilla de contraseñas fija en el código.** `SecurityManager` de la consola pasa la
constante `3183856184` a `dbms_user_security` en cada validación y cada cambio de
contraseña. Está en el fuente, en claro.

**Las contraseñas de los portales están debilitadas de tres formas a la vez.** El valor
guardado en `CONTRASENA_WEB` es `SHA-256("THINKSOFT" + minúsculas(contraseña))`: el
`"THINKSOFT"` es un *pepper* global escrito en el fuente en PL/SQL, Java y JavaScript, no
hay sal por usuario, y el paso a minúsculas recorta el espacio de claves. Son 146.312
credenciales, en su mayoría de pacientes, verificables con un algoritmo rápido. El detalle
está en [`docs/migracion-identidad.md`](migracion-identidad.md), sección 2.5.

### 10.2 En la configuración del motor

**No hay política de contraseñas.** El perfil `DEFAULT`, que usan las 4.091 cuentas de
aplicación, tiene `FAILED_LOGIN_ATTEMPTS`, `PASSWORD_LIFE_TIME`, `PASSWORD_LOCK_TIME` y
`PASSWORD_REUSE_MAX` en `UNLIMITED`, y `PASSWORD_VERIFY_FUNCTION` en `NULL`. Sin caducidad,
sin complejidad y sin límite de intentos fallidos.

**2.584 cuentas de personal dado de baja siguen abiertas.** De los 3.468 legajos en estado
`BAJA`, 2.584 conservan su usuario de Oracle en estado `OPEN`. El egreso no elimina la
cuenta. Sumado al punto anterior, son credenciales sin caducidad ni bloqueo pertenecientes
a gente que ya no trabaja en la institución. No requiere esperar la migración: es una
limpieza que se puede hacer ahora.

### 10.3 En las dependencias

| Librería | Versión | Módulos | Situación |
|---|---|---:|---|
| log4j | 1.2.16 / 1.2.17 | 16 | Fin de vida; CVE-2019-17571 |
| commons-collections | 3.x | 9 | CVE-2015-6420 (deserialización) |
| dom4j | 1.6.1 | 11 | CVE-2018-1000632 (XXE) |
| jackson-databind | 2.9.8 | 7 | Múltiples CVE de deserialización |
| commons-fileupload | 1.2.2 | 8 | CVE-2016-3092, CVE-2023-24998 |
| axis2 | 1.6.2 | 14 | Fin de vida |
| Hibernate | 4.2.6 | 15 | Fin de vida, sin parches |
| mysql-connector | 5.1.45 | 9 | Fin de vida |
| jcifs | 1.3.17 | 11 | Fin de vida |
| iText | 2.1.7 | 7 | Legacy, además de licenciamiento AGPL |

No hay Struts ni log4j 2.x. En `ANUNCIADOR`: `axios` 0.19.2, `socket.io` 2.3.0,
`express` 4.17.1.

---

## 11. Lo que falta relevar

**Prioridad operativa (2026-08-14):** los ítems 1, 4 y la remediación de inyección
(§10.1) no esperan a Fase 6 — van en **carril paralelo YA**, junto con el gate de
Identity (¿IdP corporativo o emisor propio?) y el spike de borde lab (serie/ASTM) en
Fase 1. Detalle y anti-patrones:
[`docs/sdd/ajustes-prioridad-migracion.md`](../planificacion/ajustes-prioridad-migracion.md).

1. **`v$sql` sobre producción**, y es el único pendiente que bloquea el presupuesto. El
   diccionario de la copia no sirve para datar objetos, porque el clonado reescribió
   `created` y `last_ddl_time` de los 12.670 objetos, y su caché de sentencias tampoco,
   porque refleja trabajos programados y al equipo de desarrollo. Ya se verificó que **el
   usuario `ts` tiene permiso de lectura sobre `v$sql`**, así que el pedido al DBA es
   concreto y de bajo riesgo: correr las mismas consultas de `VerificarOracle` contra
   producción, idealmente varias veces a lo largo de una semana para que la caché cubra
   los picos de uso. **Pedir la captura de una semana ahora.**
2. **Los 136 scripts de `RDBMS`**, que documentan la evolución del esquema por
   versión y pueden aportar historia que el diccionario clonado perdió.
3. **Validación funcional con usuarios**, ahora acotada. El ranking de uso por módulo de
   la sección 3.5 ya responde la pregunta gruesa; lo que queda es el nivel de pantalla,
   que ningún log cubre, y confirmar con los referentes por qué módulos como
   `LABORATORIO` tienen alta complejidad técnica y solo 21 usuarios.
4. **El inventario de consumidores directos de la base.** Hay al menos un QlikView con
   10.481 logins semestrales, Power Query de Excel, scripts de Python y clientes de
   consulta. Hay que enumerar qué extraen y quién los mantiene, porque cambian de motor
   junto con la aplicación. **Adelantar a carril paralelo (no dejarlo para “antes de
   Fase 6” como hito lejano):** define el plan de corte.
5. **Los 7 packages con sufijo `_`**, para confirmar que son código muerto y descontar
   11.792 líneas del alcance. Se verificó que los 14 packages con ese sufijo siguen
   existiendo y están `VALID`, pero la fecha de último DDL es la del clonado, así que no
   informa nada. Siete son plomería de auditoría (`TBL_AUD_*`) y siete son de negocio:
   `AMBIENTES_`, `BUSQUEDA_PONDERADA_`, `PEDIDOS_`, `PRESENTACION_FACTURACION_`,
   `SCHEDULER_`, `TRADUCCION_` y `USER_PORTAL_THINKSOFT_`. Se resuelve con el punto 1.
6. **Los 24 puntos de SQL armado en tiempo de ejecución**, que el análisis estático no
   puede reconstruir. Son ocho casos reales de negocio; el resto son envoltorios.

Una advertencia sobre el punto 1, aprendida en el intento: `ALL_TAB_MODIFICATIONS`
tampoco sirve en la copia. Registra el DML desde la última recolección de estadísticas, y
en esta instancia eso arranca en julio de 2026, o sea después del clonado. Lo que muestra
es qué está probando el equipo de desarrollo, no qué usa el hospital. La misma consulta
sobre producción sí es válida, y está incluida en `VerificarOracle`.

---

## Anexo: cómo reproducir el relevamiento

```bash
# Requiere VPN activa y tools/relevamiento/oracle.env completo.
# Extracción (solo lectura, reanudable, tolera cortes de VPN):
java -cp AGI/WebRoot/WEB-INF/lib/ojdbc7.jar tools/relevamiento/ExtraerOracle.java

# Informe de hallazgos a partir de lo extraído:
python3 tools/relevamiento/analizar.py

# Analisis de los packages de negocio (peso, naturaleza, dependencias, ciclos):
python3 tools/relevamiento/analizar-packages.py

# Inventario del SQL nativo embebido en el Java (no necesita la base):
python3 tools/relevamiento/analizar-sql-nativo.py .

# Verificacion puntual contra la base (solo lectura, requiere VPN):
java -cp AGI/WebRoot/WEB-INF/lib/ojdbc7.jar tools/relevamiento/VerificarOracle.java
```

`VerificarOracle` es el que hay que correr contra producción para cerrar el punto 1 de la
sección 11. Cada consulta está aislada, así que un permiso faltante no interrumpe el
resto, y no escribe nada en la base.

La salida queda en `tools/relevamiento/out/` (no versionada): 32 archivos CSV con el
diccionario, 2.541 archivos con el código PL/SQL, `RESUMEN.md` con el detalle de la
extracción, `HALLAZGOS.md` con el informe general, `PACKAGES.md` con el detalle por
package, `SQL-NATIVO.md` con el inventario de SQL embebido y `VERIFICACION.md` con las
mediciones puntuales contra la base.

Ninguno de los tres scripts de análisis se conecta a la base: leen lo ya extraído o
directamente los fuentes, así que se pueden re-ejecutar sin VPN.
