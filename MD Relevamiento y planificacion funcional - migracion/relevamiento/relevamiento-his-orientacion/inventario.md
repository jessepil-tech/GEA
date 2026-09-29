# Inventario — orientación

## Cómo completar O1 (dump)

```text
TS.MENU_APLICACION
  ID, DESCRIPCION, ID_PADRE, NRO_ORDEN, ACCION, ID_APLICACION (= HOSPITAL)
        filtrado por MENU_PERFIL_ACCESO / PERSONAL_PERFIL_ACCESO
```

- `ACCION` null → carpeta; path `/pages/…` → hoja; `-` → separador.
- **Padre de menú ≠ path del xhtml.** Ejemplo: `habTurnosServCentro.xhtml` está en
  `pages/configuracion/servicio/`; en el menú de origin vive bajo **TURNOS / Dominios**.

Oracle O1 (2026-08-31): **37** tiles SP `f_get_menu_acceso(2, NULL, origin)` — coincide
Playwright. Claves: `ATENCION_TURNOS` (PNG TURNOS), `ADMINISTRACION_GENERAL_NA`
(PNG ADMINISTRACION GENERAL = config), `DEPOSITO` (farmacia). Árbol:
[dump-menu-arbol-origin.md](dump-menu-arbol-origin.md).

Playwright (perfil origin, 2026-08-31): misma grilla; menú Turnos extraído de `#menuform`.

## Desvíos Hospital-Web (git `dev/dev`)

| Hecho Web | Efecto vs origin |
|-----------|------------------|
| `buildHospitalModuleTiles`: `primaryRoute` = primer hijo enabled | Tile RECEPCIÓN → `/recepcion/cola`; CONFIGURACIÓN → Convenios |
| Hab serv/prof bajo grupo CONFIGURACION | Barrio distinto al menú TURNOS |
| TURNOS children = solo `/turnos/inicio` | No se ven Agenda / Consultas / Dominios |
| AGI + ANUNCIADOR como módulos del catálogo | En origin no están en la grilla HIS |
| Hojas `icon: 'layers'` | Verona: hoja = texto |
| `menuShowAll: true` en appsettings | Home ≠ recorte de perfil |

Aciertos a conservar: grilla PNG + pending; T1 call center; hab con labels `msg.*`;
Showcase off.

## 37 módulos (grilla origin)

ACREDITACIÓN DE PROFESIONALES · ADMINISTRACION GENERAL · ADMISION INTERNADOS ·
ANALISIS DE DEBITOS · AREA ORGANIZACIONAL · ATENCION MEDICA · ATENCION MEDICA
DOMICILIARIA · CAJA · CENTRO DE PROCEDIMIENTOS · COBRANZA CONVENIOS · COMPRAS ·
CONTROL DE INFECCIONES · DEMANDA ESPONTANEA · DEPÓSITO · DIAGNOSTICO POR IMAGENES ·
ENFERMERIA AMBULATORIA · ENFERMERIA INTERNADOS · ESTADISTICAS · FACTURACION AMBULATORIA
· FACTURACION INTERNADO · GUARDIA Y EMERGENCIAS · HISTORIA CLINICA · HOSPITAL DE DIA ·
HOUSEKEEPING · INCIDENTES · INFORMES · INTERNACION · LABORATORIO · LIQUIDACION DE
HONORARIOS · NUTRICION · OFTALMOLOGIA · OTROS ESTUDIOS · PANEL DE CONTROL · RECEPCIÓN ·
SERVICIO · TERAPIA FISICA · TURNOS
