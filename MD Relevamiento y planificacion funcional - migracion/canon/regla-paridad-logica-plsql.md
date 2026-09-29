---
title: Regla — paridad de lógica PL/SQL → servicio (golden master o diferido)
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.regla-paridad-logica-plsql
---

# Paridad de lógica — el cálculo se compara, no se lee

Capas 3–4 de [`gobierno-migracion.md`](gobierno-migracion.md).
Es la contraparte de [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md): esa regla
gobierna lo que el usuario **ve**; esta gobierna lo que el sistema **calcula**.
Método y evidencia acumulada: [`dossier-migracion.md`](../arquitectura/dossier-migracion.md) § 6.2 y
[`hallazgos-golden-master-vpn-2026-08-14.md`](../arquitectura/hallazgos-golden-master-vpn-2026-08-14.md).

**Problema que resuelve:** el programa tiene ~347.759 líneas de PL/SQL y **1.202 puntos
de invocación**. El dossier ya afirma que «migrado implica golden master verde o waiver
documentado», pero eso no era una compuerta: el ledger de
[`regla-evidencia-ejecutable.md`](regla-evidencia-ejecutable.md) no tenía clase para el
cálculo, el linter no lo pedía y el loop no lo nombraba. Resultado: una función podía
declararse portada porque **compila y se lee parecida**. Un `verify` puede estar en PASS
con un redondeo distinto adentro, y eso en facturación o en dosis no es un detalle.

## Principio

> Portar una rutina no es traducirla: es **demostrar con casos que devuelve lo mismo**.
> Sin comparación contra el oráculo no hay port, hay una reimplementación optimista.

El oráculo es Oracle **11.2** (copia diaria de producción), capturado por JDBC. El código
nuevo **no** apunta al 11.2: se captura una vez, se persiste el fixture y se replica
contra PostgreSQL. Método probado: los spikes de
[`edad`](../spikes/spike-golden-master-edad/README.md), `edad-string`, `get-edad`, `interleaved`,
`ean13` y `next-id-tabla`, con nueve tests `*GoldenMasterTest` vivos en `Hospital-Api`
(incluidos los motores de agenda de Turnos).

## Tres decisiones válidas por firma

Toda firma PL/SQL que el corte toca termina en **una** de estas tres, declarada en el `spec.md`:

| Decisión | Cuándo | Qué exige |
|----------|--------|-----------|
| **Portar** | El caso de uso la necesita y es portable | Golden master verde en el ledger |
| **Puente** | Spike lo exige y el port no entra en el corte | Registro en [`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md); no es arquitectura estable ni parte del UAT aislado |
| **Rediseñar** | El legacy tiene un bug o una regla que producto decide cambiar | Decisión **firmada por producto** en el `spec.md`; no la toma quien migra |

No es válida una cuarta: «traducida y compila». Sin ninguna de las tres, la firma es un
`diferido(slug)` declarado **al abrir el corte**, no al cerrarlo.

## Las rarezas del legacy se preservan

Un comportamiento raro **no es un bug a arreglar**: es la paridad. Corregirlo en silencio
rompe reportes, integraciones y la expectativa del usuario. Casos reales ya capturados:

| Caso | Oracle hace | Y así se replica |
|------|-------------|------------------|
| `f_get_edad_string` a 13 meses | `"1 años"` (plural incorrecto) | Se replica el plural incorrecto |
| Exactamente 12 meses | `"1 año"` | — |
| Días | siempre `"dias"`, nunca singular | — |
| `p_get_edad` + 29-feb | `ADD_MONTHS` ancla en el **último día** del mes destino | No usar aritmética de fechas «natural» |
| Interleaved 2/5 | bytes WE8 (`CHR(159)`) | Comparar **HEX/bytes**, no `String` Unicode |
| `f_get_cod_barra` sin empresa | `ORA-20001` CUIT inválido | El error también es contrato |

Si producto quiere corregir la rareza, es la decisión **Rediseñar**: con firma y en el `spec.md`.

## Trampas Oracle → PostgreSQL que hay que probar con casos, no razonar

Estas diferencias no las detecta la lectura del código ni el compilador. Cada una que el
cálculo pueda tocar necesita **su caso** en el fixture:

| Diferencia | Riesgo concreto |
|------------|-----------------|
| `''` **es** `NULL` en Oracle, no en PG | Concatenaciones y `IS NULL` cambian de resultado |
| Concatenación con `NULL` | Oracle ignora el `NULL`; en PG anula toda la expresión |
| `ROUND` en `.5` | Medio arriba vs. redondeo del tipo destino: aparece en importes |
| `NUMBER` sin escala → `numeric` | Precisión y truncado distintos ([`catalogo-tipos-oracle-pg.md`](../relevamiento/catalogo-tipos-oracle-pg.md)) |
| `MONTHS_BETWEEN` / `ADD_MONTHS` | Anclaje de fin de mes y fracciones de mes |
| `TRUNC(date)` y `SYSDATE` | Zona horaria y reloj: el fixture guarda `capturedAtOracleSysdate` y el test **congela** el `Clock` |
| Comparación de `CHAR` | Oracle rellena con espacios: `'A' = 'A '` puede diferir |
| Orden alfabético (collation) | Acentos, `ñ` y mayúsculas ordenan distinto: cambia el **orden de una grilla** y el primer registro que toma un `ROWNUM` |
| `ROWNUM` → `LIMIT` | Sin `ORDER BY` determinístico, «el primero» no es el mismo |
| División por cero | Oracle `ORA-01476` vs. error PG: el mensaje puede ser contrato |
| `to_char` / `to_date` con formato implícito | Depende de `NLS`: fechas y decimales cambian según sesión |
| Secuencias | Huecos y valores iniciales: ver `spike-next-id-tabla` |

## Qué exige el gate

En el `verify-report.md`, por cada firma con decisión **Portar**, una fila de ledger de
clase **golden master**:

| Campo | Contenido |
|-------|-----------|
| Firma | `PKG.f_nombre` con el esquema real |
| Casos | cuántos, y **de dónde** salieron: captura del oráculo o sintéticos |
| Bordes | qué trampas de la tabla anterior cubren (al menos `NULL`, vacío, cero y borde de fecha si aplica) |
| Replay | comando y clase del test que compara |
| Resultado | PASS con el conteo, o los diffs si hay FAIL |

Sin esa fila, la firma **no está portada**: es silencio, y el silencio ya bloquea el gate
([`proceso-sdd-paridad-completa.md`](proceso-sdd-paridad-completa.md) § verify).

## Reglas de la captura

1. **No mutar el oráculo.** Nada de `nextval`, `insert` ni `update` en la copia: se lee.
2. **Sin PII en los fixtures.** Casos sintéticos o valores no identificables; los fixtures
   se versionan y el repo no es lugar para datos de pacientes.
3. **Fixture antes que memoria.** La captura se persiste; una tanda que no dejó fixture
   no se puede replicar y por lo tanto no es evidencia.
4. **Re-capturar en el corte.** Los seeds de secuencias y contadores cambian: el snapshot
   sirve para diseñar, no para el cutover.
5. **Credenciales fuera del repo.** La conexión al oráculo vive en un `.env` ignorado.

## No hacer

- Declarar una firma portada porque el test unitario propio pasa: el test propio prueba
  lo que el autor entendió, el golden master prueba lo que el legacy hace.
- Usar un único caso «feliz» como golden master.
- Corregir el plural, el redondeo o el mensaje de error del legacy sin firma de producto.
- Inventar wrappers en el package para tapar una función fantasma: si un `.rptdesign`
  llama algo que no existe (`ORA-00904`), se corrige el reporte.
- Dejar el puente JDBC sin registrar en [`pendientes-solo-oracle.md`](../estado/pendientes-solo-oracle.md).
