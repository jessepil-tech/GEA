# Migración del package `TS.GENERAL`

Diseño de la primera pieza a migrar. Complementa `docs/dossier-migracion.md`.

Mapeo packages → CQRS / handlers / repositories (plan transversal):
[`plan-migracion-packages-cqrs.md`](plan-migracion-packages-cqrs.md).

**Hallazgos VPN 2026-08-14 (índice):**  
[`hallazgos-golden-master-vpn-2026-08-14.md`](hallazgos-golden-master-vpn-2026-08-14.md)

- **Objeto:** `TS.GENERAL` (**209** líneas de especificación, **4.135** de cuerpo — VPN)
- **Rutinas (`ALL_PROCEDURES`):** **85** (el histórico hablaba de 82 públicas + privadas)
- **Lo usan:** 47 packages, 23 triggers, 187 reportes BIRT y 58 invocaciones desde Java
- **Evidencia:** `tools/golden-master/fixtures/general/`

---

## 1. Por qué esta pieza primero

`GENERAL` es el cuello de botella del grafo de dependencias: lo usan 47 de los 61
packages de negocio y 23 triggers. Mientras no exista su equivalente en el destino,
ningún otro package puede cerrarse. Además contiene la generación de identificadores,
que se invoca en **cada insert** que hace Hibernate.

La segunda razón es que es tratable. A pesar de su posición central, **no es lógica
de negocio**: es una biblioteca de utilidades. De sus 82 rutinas públicas:

| Categoría | Rutinas | Naturaleza |
|---|---:|---|
| Códigos de barra | **31** | Cálculo puro, sin acceso a reglas |
| Fechas y edades | 14 | Cálculo puro |
| Generación de identificadores | 10 | **El trabajo real** |
| Conversión de tipos | 7 | Desaparece: existe por semántica NLS de Oracle |
| Tokens y cifrado | 5 | Va con el frente de identidad |
| Sesión y permisos | 4 | Va con el frente de identidad |
| Multiempresa | 2 | Lectura de configuración |
| Borrado masivo | 2 | **No debe existir en el destino** |
| Otros | 10 | Validación de CUIT, tipo de comprobante AFIP, formularios de HC |

Los códigos de barra son el 38% de la superficie y son el caso más mecánico: generan
cadenas Interleaved 2 of 5 y EAN-13 a partir de identificadores. En el destino son una
librería de códigos de barra más pruebas de equivalencia.

**14 de las 82 rutinas son envoltorios `_hr`** que solo existen para devolver un
`RESULT_SET` y que Hibernate pueda invocarlas como consulta nombrada. No tienen lógica
propia: desaparecen por completo.

---

## 2. La restricción que define el diseño

La intuición sería reimplementar `GENERAL` en Java y borrar el package. **No se puede**,
y el motivo aparece al ver quién lo llama:

| Consumidor | Funciones distintas que usa | Observación |
|---|---:|---|
| Otros packages PL/SQL | 46 | Llamadas embebidas en SQL y en lógica |
| Reportes BIRT | 11 | Pero repartidas en **187 de los 408 reportes** |
| Solo desde Java | 19 | De las cuales 12 son envoltorios `_hr` |

**54 funciones distintas tienen que seguir siendo invocables desde la base de datos.**
Los reportes BIRT las llaman dentro de sus consultas SQL, y la estrategia del dossier
es conservar BIRT server-side. Los 13 packages que piden códigos de barra hacen lo
mismo durante toda la convivencia.

La consecuencia práctica es favorable: **de las 11 funciones que necesita BIRT,
bastan las 3 más usadas de GENERAL para cubrir casi todo el volumen de edad/barcode**.

Conteo real en `.rptdesign` del monorepo (2026-08-14) vs catálogo Oracle:

| Función (llamada en BIRT) | Usos | Realidad en Oracle 11.2 |
|---|---:|---|
| `ts.personas.f_get_persona_full` | **301** | Existe en `PERSONAS` (el doc la confundía con `proveedor_full`) |
| `ts.general.f_get_edad_anio` | 121 | OK — golden master PASS |
| `ts.general.f_get_interleaved_2_5` | 98 | OK — golden master PASS |
| `ts.personas.f_get_matricula_full` | 82 | Fuera de GENERAL — fuente en `fixtures/personas/` |
| `ts.personas.f_get_especialidad_full` | 56 | Fuera de GENERAL — fuente capturada |
| `ts.personas.f_get_personal_matricula` / `f_get_persona_telefono` | 54 c/u | Fuera de GENERAL — fuentes capturadas |
| `ts.general.f_get_edad_string` | 22 | OK — golden master PASS |
| `ts.general.f_get_domicilio_persona` | 11 | **No existe** (ORA-00904). Real: `PERSONAS` → VARCHAR2. Ver `birt_ghost_*.csv` |
| `ts.general.f_get_proveedor_full` | 4 | **No existe** (ORA-00904). Sustituto: `PERSONAS.f_get_persona_full` |
| `ts.general.f_prestacion` | 5 | OK — fuente capturada |
| `ts.general.f_concat_val_pos_dom_hc` | 3 | OK — fuente + bordes sintéticos |

CSV canónico de usos: `tools/golden-master/fixtures/misc/birt_function_counts.csv`.

Portar edad + interleaved + `PERSONAS.f_get_persona_full` desbloquea el grueso de reportes vivos.
Los Retención* con `GENERAL.f_get_*` fantasma hay que **corregir el SQL** al package real (no solo portar).

Inventario VPN: `tools/golden-master/fixtures/general/general_routines.csv` (**85** rutinas).

Portar esas funciones a PostgreSQL (más arreglar los fantasma) desbloquea BIRT
sin tocar la mayoría de `.rptdesign` sanos.

---

## 3. La generación de identificadores

Es la parte que exige diseño, no traducción. `f_next_id_tabla` es una **cascada de
tres niveles** según el nombre de tabla que recibe:

1. **Despacho a secuencias de Oracle.** Una cadena de `if as_tabla = 'X' then return
   ts.sec_id_X.nextval`. Cubre la mayoría de los casos.
2. **Tablas contador dedicadas.** Para 11 entidades hace
   `SELECT prox_id ... FOR UPDATE` seguido de `UPDATE prox_id + 1`.
3. **Contador genérico.** Si no cayó en ninguna rama, busca la fila de la tabla en
   `TS.SEC_ID_TABLA` (348 filas, una por tabla) con `FOR UPDATE`.

De los **112 objetos `sec_id_*` que referencia**, la distribución es mucho mejor de
lo que sugiere el mecanismo:

| Tipo | Cantidad | Costo de migración |
|---|---:|---|
| Secuencias reales de Oracle | **100** | Trivial: `CREATE SEQUENCE` más `setval` |
| Tablas contador con `FOR UPDATE` | **11** | Requiere decisión |
| Referenciado pero inexistente | 1 | `SEC_ID_E_PAGO` (ver defectos) |

Las 11 tablas contador son `SEC_ID_TABLA`, `SEC_ID_PERSONA`, `SEC_ID_ATENCION_AMB`,
`SEC_ID_ATENCION_INT`, `SEC_ID_INTERNACION`, `SEC_ID_MENSAJE_INTERFACE_PAC`,
`SEC_ID_MOV_STOCK`, `SEC_ID_ORD_SERV_AMB`, `SEC_ID_ORD_SERV_INT`,
`SEC_ID_PRESCRIP_PREST_AMB` y `SEC_ID_RECETA_PAC`.

### 3.1 El costo oculto del patrón `FOR UPDATE`

Un contador en tabla con `SELECT ... FOR UPDATE` **serializa todas las inserciones**
de esa entidad contra un único lock de fila, que se mantiene hasta el commit de la
transacción que lo tomó. Cada alta de paciente o de atención espera a la anterior.

Que el legacy convive con esto no significa que sea aceptable en el destino: las
secuencias de PostgreSQL no son transaccionales y no bloquean, así que el cambio
**elimina el punto de contención** en lugar de trasladarlo.

Hay evidencia de que el problema se conocía: `f_next_id_tabla` desvía 10 tablas de
honorarios y liquidación a `pf_next_id_tabla_aut`, que es el mismo código envuelto en
`PRAGMA AUTONOMOUS_TRANSACTION` con `commit` propio, justamente para liberar el lock
sin esperar a la transacción del negocio. Ese comportamiento es exactamente el de
`nextval` en PostgreSQL, así que esas 10 tablas se mapean sin discusión.

### 3.2 Claves con semántica incrustada

`ID_INTERNACION` **no es un subrogado opaco**. Se construye así:

```
id_internacion = lpad(nro_centro, 2, '0')
              || lpad(cod_tipo_admision, 2, '0')
              || lpad(secuencial_por_centro_y_tipo, 7, '0')
```

Para internaciones de bebé el tipo de admisión se reemplaza por `'00'`. El secuencial
sale de `SEC_ID_INTERNACION`, que está indexada por `ID_CENTRO_ATE` y `TIPO_ADMISION`:
es un contador por centro y por tipo, no global.

El identificador se **descompone después** en otras consultas, por ejemplo
`substr(id_internacion, 2, 2) = '00'` para detectar bebés. Esto lo convierte en un
dato con formato, no en una clave técnica, y hay que decidir explícitamente si el
formato se preserva. Como aparece impreso en códigos de barra y etiquetas, lo más
probable es que preservarlo sea obligatorio, al menos durante la convivencia.

Conviene revisar también `f_next_nro_receta_pac`, `f_get_nro_orden` y
`COD_GS1_PERSONAL` buscando el mismo patrón.

---

## 4. Defectos latentes encontrados

Cuatro cosas que el análisis expuso y que **no hay que reproducir** al migrar.

### 4.1 Condición de carrera en identificadores compuestos

`f_next_id_compuesta_tabla` obtiene el siguiente valor con
`SELECT NVL(MAX(columna), 0) + 1` sobre SQL dinámico, **sin lock ni secuencia**. Dos
sesiones concurrentes pueden obtener el mismo identificador. Es un defecto real del
legacy, no una particularidad de Oracle, y explicaría claves duplicadas esporádicas
si el sistema las reporta.

### 4.2 Ceros a la izquierda que se pierden

El `lpad` de `f_next_id_internacion` produce una cadena, pero `ID_INTERNACION` está
declarada `NUMBER(22)`. La conversión implícita **descarta el cero inicial** cuando el
número de centro es menor a 10, y el identificador queda de 10 dígitos en lugar de 11.
El propio código lo asume al filtrar por `length(id_internacion) = 10`. Cualquier
lógica de migración que corte por posición fija tiene que contemplar los dos largos.

### 4.3 Secuencia inexistente

`SEC_ID_E_PAGO` se referencia en el cuerpo pero **no existe** en el esquema, ni como
secuencia ni como tabla. La rama que la usa falla si se ejecuta, lo que sugiere que
es código muerto; hay que confirmarlo antes de portarlo.

### 4.4 Descubrimiento del esquema en tiempo de ejecución

Cuando el contador genérico no encuentra la fila de una tabla, la crea consultando
`ALL_TABLES` y `ALL_CONS_COLUMNS` para **descubrir la clave primaria en caliente** y
sembrar el contador con `MAX(pk) + 1`. Funciona, pero significa que la generación de
identificadores depende del diccionario de datos y de que la tabla tenga PK simple.
Recordando que hay 571 tablas sin clave primaria, esta rama es una fuente de errores
en tiempo de ejecución. En el destino no debe existir.

---

## 5. Diseño destino

Tres capas, según quién necesita cada cosa.

### Capa 1 — Secuencias nativas de PostgreSQL

Las 100 secuencias se crean con `CREATE SEQUENCE` y se posicionan con `setval` al
valor de corte. Las 11 tablas contador se convierten en secuencias, con dos
excepciones que siguen necesitando un contador explícito porque son **por clave de
negocio**, no globales:

- `SEC_ID_INTERNACION` (por centro y tipo de admisión)
- Cualquier otra que el relevamiento de claves con formato confirme

Para esas, el contador se implementa con `INSERT ... ON CONFLICT DO UPDATE ...
RETURNING`, que es atómico y no mantiene el lock hasta el commit.

Con esto **desaparece `f_next_id_tabla` como punto de entrada**: la asignación de
identificadores pasa a ser responsabilidad del mapeo de entidades, no de una llamada
explícita por insert.

### Capa 2 — Capa de compatibilidad en PostgreSQL

Las **11 funciones que consumen los reportes BIRT**, portadas a SQL o PL/pgSQL con
la misma firma y el mismo nombre. Objetivo explícito: que los 187 `.rptdesign` sigan
funcionando sin editarlos. Es la pieza que permite postergar la decisión sobre BIRT.

Durante la convivencia, esta capa crece para cubrir las 46 funciones que invocan
otros packages, y se reduce a medida que esos packages se van portando.

### Capa 3 — Biblioteca de dominio en Java

Todo lo que es cálculo puro se implementa en Java y deja de existir en la base:

- **Códigos de barra:** librería de generación, con las cadenas Interleaved 2 of 5 y
  EAN-13 validadas contra las que produce hoy el package.
- **Fechas y edades:** `java.time`. Atención a las reglas de edad, que en contexto
  clínico distinguen años, meses y días y tienen casos borde propios.
- **Validaciones:** CUIT y tipo de comprobante AFIP son funciones puras con
  algoritmo conocido.
- **Conversión de tipos:** no se porta. Las siete funciones `VARCHAR_TO_NUMBER` y
  similares existen porque PL/SQL necesita conversión sensible a NLS; Java no tiene
  ese problema.

---

## 6. Lo que no se migra

| Rutina | Motivo |
|---|---|
| Los 14 envoltorios `_hr` | Solo existen para que Hibernate pudiera invocar por cursor |
| `p_truncate_table` | `EXECUTE IMMEDIATE 'truncate table ...'` con esquema y tabla por parámetro: DDL dinámico invocable desde la aplicación |
| `p_borrar_datos_tabla` | `EXECUTE IMMEDIATE 'DELETE FROM ...'` en transacción autónoma |
| Las 7 conversiones NLS | Sin equivalente necesario en Java |
| La rama de descubrimiento de PK | Ver 4.4 |
| `SEC_ID_E_PAGO` | Referencia a un objeto inexistente |

`p_truncate_table` y `p_borrar_datos_tabla` merecen una nota aparte: son borrado
masivo con nombre de tabla por parámetro, expuesto como procedimiento invocable. Hay
que verificar quién los llama y con qué privilegios antes de decidir cómo se
reemplazan, porque además de no ser migrables son un riesgo de seguridad vigente.

---

## 7. Plan de trabajo

1. **Golden master de las 82 rutinas.** Capturar entradas y salidas reales contra la
   copia de preproducción. Prioridad a las 11 de BIRT y a las 10 de identificadores.
2. **Inventario de claves con formato.** Confirmar si `f_next_nro_receta_pac`,
   `f_get_nro_orden` y `COD_GS1_PERSONAL` tienen semántica incrustada como
   `ID_INTERNACION`. Determina si el destino puede usar identificadores opacos.
3. **Las 100 secuencias.** Script de creación y posicionamiento, más verificación de
   que ninguna quedó por debajo del máximo real de su tabla.
4. **Las 11 tablas contador.** Decisión caso por caso entre secuencia y contador por
   clave de negocio.
5. **Capa de compatibilidad de 11 funciones** para BIRT, validada contra el golden
   master.
6. **Biblioteca Java** de códigos de barra, fechas y validaciones.
7. **Verificar los consumidores de borrado masivo** y definir su reemplazo.

Los puntos 3 a 6 son independientes entre sí y se pueden encarar en paralelo.

**Estado Hospital-Api (2026-08-14):** V10 (~120 sequences) + V13 (`sec_id_tabla` 348 +
`sec_id_internacion` + `InternacionIdService`) + libs edades/AFIP/Interleaved. Pendiente:
cablear generadores de entidad y `setval`/`UPDATE prox_id` en corte.
Ver [`sdd/spike-next-id-tabla/`](../spikes/spike-next-id-tabla/).

---

## 8. Riesgos

| Riesgo | Impacto | Mitigación |
|---|---|---|
| Secuencia posicionada por debajo del máximo real | Alto: colisiones de clave en producción | Verificar `setval` contra `MAX(pk)` de cada tabla, no contra el `prox_id` del contador |
| Perder el formato de `ID_INTERNACION` | Alto: rompe códigos de barra, etiquetas y consultas que usan `substr` | Decidir explícitamente en el punto 2 del plan |
| Reproducir el `MAX + 1` sin lock | Medio: arrastra un defecto conocido | Secuencias o `ON CONFLICT ... RETURNING` |
| Diferencias en el cálculo de edad | Medio: afecta informes clínicos y facturación | Golden master sobre `f_get_edad_anio`, la función más usada de todo el package |
| Editar los 187 reportes por no tener capa de compatibilidad | Alto en esfuerzo | Portar primero las 11 funciones de BIRT |
| Trasladar el `FOR UPDATE` tal cual | Medio: mantiene la contención | Secuencias no transaccionales |
