# Reportes BIRT de otro cliente (N/A instalación GEA/base)

Pedido de producto **2026-09-21**: el carril sidecar migra diseños **base**
(camino genérico / fábrica) y, si existieran, diseños **específicos GEA**.
`esClienteGEA()` cubre `GEA` / `NEUROS` / `OFTAL` / `SANATORIOCANADA` /
`SEMEGER`. **No hay** `.rptdesign` cuyo basename lleve `GEA` (0 archivos):
GEA usa los diseños base. Las variantes por sufijo de **otro** cliente
salen del universo a migrar (`N/A` en
[`regla-instalacion-referencia.md`](../canon/regla-instalacion-referencia.md)).

No borrar los `.rptdesign` del legado. No desregistrar el sidecar salvo
pedido: un corte previo pudo haber portado una variante (hoy:
`IndicacionesAmbA4UNIONPERSONAL`).

Criterio de corte = **basename** con token de cliente (no `_UNIFICADA`,
`TICKET`, `A4`, `A5`, `PREIMPRESO` sueltos: son formato de impresión).

## Cuentas (walk Hospital-Legacy, 408 archivos / 329 basename)

| Conjunto | Basename únicos | Archivos |
|----------|----------------:|---------:|
| Bruto legado | 329 | 408 |
| **N/A otro cliente** | **32** | **53** |
| **Universo a migrar (base + GEA)** | **297** | **355** |
| HOSPITAL_2 base (SoT de corte) | 287 | 287 |
| HOSPITAL_2 otro cliente | 32 | 32 |

Baja: **−32 basename (−10 %)** respecto de 329; **−53 archivos (−13 %)**
respecto de 408. En HOSPITAL_2: **319 → 287** (−32).

`EpicrisisGYE` **no** está acá: GYE = Guardia y Emergencias (módulo), sigue
en el listado base. `EpicrisisGYEMATERDEI` sí (Materdei).

Listado operativo (sin estas filas):
[`listado-reportes-birt-legacy.md`](listado-reportes-birt-legacy.md).

## Por cliente

### UNIONPERSONAL (13) — esClienteUP (`UNIONPERSONAL`)

| reportId | Copias WAR | Sidecar |
|----------|------------|---------|
| `EstudioAmbA4UNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/EstudioAmbA4UNIONPERSONAL.rptdesign` | no |
| `EstudioIntA4UNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/EstudioIntA4UNIONPERSONAL.rptdesign` | no |
| `FacturaBPREIMPRESOUNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/FacturaBPREIMPRESOUNIONPERSONAL.rptdesign`<br>`WS-HOSPITAL/WebRoot/pages/reports/FacturaBPREIMPRESOUNIONPERSONAL.rptdesign` | no |
| `HistoriaClinicaUNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/HistoriaClinicaUNIONPERSONAL.rptdesign` | no |
| `IndicacionPacPISOUNIONPERSONALINDICACIONES` | `HOSPITAL_2/WebRoot/pages/reports/IndicacionPacPISOUNIONPERSONALINDICACIONES.rptdesign` | no |
| `IndicacionesAmbA4UNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/IndicacionesAmbA4UNIONPERSONAL.rptdesign` | sí (`IndicacionesAmbA4UNIONPERSONAL`, corte 2026-09-15) |
| `IndicacionesIntA4UNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/IndicacionesIntA4UNIONPERSONAL.rptdesign` | no |
| `OrdenServicioUNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/OrdenServicioUNIONPERSONAL.rptdesign` | no |
| `RecetaAmbA4UNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/RecetaAmbA4UNIONPERSONAL.rptdesign` | no |
| `RecetaIntA4UNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/RecetaIntA4UNIONPERSONAL.rptdesign` | no |
| `RecetaLibreIntUNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/RecetaLibreIntUNIONPERSONAL.rptdesign` | no |
| `RecetaLibreUNIONPERSONAL` | `HOSPITAL_2/WebRoot/pages/reports/RecetaLibreUNIONPERSONAL.rptdesign` | no |
| `informeLaboratorioUNIONPERSONAL` | `AGH/WebRoot/pages/reports/informeLaboratorioUNIONPERSONAL.rptdesign`<br>`AGP/WebRoot/pages/reports/informeLaboratorioUNIONPERSONAL.rptdesign`<br>`HOS-APP/WebRoot/pages/reports/informeLaboratorioUNIONPERSONAL.rptdesign`<br>`HOSPITAL_2/WebRoot/pages/reports/informeLaboratorioUNIONPERSONAL.rptdesign`<br>`WS-HOSPITAL/WebRoot/pages/reports/informeLaboratorioUNIONPERSONAL.rptdesign` | no |

### SANJUANDEDIOS (3) — esClienteSJDD (`SANJUANDEDIOS`)

| reportId | Copias WAR | Sidecar |
|----------|------------|---------|
| `ConfirmacionDatosPacienteSANJUANDEDIOS` | `HOS-APP/WebRoot/pages/reports/ConfirmacionDatosPacienteSANJUANDEDIOS.rptdesign`<br>`HOSPITAL_2/WebRoot/pages/reports/ConfirmacionDatosPacienteSANJUANDEDIOS.rptdesign` | no |
| `FacturaCPREIMPRESOSANJUANDEDIOS` | `HOSPITAL_2/WebRoot/pages/reports/FacturaCPREIMPRESOSANJUANDEDIOS.rptdesign`<br>`WS-HOSPITAL/WebRoot/pages/reports/FacturaCPREIMPRESOSANJUANDEDIOS.rptdesign` | no |
| `informeLaboratorioSANJUANDEDIOS` | `AGH/WebRoot/pages/reports/informeLaboratorioSANJUANDEDIOS.rptdesign`<br>`AGP/WebRoot/pages/reports/informeLaboratorioSANJUANDEDIOS.rptdesign`<br>`HOS-APP/WebRoot/pages/reports/informeLaboratorioSANJUANDEDIOS.rptdesign`<br>`HOSPITAL_2/WebRoot/pages/reports/informeLaboratorioSANJUANDEDIOS.rptdesign`<br>`WS-HOSPITAL/WebRoot/pages/reports/informeLaboratorioSANJUANDEDIOS.rptdesign` | no |

### MATERDEI (3) — esClienteMATERDEI

| reportId | Copias WAR | Sidecar |
|----------|------------|---------|
| `EpicrisisMATERDEI` | `AGP/WebRoot/pages/reports/EpicrisisMATERDEI.rptdesign`<br>`HOS-APP/WebRoot/pages/reports/EpicrisisMATERDEI.rptdesign`<br>`HOSPITAL_2/WebRoot/pages/reports/EpicrisisMATERDEI.rptdesign` | no |
| `EtiquetasAdmisionMATERDEI` | `HOSPITAL_2/WebRoot/pages/reports/EtiquetasAdmisionMATERDEI.rptdesign` | no |
| `TrenExtraccionMATERDEI` | `HOSPITAL_2/WebRoot/pages/reports/TrenExtraccionMATERDEI.rptdesign` | no |

### GYE+MATERDEI (1) — combinación GYE + Materdei

### ALPI (5) — esClienteALPI

| reportId | Copias WAR | Sidecar |
|----------|------------|---------|
| `IndicacionPacPISOALPI` | `HOSPITAL_2/WebRoot/pages/reports/IndicacionPacPISOALPI.rptdesign` | no |
| `PedInterconsultaALPI` | `HOSPITAL_2/WebRoot/pages/reports/PedInterconsultaALPI.rptdesign` | no |
| `RegistroAdmDirectaEnfALPI` | `HOSPITAL_2/WebRoot/pages/reports/RegistroAdmDirectaEnfALPI.rptdesign` | no |
| `RegistroAdmEnfALPI` | `HOSPITAL_2/WebRoot/pages/reports/RegistroAdmEnfALPI.rptdesign` | no |
| `RegistroCtrlEnfALPI` | `HOSPITAL_2/WebRoot/pages/reports/RegistroCtrlEnfALPI.rptdesign` | no |

### CEMIC (2) — esClienteCEMIC

| reportId | Copias WAR | Sidecar |
|----------|------------|---------|
| `EtiquetasAdmisionCEMIC` | `HOSPITAL_2/WebRoot/pages/reports/EtiquetasAdmisionCEMIC.rptdesign` | no |
| `ParteInsumosQuirurgicoCEMIC` | `HOSPITAL_2/WebRoot/pages/reports/ParteInsumosQuirurgicoCEMIC.rptdesign` | no |

### CPI (4) — esClienteCPI

| reportId | Copias WAR | Sidecar |
|----------|------------|---------|
| `AccionesInmediatasEstudiosA4CPI` | `HOSPITAL_2/WebRoot/pages/reports/AccionesInmediatasEstudiosA4CPI.rptdesign` | no |
| `AccionesInmediatasIndEnfA4CPI` | `HOSPITAL_2/WebRoot/pages/reports/AccionesInmediatasIndEnfA4CPI.rptdesign` | no |
| `ReciboCPI` | `HOSPITAL_2/WebRoot/pages/reports/ReciboCPI.rptdesign` | no |
| `informeLaboratorioCPI` | `AGH/WebRoot/pages/reports/informeLaboratorioCPI.rptdesign`<br>`AGP/WebRoot/pages/reports/informeLaboratorioCPI.rptdesign`<br>`HOS-APP/WebRoot/pages/reports/informeLaboratorioCPI.rptdesign`<br>`HOSPITAL_2/WebRoot/pages/reports/informeLaboratorioCPI.rptdesign`<br>`WS-HOSPITAL/WebRoot/pages/reports/informeLaboratorioCPI.rptdesign` | no |

### DUHAU (1) — esClienteDUHAU

| reportId | Copias WAR | Sidecar |
|----------|------------|---------|
| `informeLaboratorioDUHAU` | `AGH/WebRoot/pages/reports/informeLaboratorioDUHAU.rptdesign`<br>`AGP/WebRoot/pages/reports/informeLaboratorioDUHAU.rptdesign`<br>`HOS-APP/WebRoot/pages/reports/informeLaboratorioDUHAU.rptdesign`<br>`HOSPITAL_2/WebRoot/pages/reports/informeLaboratorioDUHAU.rptdesign`<br>`WS-HOSPITAL/WebRoot/pages/reports/informeLaboratorioDUHAU.rptdesign` | no |

## Qué no entra acá

- `EpicrisisGYE`: módulo Guardia y Emergencias, no `esCliente*`; sigue en el
  listado base. `EpicrisisGYEMATERDEI` es de Materdei y sí está arriba.
- `*_UNIFICADA`, `*TICKET`, `*A4`, `*A5`, `*PREIMPRESO` sin sufijo de
  cliente: formato de impresora, siguen en el listado base.
- Ramas `esClienteX()` **dentro** de un diseño base (logo MATERDEI,
  visibilidad CEMIC, viewer GEA vs printer): se portan con el diseño
  base; no son un `reportId` extra.
