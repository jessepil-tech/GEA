---
title: Relevamiento — integraciones externas del HIS legacy
version: 1.0.0
status: canonical
owner: grupogea
last_updated: 2026-09-15
phase_id: sdd.hospital.relevamiento-integraciones-externas
---

# Integraciones externas — con qué habla el hospital hacia afuera

Capa 3b′ de [`gobierno-migracion.md`](../../canon/gobierno-migracion.md): mide el tamaño de un
frente que el relevamiento por menú **no puede ver**, porque no cuelga de ninguna hoja del
árbol de módulos.

**Qué es:** el mapa de los sistemas de terceros con los que el HIS intercambia datos, con
el esfuerzo real de cada frente separado del conteo bruto de clases.

**Qué no es:** no sustituye el relevamiento A–C del módulo que consume la integración
([`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md)), ni habilita a
implementar. Es descubrimiento: produce **candidatos y dimensión**, no universo firmado
([`loop-migracion-corte.md`](../../canon/loop-migracion-corte.md) § paso 3).

Detalle por integración, con endpoints y rutas: [`inventario.md`](inventario.md).

> **Alcance (2026-09-15):** de las cinco integraciones reales, el cliente dejó **dentro** solo
> `AFIP`; excluyó `ALFABETA`, `BIONEXO`, `VALIDADORES`, `RECETAS` y `WS-HOSPITAL`, y no
> clasificó `ANMAT` —que es trazabilidad regulatoria—
> ([`alcance-proyectos-migracion.md`](../../planificacion/alcance-proyectos-migracion.md)). Dos advertencias que
> ese recorte no resuelve: la elegibilidad de obras sociales se invoca **desde pantalla** en
> circuitos que sí se migran (consecuencia 2 del alcance), y las interfaces de laboratorio,
> autoanalizadores y PACS viven en `HOSPITAL-BUSINESS`, que está **dentro** de alcance.

## Tres correcciones al recorte de partida

El frente se venía nombrando como «siete proyectos de integración». No es así, y la
diferencia cambia cómo se planifica:

1. **`RECETAS` y `PROVEEDORES` no son integraciones.** Son dos portales extranet JSF para
   usuarios **humanos** externos: médicos prescriptores y proveedores. Tienen login propio,
   auto-registro y recuperación de contraseña (`loginPrescriptor.xhtml`, `registro.xhtml`,
   `loginProveedor.xhtml`). Son 159 clases propias entre los dos: el mayor volumen de
   código del recorte, y son **aplicaciones web de usuario final**, no interfaces máquina
   a máquina. Su migración se planifica como pantallas, con paridad UI.
2. **`VALIDADORES` no es una integración: son diecisiete.** El despachador
   `ValidadorWS.java` resuelve por `idValidadorOnline` en un `switch` de **29 `case`** que
   instancia **17 clientes distintos** (Conexia, Traditum, Sancor, ITC/Sitel, OSMECON,
   APROSS, Activia, HMS, Swiss Medical, Medicus, OSPJN, UTA y un facade propio). Cada obra
   social es un contrato, una homologación y un ciclo de vida separados. El riesgo no está
   en el volumen de código: está en la **coordinación con diecisiete contrapartes**.
3. **Las interfaces de laboratorio no viven en esos proyectos.** Nextlab, DNLab, Kern y
   Medibase están en `HOSPITAL-BUSINESS`; los jobs de `SCHEDULER` solo las disparan. Y son
   un frente distinto del de los **autoanalizadores** (protocolo ASTM sobre TCP y puerto
   serie), que a su vez es distinto del de **imagenología PACS/DICOM**.

## Las cinco integraciones reales

| Integración | Contraparte | Protocolo | Dominio | Criticidad |
|-------------|-------------|-----------|---------|-----------|
| AFIP (factura electrónica) | **Facade del proveedor**, no AFIP | SOAP Axis2 | Facturación | **Bloqueante**: sin CAE no hay factura válida |
| ANMAT / trazabilidad | Sistema Nacional de Trazabilidad (PAMI) | SOAP + WS-Security | Farmacia | Alta, tolera informe diferido |
| VALIDADORES (×17) | Obras sociales | SOAP · REST · POST form | Admisión y facturación | Bloqueante **por convenio**, no global |
| BIONEXO | Marketplace de compras | SOAP con XML embebido | Compras | Baja: hay circuito manual |
| ALFABETA | Vademécum comercial | SOAP (ZIP) + archivos planos | Farmacia | Baja: desactualiza precios |

## Esfuerzo real vs. conteo bruto

El conteo de clases engaña porque la mayoría son *stubs* generados por herramienta
(`wsdl2java`, `xjc`, Axis 1), que se regeneran desde el WSDL en minutos:

| Proyecto | Clases | Generadas | Propias | Núcleo real |
|----------|-------:|----------:|--------:|------------:|
| `VALIDADORES` | 311 | 221 | 90 | **17** (16 clientes + despachador) |
| `ANMAT` | 65 | 41 | 24 | **7** (9 de las propias son scheduler duplicado) |
| `AFIP` | 36 | 33 | 3 | **1** |
| `BIONEXO` | 23 | 19 | 4 | **2** |
| `ALFABETA` | 14 | 10 | 4 | **2** |

`AFIP` es el caso extremo: 36 clases, **una** con lógica. Contarlo como 36 sobredimensiona
el frente en un orden de magnitud. `VALIDADORES` es el inverso: el código se reduce a 17
clases, pero multiplicado por diecisiete negociaciones externas.

## Hallazgos que cambian decisiones

1. **AFIP no habla con AFIP.** El proyecto invoca un facade Axis2 del proveedor del HIS
   (`facade.thinksoft.com.ar`), que es quien resuelve WSFEv1. No hay **ningún** rastro de
   WSAA, certificados `.p12` ni ticket de acceso en `AFIP/src` (verificado: cero archivos).
   Si la plataforma nueva va a facturar **directo** contra AFIP, ese trabajo —autenticación
   WSAA, gestión de certificados, homologación— **no está relevado ni estimado en ningún
   lado**. Si va a seguir usando el facade, hay que confirmar que el proveedor lo mantiene.
2. **El facade recibe la contraseña de la base del HIS en cada request.** El cliente envía
   la credencial y la cadena de conexión Oracle como parámetros de autenticación. Es un
   acoplamiento que la plataforma nueva **no debe replicar**.
3. **Credencial embebida en el código.** `ImpBusAlfabeta.java:357` fija la clave del
   servicio en el fuente. Rotarla y externalizarla; el resto de las integraciones ya lee
   sus credenciales de la tabla de parámetros.
4. **El job de Alfabeta descarga y no procesa.** En `ImpBusAlfabeta` la llamada a
   `processFile` está comentada bajo un `//TODO procesar el archivo` (línea 376): el job
   escribe el ZIP en un temporal y termina. La actualización real del vademécum es
   **manual**, por `alfaBetaUploadPage.xhtml`. Migrar el job tal cual sería portar código
   muerto y creer que el vademécum se actualiza solo.
5. **Ruta de `truststore` de Windows fija en el código** en el envío de recetas a farmacias
   externas: rompe en cualquier entorno que no sea Windows.
6. **Hay código desactivado que parece vivo:** el procesamiento de proveedores de Bionexo y
   bloques de validadores ITC están comentados. Antes de portar, confirmar qué está en uso.

## Dos frentes que este relevamiento descubre y no cubre

Ambos son de laboratorio e imagenología, y ninguno estaba nombrado en el programa:

- **Autoanalizadores (ASTM).** Integración con el instrumental del laboratorio por TCP y
  puerto serie, con servidores propios en `SCHEDULER` para que los equipos empujen
  resultados. Es la integración **más crítica** del conjunto: si cae, los resultados se
  cargan a mano. Necesita su propio relevamiento.
- **Imagenología (PACS/DICOM).** Usa `dcm4che`, con `ImpBusInterfaceFuji` de ~3.300 líneas
  y endpoints para Philips y VisualMedica. Frente considerable y separado.

## Lo que no se pudo determinar

- **Frecuencia de cada job.** No hay cron en el código: los triggers se arman desde la
  tabla `tarea_programada` de Oracle. Sin acceso a una instancia no se sabe cada cuánto
  corre cada integración **ni cuáles están habilitadas en producción**.
- **Qué obra social es cada `idValidadorOnline`.** Solo 11 de los `case` tienen comentario;
  el resto exige leer `validador_online_df`.
- **Qué integraciones están vivas hoy.** Distinguir «código muerto» de «desactivado por
  configuración» requiere datos o logs de producción.
- **Volumen transaccional** de cada interfaz: nada en el código lo indica.
- **Contrato del facade AFIP** y de la API de recetas: ambas especificaciones están fuera
  de este repositorio.

## Próximo paso

Estas preguntas se responden con **una** consulta a la copia Oracle —que el cliente declaró
copia fiel de producción— y no leyendo más código:
[`tools/consultas-relevamiento.sql`](../../../tools/consultas-relevamiento.sql) §§ 1–2. Hasta
entonces, ninguna integración entra a un corte: sin saber si está habilitada y con qué
frecuencia corre, no se puede firmar su universo.
