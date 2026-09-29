---
title: Regla — instalación de referencia (paridad ¿con cuál de los 16 clientes?)
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.regla-instalacion-referencia
---

# Instalación de referencia — «paridad con el legacy» no dice con cuál

Capas 3–4 de [`gobierno-migracion.md`](gobierno-migracion.md).
Precede a [`regla-paridad-ui-legacy.md`](regla-paridad-ui-legacy.md) y a
[`regla-paridad-logica-plsql.md`](regla-paridad-logica-plsql.md): antes de comparar contra el
legacy hay que decir **qué legacy**.

**Problema que resuelve:** el HIS es un producto multi-instalación con ramas de
comportamiento **en el código**. Hay **313 invocaciones** de `esClienteX()` sobre **16
clientes** distintos, más 44 `.xhtml` con ramas. Y no están en un rincón: hay ramas por
cliente en `BBRecepcionPaciente`, en `BBRecepcionarPaciente` de AGI y en `BBAgendaHosApp`, o
sea dentro de circuitos ya declarados cerrados.

## El agujero, que es peor que la existencia de las ramas

La rama se decide por el atributo `cliente` de **`acercade.xml`, un archivo empaquetado
dentro del WAR** (`XMLVersionParser.CLIENT_NAME`), comparado contra literales:

```java
public static boolean esClienteCEMIC() {
    return "CEMIC".equals(XMLVersionParser.getClientNameFullTrim());
}
```

Los **once** `acercade.xml` del repositorio dicen `cliente="TS"`, y **`TS` no coincide con
ningún helper** (`CEMIC`, `CPI`, `EMME`, `GEA`, `MATERDEI`, `OTAMENDI`, `UNC`,
`UNIONPERSONAL`, …). Consecuencia verificada:

> **Leyendo o ejecutando el legacy tal como está en el repositorio, las 313 ramas por
> cliente están apagadas.** Quien releva ve el camino genérico de fábrica, no el del
> hospital que se va a migrar.

Y la agrupación tampoco es uno a uno: `esClienteGEA()` es verdadero para **cinco** nombres
(`GEA`, `NEUROS`, `OFTAL`, `SANATORIOCANADA`, `SEMEGER`). Un helper, cinco instalaciones.

## Principio

> El corte declara **su instalación de referencia** en el `spec.md`. Sin ese dato, «igual al
> legacy» es una afirmación sin sujeto: hay dieciséis legacies.

## Qué exige el gate

En el `spec.md`, al firmar el universo (paso 3 del [loop](loop-migracion-corte.md)):

| Campo | Contenido |
|-------|-----------|
| Instalación de referencia | El valor de `cliente` del ambiente contra el que se releva (no `TS`) |
| Ramas detectadas | Las `esClienteX()` que toca el universo, con archivo y línea |
| Decisión por rama | **Se porta** (es la de la instalación de referencia) · **N/A** (es de otro cliente) · `diferido(multi-instalacion)` si la plataforma nueva va a servir a varias |

Comando para no buscarlas a mano:

```bash
grep -rn 'esCliente' <paths del universo>
```

Si el universo del corte **no** tiene ramas, se declara así: «sin ramas por instalación».
Eso es una afirmación verificable; el silencio no.

## La pregunta de producto que esto destapa

¿La plataforma nueva es **una** instalación o un producto multi-instalación como el legacy?
No es una decisión de quien migra, y cambia el diseño:

- **Una instalación:** las ramas de los otros quince clientes son código a **no** portar.
  Hay que decir cuál es la nuestra y descartar el resto con criterio, no por omisión.
- **Multi-instalación:** el `if` por nombre de cliente compilado en el artefacto **no se
  replica**: eso se resuelve con configuración por instalación, no con ramas en el código.

Hasta que producto firme la respuesta, cada corte declara su referencia y difiere lo demás.

**Reportes BIRT (2026-09-21):** el carril sidecar toma instalación **GEA**
(helper `esClienteGEA`, cinco nombres) sobre diseños **base**. Variantes
`.rptdesign` de UNIONPERSONAL / SANJUANDEDIOS / MATERDEI / ALPI / CEMIC /
CPI / DUHAU = `N/A` (otro cliente):
[`listado-reportes-birt-otro-cliente.md`](../relevamiento/listado-reportes-birt-otro-cliente.md).

## No hacer

- Afirmar paridad sin decir contra qué instalación se comparó.
- Relevar el comportamiento ejecutando el legacy con `cliente="TS"` y creer que se vio todo:
  se vio el camino genérico y **ninguna** rama.
- Portar una rama de otro cliente «por si acaso»: es comportamiento de otro hospital dentro
  del nuestro, y nadie lo va a poder explicar después.
- Replicar el `esClienteX()` en la plataforma nueva. Si hace falta variar por instalación,
  es configuración; el nombre del cliente no se compila en el artefacto.
- Borrar una rama sin registrarla. Se decide y se escribe: N/A también es una decisión.
