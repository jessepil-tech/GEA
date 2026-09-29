---
title: Regla — no funcionales en el gate (tiempo, volumen, concurrencia)
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.regla-no-funcionales-migracion
---

# No funcionales — lo que la base vacía y el usuario único esconden

Capas 3–4 de [`gobierno-migracion.md`](gobierno-migracion.md).
Complementa [`regla-paridad-logica-plsql.md`](regla-paridad-logica-plsql.md): el golden
master prueba que el cálculo **da lo mismo**; esta regla prueba que el sistema **sirve**
cuando hay datos y hay gente.

**Problema que resuelve:** todo el gate actual es funcional y se ejecuta en el mejor
escenario imaginable: **base casi vacía y un solo actor**. En esas condiciones cualquier
consulta es rápida y ninguna condición de carrera aparece. Un corte puede cerrar en PASS
y romperse el primer día con datos reales. Dos datos del propio legacy alcanzan para
mostrar que el riesgo no es teórico:

- El PL/SQL legacy usa **`FOR UPDATE` en 317 lugares**: hay bloqueo pesimista deliberado
  sobre recursos que se disputan. Portar la lógica sin portar el bloqueo no cambia el
  resultado de un golden master —que corre en un solo hilo— y sí produce doble turno,
  doble entrega de stock o numeración repetida.
- Hay tablas de operación con cientos de miles de filas. Una grilla que trae todo y
  pagina en memoria se comporta perfecto contra el fixture y colapsa contra el histórico.

## Principio

> El presupuesto no funcional se **declara al abrir** el corte y se **mide al cerrarlo**.
> Medido en vacío y con un solo actor, un PASS no dice nada sobre producción.

La referencia es el legacy: la migración puede empeorar la experiencia solo con una
decisión explícita y firmada, nunca por omisión.

## Los tres ejes

### 1. Tiempo de respuesta

Se declara en el paso 3 del [loop](loop-migracion-corte.md) (universo firmado) y se mide
en el paso 6 (evidencia). Defaults orientativos, **revisables por producto** por corte:

| Operación | Presupuesto default |
|-----------|---------------------|
| Grilla / consulta interactiva | p95 ≤ 2 s con volumen realista |
| Acción de escritura (otorgar turno, registrar llegada) | p95 ≤ 1,5 s |
| Reporte BIRT | no peor que el legacy en la misma operación |

Lo obligatorio no es el número: es **declarar el presupuesto y medirlo**. Un corte que no
declara presupuesto no puede afirmar nada sobre su desempeño.

### 2. Volumen

El volumen real se consulta, no se estima:
[`tools/consultas-relevamiento.sql`](../../tools/consultas-relevamiento.sql) § 4, contra la
copia Oracle (copia fiel de producción declarada por el cliente).

Una medición sobre el fixture de tres filas no es una medición. El dataset de la prueba
tiene que estar en el **orden de magnitud** del legacy para la tabla que domina la
consulta. Si no hay volumen disponible, se dice así: `medido en vacío` y
`diferido(perf-volumen)`. Eso es honesto; llamarlo PASS no lo es.

Qué mirar además del reloj: si la consulta trae todo y filtra o pagina **en memoria**, el
tiempo con fixture no predice nada. Eso se detecta leyendo la consulta, y es un hallazgo
que se declara aunque el cronómetro haya dado bien.

### 3. Concurrencia y transaccionalidad

Por cada recurso que el corte disputa, el `spec.md` declara la estrategia y el `verify`
la prueba con **dos actores simultáneos**:

| Recurso típico | Qué no puede pasar |
|----------------|--------------------|
| Turno en un horario | Dos pacientes con el mismo cupo |
| Cama / ambiente | Dos internaciones en el mismo lugar |
| Número de comprobante o ID de negocio | Numeración repetida o con hueco no previsto |
| Stock de un ítem | Entregar dos veces lo mismo |

Estrategias válidas: restricción de unicidad en la base, bloqueo optimista con versión,
`SELECT … FOR UPDATE` equivalente al del legacy, o *advisory lock*. La estrategia se
nombra; «el servicio es transaccional» no es una estrategia.

Regla de correspondencia: **si el legacy usaba `FOR UPDATE` sobre ese recurso, el corte no
puede declarar la concurrencia como «no aplica»**. O replica el control, o declara
`diferido(slug)` con el riesgo escrito.

Transaccionalidad: un caso de uso que escribe en más de una tabla evidencia el **rollback**
—forzar el fallo intermedio y mostrar que no quedaron filas huérfanas—, no solo el camino
feliz.

## Qué exige el gate

Fila de ledger de clase **no funcional** en el `verify-report.md`:

| Campo | Contenido |
|-------|-----------|
| Operación | Qué se midió, con qué comando o trace |
| Presupuesto | El declarado, y quién lo aprobó si no es el default |
| Medición | Valor observado y percentil, no «rápido» |
| Volumen | Filas de la tabla dominante durante la medición |
| Concurrencia | Recurso, estrategia y resultado de la prueba de dos actores |

## No hacer

- Llamar performance a un cronómetro en localhost contra una base vacía.
- Reportar un promedio en lugar de un percentil: el promedio esconde justo el caso que
  el usuario recuerda.
- Declarar «no aplica» la concurrencia sin haber mirado si el legacy bloqueaba.
- Corregir un problema de desempeño cambiando el comportamiento funcional (traer menos
  datos, cambiar el orden, recortar el rango por defecto) sin pasar por
  [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md): eso es un gap de paridad,
  no una optimización.
- Dejar el hallazgo de desempeño como comentario en el PR: si no está en el ledger, no existe.
