---
title: Regla — migración incremental de reportes BIRT
status: canonical
owner: grupogea
last_updated: 2026-08-28
phase_id: sdd.hospital.regla-migracion-reportes-birt
indice_blurb: Migración incremental de reportes BIRT (R5+)
---
# Regla — migración incremental de reportes BIRT

`phase_id:` **`sdd.hospital.regla-migracion-reportes-birt`**  
Fecha: **2026-08-28**  
**Estado:** **CANÓNICA** (frente R5+; cientos de `.rptdesign`)

Complementa:

- [`register-report-sidecar.md`](../arquitectura/register-report-sidecar.md) — alta mecánica en el sidecar
- [`regla-birt-columnas-minusculas.md`](regla-birt-columnas-minusculas.md) — bindings PG
- [`playbook-migracion-reporte-birt.md`](../arquitectura/playbook-migracion-reporte-birt.md) — checklist + bitácora de pitfalls (viva)

---

## Principio

Migrar reportes es **un corte por `reportId`**, iterativo e incremental.  
No se “limpia” un diseño inventando layout: la fuente de verdad visual/estructural es el **`.rptdesign` legacy elegido** (+ PDF de referencia del mismo reporte).

| Pregunta | Respuesta por defecto |
|----------|------------------------|
| ¿Cuántos reportes por slice? | **Uno** (`reportId` = basename del `.rptdesign`) |
| ¿Cuál legacy copiar? | El que usa el Java/callers vigentes; no el archivo “más grande” ni una variante de **otro cliente** (UNIONPERSONAL / SANJUANDEDIOS / MATERDEI / ALPI / CEMIC / CPI / DUHAU) |
| ¿Portamos `*UNIONPERSONAL*` / `*SANJUANDEDIOS*` / …? | **No** (`N/A` otro cliente). Universo = diseños **base** + GEA. GEA no tiene `.rptdesign` propio. Inventario: [`listado-reportes-birt-otro-cliente.md`](../relevamiento/listado-reportes-birt-otro-cliente.md). `EpicrisisGYE` sí (módulo guardia, no cliente). |
| ¿Podemos omitir una sección que legacy muestra? | No → implementar, o diferir con evidencia; ver waiver |
| ¿Mocks de packages/callables? | **Prohibido**. Seed de datos OK; callable faltante = fallo (o `callables.check=warn` solo en debug local) |

---

## Restricciones de producto (no negociables)

1. **Paridad con el legacy elegido**, no con “lo que quedó lindo en el PoC”.
2. **Un ejemplo HTTP** por reporte: `Hospital-Reports/examples/api-requests/<reportId>.json`.
3. **Params** = inventario completo de lo que legacy envía en **todos** los
   call sites (unión de `parameters.put` + `ReportManager`); **ninguno afuera,
   ninguno inventado**. Relevamiento multi-sitio + **nullabilidad por sitio**
   (un param puede ser `null` en un camino y valor en otro) — el SQL/diseño
   debe cubrir esos casos; el JSON canónico documenta un camino concreto.
   No confundir con credenciales JDBC (`DB_USER` ≠ usuario de impresión).
   **`urlLogo`:** `ReportManager.printReport` **siempre** inyecta el param.
   El JSON lo manda si el layout es imagen **file** (`params["urlLogo"]`) —
   código cliente HIS (`SDLC_VM` en Cañada). El default sidecar `GEA` **no**
   es paridad. Omitir solo si el logo sale de **BLOB SQL** o el layout no
   usa esa imagen. Que el bean no haga `put` **no** autoriza omitir en el
   caso file. Detalle: playbook §2.
4. **SQL packages** = ports reales **solo** desde `Legacy-DB/Packages` → `sql/packages-pg/`;
   **prohibido** `Hospital-Legacy/RDBMS` como SoT; documentar gaps en checklist, no rellenar con literales.
   Tablas de dominio en el port: **`ts.<tabla>`**. El schema del callable
   (`personas`, `historia_clinica`, …) **no** es el de las tablas.
   Prohibido `FROM persona` o `search_path`/`currentSchema` como sustituto.
   **Forma del `queryText`:** si legacy es `{call …}`, el migrado mantiene callable
   (lógica en `packages-pg`); **prohibido** embeber un `SELECT` nuevo en el diseño
   que no existía en el `.rptdesign` legacy.
   **Excepción BIRT+JDBC PG (OUT cursor):** si `SPSelectDataSet`/`{call}` no
   entrega ResultSet, admitir `SELECT * FROM schema.func(...)` con `func` =
   port en `packages-pg` (`RETURNS SETOF`/`TABLE`). Ver playbook +
   `birt-design-pg-pitfalls.mdc`.
5. **DDL** = schema migrado **`ts`**. Datasets BIRT califican `ts.<tabla>`.
   **Prohibido** tratar `public` + `search_path` como canónico de dominio
   (PuC histórico; no reabrir).
   Reports porta contra una PG **con TS completo**; su smoke **no** cubre el piloto Api/Web (Flyway **por cortes**).
   **Reports** publica la lista `ts.*` del print-path (contrato).
   **El corte que cablea** el botón cruza esa lista contra **su** dump/Flyway. Tabla Oracle ausente acá → Flyway 1:1 en ese corte o `diferido(slug)`. Sidecar `[x]` / grilla con filas **no** prueban el print-path en el piloto.

---

## Ritmo incremental (por reporte)

```
identificar legacy → copiar diseño → Oracle→PG → registrar → packages/seeds → smoke PDF → anexar pitfalls
```

Detalle operativo: [`playbook-migracion-reporte-birt.md`](../arquitectura/playbook-migracion-reporte-birt.md).

---

## Anti-patrones (resumen)

| Anti-patrón | Hacer en cambio |
|-------------|-----------------|
| Mezclar `Epicrisis` + `EpicrisisGYE` / Materdei en un solo `reportId` | Un diseño = un id; GYE (guardia) es corte aparte; Materdei/`UNIONPERSONAL`/… son N/A otro cliente |
| Ocultar columna y dejar `colSpan` anclado ahí | Quitar columna (como base) o celda vacía + título en columnas visibles |
| `pageBreakBefore/After=avoid` “por las dudas” | Solo con evidencia; suele generar páginas en blanco |
| Visibilidad `flag=='N' \|\| rownum==null` sin chequear base | Contrastar el `.rptdesign` legacy; secciones vacías pueden requerir header sin dataset |
| Inventar bloques de firma / columnas | Copiar del legacy elegido |
| Variantes `*.imprimir.json`, `*.internacion-*.json` | Un solo `<reportId>.json` |
| Cablear BIRT porque sidecar `[x]` o la grilla lista filas | Reports fuma schema **completo**; Api/Web es Flyway por corte. Cruzar `ts.*` del reporte vs **esta** PG. PDF cabeceras vacías = FAIL del cable |

---

## Dónde vive el aprendizaje

Cada bug de layout/SQL/params **nuevo** se agrega al final de  
[`playbook-migracion-reporte-birt.md`](../arquitectura/playbook-migracion-reporte-birt.md) § Bitácora de pitfalls  
(fecha + `reportId` + síntoma + causa + fix).  
Las reglas Cursor en `Hospital-Reports/.cursor/rules/` resumen lo estable para el agente.
