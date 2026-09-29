# Hallazgos en reportes BIRT (migrados o en corte)

Cola corta de **problemas observados** (dato, layout, contrato) que no se
corrigen en el corte salvo pedido. No reemplaza el listado `[x]` ni los
huérfanos / otro cliente.

Al detectar uno: **una fila**, hallazgo mínimo, sin rediseñar acá.

| reportId | Fecha | Hallazgo |
|----------|-------|----------|
| `DetLibroInternacion` | 2026-09-23 | Encabezado Mes/Año sale de `NRO_MES_LIBRO_INT` / `ANO_LIBRO_INT` (bean `mes`/`ano` del libro). HOSPROD libro `10` centro `654`: mes null→**0**, año **12082**. Las internaciones son mayo **2021**. El sidecar replica el HIS; el año del PDF no es calendario. |
| `devolucionInsumos` | 2026-09-23 | Dataset `QUIROFANO` del SoT lee `ts.evento_quirofano` (no existe en HOSPROD ni PG). El HIS persiste ocupación en `ts.ocupacion_quirofano`. El port usa esa tabla con alias `fecha_hora_ing/egr` → `fecha_hora_ini/fin` para que el dataset no tumbe el PDF. |
