# Guía de pruebas — Flujo E2E (Hospital-Web ↔ Hospital-Api ↔ schema `ts`)

> Guía práctica para probar el stack nuevo contra el PG remoto. Refleja el estado real
> al cierre de la migración a `ts.*` (Fase 2 + AGI + catálogo, piloto `public.*` retirado).

---

## 1) Stack necesario

| Componente | Puerta | Descripción |
|---|---|---|
| PostgreSQL remoto | `10.0.0.35:10520` | Base `grupogea-hospital_dev-test` (schema `ts` con datos demo) |
| **Hospital-Identity** | `:8080` | Login JWT (`admin` / `Admin123!`) |
| **Hospital-Api** | `:8081` | Backend Quarkus (schema `ts.*`) |
| **Hospital-Web** | `:4200` | Front Angular |

> Si un componente no está arriba, levantarlo como dev (ver notas al final). El **Web apunta a `http://127.0.0.1:8081/api`** (configurada en `Hospital-Web/appsettings.json`).

---

## 2) Credenciales y documentos demo

- **Login humano (ops/recepción)**: abrir el Web → login → `admin` / `Admin123!`.
- **Display TV / API Key**: los endpoints de anunciador/AGI aceptan `X-API-Key: demo-api-key` (sin login), igual que el display.

**Documentos demo de AGI** (para el tótem `/agi/recepcion`). El convenio 5001 ahora **exige validador + elegibilidad**, así que el flujo distingue:

| Documento | Resultado | Ticket | ¿Dónde se ve? | ¿Se llama desde? |
|---|---|---|---|---|
| `30111222` | ✅ Autorizado | "Espera de atención" (`T NNNN`) | `/recepcion/espera-amb` (Cola B) | Desde la espera de **servicios** (Cola B) |
| `30777888` | ✅ Autorizado | ídem | ídem | ídem |
| `30666888` | ✅ Autorizado (token) | ídem | ídem | ídem |
| `30999888` | ❌ **Rechazo** (control administrativo) | "Espera de recepción" (`T NNNN`) | `/agi/espera` + `/recepcion/cola` (Cola A humana) | Desde `/agi/espera` o `/recepcion/cola` → **Llamar** → display |

> **Clave:** el autorizado NO se llama desde `/agi/espera`. Solo el **rechazado** entra a la Cola A humana (que es lo que muestra `/agi/espera`) y se llama ahí. El autorizado va a Cola B (espera-amb).

---

## 3) Flujos de prueba (pantalla por pantalla)

### A) Anunciador simple — `/anunciadores` y `/display/anunciadores/1001`

1. Ir a `/anunciadores` (apunta a ANU-DEMO, id **1001**, `mostrar_prof_ocupa_amb = N` → display **simple**).
2. Abrir el display → `/display/anunciadores/1001`.
3. Debe mostrar: **llamados** (ventana de `ctd_minutos_muestra_pac`, default 5 min), diccionario TTS, logo/fondo.
4. **Probar el llamado en el display simple:**
   - Desde `/recepcion/cola` (flujo D) o `/agi/espera` (flujo F) presionar **Llamar** de un paciente.
   - El display `1001` actualiza por **WebSocket** mostrando el paciente (ej. `PACIENTE, Demo`) con `llamar='S'` (rojo) y luego pasa a `N` al consumirse.

### B) Anunciador compuesto — `/display/anunciadores/1002`

1. Abrir `/display/anunciadores/1002` (ANU-COMP, id **1002**, `mostrar_prof_ocupa_amb = S` → display **compuesto**).
2. DEBE mostrar además de los llamados, la **ocupación de ambientes**:
   - Consultorio 1 → **GOMEZ, CARLOS**
   - Consultorio 2 → **LOPEZ, MARIANA**
   - Consultorio 3 → **DIAZ, PEDRO**
3. Esa ocupación sale de `ts.ocupacion_ambiente_amb` + `ts.det_ocupacion_ambiente_amb` + `ts.persona` (fecha de HOY, logout null).
4. También se puede ver la ocupación por API: `GET http://127.0.0.1:8081/api/v1/anunciadores/1002/ocupacion-ambientes` (header `X-API-Key: demo-api-key`).

### C) Recepción Cola A (humana) — `/recepcion/cola`

> Corresponde a `ts.cola_espera_recep` (espera de recepción humana). Data demo: 5 tickets (4001-4005) con pacientes.

1. Ir a `/recepcion/cola`. Muestra **pendientes** (prioridad + FIFO), **últimos llamados** y **conteo por prefijo** (`A`, `B`, `C`).
2. En el detalle está el **puesto/box** (3001 / 3002). Elegir un box y presionar **Llamar**.
3. Al llamar:
   - La fila pasa a `llamado='S'` (aparece en "últimos llamados").
   - Se inserta en `ts.llamado_anunciador` → **aparece en el display** del anunciador (A o B, según el `anunciador_id` resuelto).
   - Control de **throttle de 5 s**: si se llama al mismo box < 5 s, responde `400 "Debe aguardar 5 segundos"` (es correcto).
4. Verificador por API: `GET http://127.0.0.1:8081/api/v1/recepcion/colas/2001/pendientes` (X-API-Key).

### D) Cola B / Espera ambulatoria — `/recepcion/espera-amb`

> Corresponde a `ts.cola_espera_serv_amb` (espera de atención por servicio). Data demo: 3 filas (5001-5003) con paciente + servicio.

1. Ir a `/recepcion/espera-amb?idCentroAte=1001`. Muestra: **paciente, tipo, servicio, especialidad, profesional, recepción, estado** (fiel al legacy: **sin columna Ticket**).
2. Data demo visible:
   - `GARCIA, JUAN DEMO` / CLINICA MEDICA / en espera
   - `PEREZ, LUIS DEMO` / CLINICA MEDICA / en espera
   - `RODRIGUEZ, ANA DEMO` / LABORATORIO / en espera
3. Verificador por API: `GET http://127.0.0.1:8081/api/v1/recepcion/espera-amb?idCentroAte=1001` (X-API-Key).
   - Filtros: `servicio`, `personal`, `colaEspera` (COLUNA), `estado` (`EN_ESPERA`/`EN_ATENCION`).

### E) Tótem AGI — `/agi/recepcion` (confirmar recepción / sacar turno)

1. Ir a `/agi/recepcion`.
2. Tipear un documento demo (p.ej. **`30111222`**) → Enter. Identifica al paciente (**PACIENTE, Demo**, id `20001` SoT Turnos).
3. Lista **turnos OTORGADO** (hay 3 turnos demo). Elegir un turno.
4. En "convenio/requerimientos": el convenio 5001 NO exige validador (menos "sin seed"). Con un documento de `elegibilidad_seed` el flujo decide **autorizado** vs **rechazo**:
   - `30111222`, `30777888`, `30666888` → autorizado → ticket `T NNNN` → entra a **Cola B** (se ve en `/recepcion/espera-amb` con paciente + servicio).
   - `30999888` → **rechazo** (control administrativo) → entra a **Cola A humana** (se ve en `/recepcion/cola`).
5. Al final muestra el **ticket físico** con formato `T 0004` (prefijo + espacio + 4 dígitos, fiel al tótem).
6. **Importante (fidelidad):** el ticket del AUTORIZADO va a `ts.recepcion_amb` + `ts.cola_espera_serv_amb` (NO a la cola de recepción). Se ve en **espera-amb**, no en `agi/espera`.
   - El ticket del RECHAZO va a `ts.cola_espera_recep` → se ve en **recepción/cola**.

### F) Espera AGI — `/agi/espera`

1. Ir a `/agi/espera`.
2. Hay un selector de **destino**:
   - **Espera recepción** (`ESPERA_RECEPCION`, o sin filtro) → muestra la **cola humana** (`cola_espera_recep`, 4001-4005) con paciente.
   - **Espera atención** (`ESPERA_ATENCION`) → normalmente **vacío**: las autorizadas van a `recepcion_amb`/Cola B, NO a la cola humana (fiel al legacy). Para ver autorizadas hay que ir a `/recepcion/espera-amb`.
3. Botón **Llamar** en una fila de cola humana → inserta en `ts.llamado_anunciador` y aparece en el display.

### G) Catálogo de convenios — `/catalogo/convenios`

1. Ir a `/catalogo/convenios`. Lista los convenios desde `ts.convenio` (OBRA SOCIAL DEMO).
2. Crear/editar un convenio → se mantiene en `ts.convenio` fiel (id numérico string, campos `cod_conv_validador` como codigo, etc.).

---

## 4) Datos demo disponibles en `ts` (resumen)

| Entidad | Ids demo |
|---|---|
| Anunciador simple | `1001` ANU-DEMO |
| Anunciador compuesto | `1002` ANU-COMP (con ocupación) |
| Pacientes | `20001` PACIENTE Demo (DNI 30111222) · `20002` PEREZ · `20003` RODRIGUEZ |
| Personas (pacientes) | `20001`/`20002`/`20003` |
| Personas (personal ocupación) | `9001` GOMEZ · `9002` LOPEZ · `9003` DIAZ |
| Turnos | `30001`/`30002`/`30003` (OTORGADO) |
| Convenio | `5001` OBRA SOCIAL DEMO |
| Servicios | `10` CLINICA MEDICA · `20` LABORATORIO |
| Terminal AG | `1` (+ opción 1) |
| Recepción | `2001` (+ puestos 3001/3002) |
| Cola A | `4001`-`4005` |
| Cola B | `5001`-`5003` |
| Recepción amb (ref cola B) | `1`, `2` |
| Ocupación ambientes | Consultorios 1/2/3 con personal |

---

## 5) Notas de fidelidad (por qué se ve así)

- **`agi/espera` ES PERA_ATENCION vacío**: los autorizados van a `recepcion_amb` + `cola_espera_serv_amb` (atención por servicio). El legacy separa autorizados (van a atención/TV) de humanos (cola de recepción). No es error.
- **`espera-amb` sin columna Ticket**: el legacy `colaEspera.xhtml` no muestra ticket en esa grilla y deja `prefijo_ticket_recep/nro_ticket_recep` en NULL. Fiel.
- **Ticket físico del tótem**: formato `T 0004` (prefijo + espacio + 4 dígitos) de `tmp_ticket_autorecepcion.nro_espera_recep`.
- **"Sin seed" en AGI**: aparece solo si el documento no está en `public.elegibilidad_seed` Y el convenio exige validador online + elegibilidad. Con el convenio 5001 (no exige) y los 4 docs demo no debería aparecer.
- **❌ Error 500 `duplicate key pk_recepcion_amb` al confirmar en AGI**: pasa si se hizo un **seed manual en `ts` con ids manuales** pero **NO se sincronizaron las secuencias** (`sec_id_recepcion_amb`, `sec_id_turno`, etc.). Cuando AGI genera un id con `nextval`, choca con un id ya sembrado. **Fix:** sincronizar las secuencias al máximo de cada tabla (ver §7).

---

## 7) Sincronizar secuencias después de un seed manual en `ts`

> Importante tras limpiar+re-sembrar (o insertar ids manuales): dejar las secuencias al día para que AGI/confirmación no colisionen (500 `pk_*`).

```sql
SELECT setval('sec_id_recepcion_amb',       GREATEST((SELECT COALESCE(MAX(id_recep_amb),0)         FROM ts.recepcion_amb),1)::bigint);
SELECT setval('sec_id_turno',               GREATEST((SELECT COALESCE(MAX(id_turno),0)             FROM ts.turno),1)::bigint);
SELECT setval('sec_id_cola_espera_recep',   GREATEST((SELECT COALESCE(MAX(id_cola_espera_recep),0) FROM ts.cola_espera_recep),1)::bigint);
SELECT setval('sec_id_cola_espera_serv_amb',GREATEST((SELECT COALESCE(MAX(id_cola_espera_serv_amb),0) FROM ts.cola_espera_serv_amb),1)::bigint);
SELECT setval('sec_id_ord_serv_amb',        GREATEST((SELECT COALESCE(MAX(id_ord_serv_amb),0)      FROM ts.ord_serv_amb),1)::bigint);
SELECT setval('sec_id_det_prest_recep_amb', GREATEST((SELECT COALESCE(MAX(id_det_prest_recep_amb),0)FROM ts.det_prest_recep_amb),1)::bigint);
SELECT setval('sec_id_llamado_anunciador',  GREATEST((SELECT COALESCE(MAX(id_anunciador_paciente),0)FROM ts.llamado_anunciador),1)::bigint);
```

> Sin esto, el siguiente `POST /agi/recepciones` puede fallar con 500 (`duplicate key`).

---

## 8) Levantar el stack (si hace falta)

```powershell
# Desde C:\Repos\GrupoGEA\GrupoGeaFull
$env:MAVEN_HOME='C:\Users\ealbo\maven\apache-maven-3.9.16'
$env:Path="$env:MAVEN_HOME\bin;$env:Path"

# Identity (:8080) — contra el remoto
$env:HOSPITAL_PG_URL='jdbc:postgresql://10.0.0.35:10520/hospital_identity'; $env:HOSPITAL_PG_USER='postgres'; $env:HOSPITAL_PG_PASSWORD='<pass>'
mvn -f Hospital-Identity/pom.xml -pl presentation-api -am quarkus:dev

# Api (:8081) — contra el remoto, puerto 8081 (por defecto)
$env:HOSPITAL_PG_URL='jdbc:postgresql://10.0.0.35:10520/grupogea-hospital_dev-test'; $env:HOSPITAL_PG_USER='postgres'; $env:HOSPITAL_PG_PASSWORD='<pass>'
mvn -f Hospital-API/pom.xml -pl presentation-api -am quarkus:dev "-Dquarkus.http.port=8081"

# Web (:4200)
cd Hospital-Web; npm ci; npx ng serve
```

> `-Duser.timezone=UTC` si el Postgres 16 de pruebas rechaza `America/Buenos_Aires`.
> No subir credenciales a git; el password del PG remoto es privado.
