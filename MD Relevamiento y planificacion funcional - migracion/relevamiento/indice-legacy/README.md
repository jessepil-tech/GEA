---
title: Índice del legacy HIS (generado)
version: 1.0.0
status: generated
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.indice-legacy
---

# Índice del legacy — candidatos para firmar el universo

**Generado** por [`tools/indice-legacy.sh`](../../../tools/indice-legacy.sh) el 2026-09-16.
No se edita a mano: se regenera.

Sirve al **paso 3** de [`loop-migracion-corte.md`](../loop-migracion-corte.md)
(firmar universo). Consulta de un corte:

```bash
./tools/indice-legacy.sh --semilla <basename o accion del menú>
```

> **Candidatos ≠ universo firmado.** Lo que obliga en el gate es lo que Clarify
> aceptó para *ese* corte. Este índice acota la búsqueda; no la decide.

## Fuentes

| Capa | Fuente | Estado |
|------|--------|--------|
| xhtml / includes / EL / BIRT | `Hospital-Legacy` | completa |
| Menú (`in_menu`) | `relevamiento-his-orientacion/dump-menu-origin.csv` | completa |
| Firmas PL/SQL | SoT `Legacy-DB/Packages`: no disponible en esta máquina | **parcial** — derivadas de literales Java |

Las firmas PL/SQL salen de literales en Java (ahí viven las llamadas), así que
son candidatas: se confirman contra el SoT de packages. `RDBMS/` del legacy
**no** es SoT ([`regla-ddl-postgres-migrado.md`](../regla-ddl-postgres-migrado.md)).

## Contenido

| Archivo | Filas | Qué contiene |
|---------|------:|--------------|
| `xhtml.tsv` | 3347 | pantalla, war, carpeta, basename, líneas, form, in_menu |
| `xhtml-include.tsv` | 4817 | pantalla → `ui:include` / template (el 1 hop) |
| `xhtml-bean.tsv` | — | pantalla → bean → propiedad o método EL |
| `xhtml-reporte.tsv` | — | pantalla → `.rptdesign` invocado |
| `java-package.tsv` | — | clase Java → firma `PKG.FN` + nivel (**A** convención `f_`/`p_`; **B** candidata por contexto SQL) |
| `java-reporte.tsv` | — | clase Java → `.rptdesign` invocado |
| `tronco.tsv` | — | objetos compartidos: entran solo por firma invocada |
| `job.tsv` | 82 | proyecto → job → punto de entrada → firmas → tablas (**capacidades sin pantalla**; consulta con `--jobs`) |

## Métricas del walk

| Métrica | Valor |
|---------|------:|
| xhtml | 3347 |
| xhtml en menú | 941 |
| java | 9462 |
| beans referenciados desde xhtml | 2460 |
| reportes invocados | 250 |
| firmas PL/SQL nivel A | 66 |
| firmas PL/SQL nivel B (a confirmar) | 6 |
| procesos programados (jobs) | 82 |

Contraste con el walk manual: [`relevamiento-his-inventario-global/inventario.md`](../relevamiento-his-inventario-global/inventario.md).

## Límites conocidos (leer antes de firmar un universo)

1. **Delegación a BUSINESS.** La firma PL/SQL se atribuye a la clase donde está el
   literal. Si el bean delega en `HOSPITAL-BUSINESS`, la firma aparece bajo la clase
   de BUSINESS y **no** bajo el bean: al firmar el universo hay que mirar también la
   capa BUSINESS del corte.
2. **Includes dinámicos.** 231 de 4817 includes no se resuelven a un path
   (src calculado en EL o cross-WAR): quedan con la 3.ª columna vacía y se revisan a mano.
3. **Nivel B es sospecha,** no hallazgo: se confirma contra el SoT de packages.
4. El índice ve **invocación**, no semántica: no dice si la capacidad es de este corte.
   Eso lo decide Clarify.
5. **El nombre de un job no dice todo lo que hace.** `MigrarTurnoVencidoJob` también
   purga la cola de espera y apaga el anunciador. `--jobs` busca por texto: encuentra
   por nombre, tabla o punto de entrada, no por efecto. El efecto real está relevado en
   [`relevamiento-procesos-programados/`](../relevamiento-procesos-programados/README.md).
6. **La frecuencia de los jobs no está en el código:** vive en `TS.TAREA_PROGRAMADA`,
   con un flag `ACTIVA` que decide si corren. El índice no puede saber cuáles están vivos.
