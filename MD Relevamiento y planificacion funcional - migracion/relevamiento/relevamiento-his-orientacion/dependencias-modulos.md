# Dependencias entre módulos HIS (roadmap)

`phase_id:` **`sdd.hospital.dependencias-modulos`**  
Fecha: **2026-09-07**  
Padre: [`README.md`](README.md) · Cobertura: [`cobertura.md`](cobertura.md)

**Qué es:** mapa **liviano** para ordenar el proyecto. Una fila por tile (37 origin +
2 admin + satélites) con circuito, dependencias **conocidas** y nivel de confianza.

**Qué no es:** 37 relevamientos A–C. Un dump de menú no cierra maestros ni ABM
([`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md)).
Tampoco es el grafo de packages PL/SQL (`GENERAL` ↔ ciclo de 29 packages).

Confianza:

| Código | Significa |
|--------|-----------|
| **A–C** | Hay carpeta `relevamiento-<modulo>/` |
| **slice** | Hay CU/SDD parcial, no el módulo |
| **inferido** | Menú O1 + gate xhtml + dossier; **puede cambiar** al hacer A–C |
| **hueco** | No hay evidencia suficiente para priorizar por dependencia |

**Nombres:** la grilla origin usa el PNG (`TURNOS`, `DEPÓSITO`). `MENU_APLICACION`
puede usar otra clave (`ATENCION_TURNOS`, `DEPOSITO`). En la tabla: **label origin**
y clave entre paréntesis.

**Avance (2026-09-07):** PG `ts` = SoT destino; E2E con filas nacidas de CUs;
copia Oracle = bootstrap de padres, no ABM. Canon: [`criterio-avance-e2e-datos.md`](../../canon/criterio-avance-e2e-datos.md).

---

## Cómo usarlo en el roadmap

1. Elegir **circuito** (no tile suelto).
2. Dentro del circuito, respetar el tronco compartido (Identity, `ts.centro_atencion`,
   personal, convenios) **sin** esperar ADMINISTRACION_GENERAL entero.
3. Colgar cada CU en su padre `MENU_APLICACION`; hermanos = disabled.
4. `backlog-orden-*` elige la fila de **esta semana**; este mapa es el tablero
   de **negocio** (tiles / circuitos).
5. Capacidad de ingeniería (16 streams, BODY, paralelo, A–C):
   [`relevamiento-his-inventario-global/arbol-dependencias.md`](../relevamiento-his-inventario-global/arbol-dependencias.md)
   § Tablero — no sustituye este mapa ni el A–C.

Orden típico de circuitos (el **siguiente** trabajo no es un tile nuevo):

```text
Plataforma (Identity, ts, Reports)
    → Ambulatorio YA EN CÓDIGO: cobrar escrituras (T2/T3, T4→T5, cola/llamar)
      + bootstrap de padres si hace falta (no dump como prueba de ABM)
    → Recién entonces ensanchar Ambulatorio (gate recepción, Cola B)
    → Internación (Admisión → Internación → Enfermería piso → Nutrición)
    → Diagnóstico (Lab / DxI / otros)
    → Facturación / caja / depósito
    → HC / Informes como transversal (no “un tile más”)
```

---

## Circuitos (dependencias entre módulos)

```text
                    ┌─ Identity / menús / personal ─┐
                    │         ts.centro_*            │
                    └──────────────┬─────────────────┘
         ┌─────────────────────────┼─────────────────────────┐
         ▼                         ▼                         ▼
   AMBULATORIO               INTERNACIÓN                 CONFIG (hojas)
   Recepción ──────┐         Admisión internados         ADMINISTRACION_GENERAL
   Turnos ─────────┼─ HC     Internación ──┬─ Enfermería   (11804 Nutrición ABM,
   Demanda ────────┤         Censo/cama ───┤   internados     convenios, …)
   Atención médica ┘         Guardia/GyE   ├─ Nutrición ops
                             Hosp. día     └─ Farmacia (depósito)
                                           (indicaciones + flag nutrición)
```

**Transversales:** Historia clínica, Informes, Estadísticas, Panel — leen varios circuitos;
no desbloquean Nutrición ni Turnos por sí solos.

---

## Tablero (origin 37 + admin + satélites)

Leyenda dest.: **slice** (CU en `ts`, módulo incompleto) / **A–C** / **pending**.
Depende de / habilita = módulos o troncos, no 1.200 hojas. “Piloto” no es estado
de destino.

### Ambulatorio (núcleo de uso)

| Módulo | A–C | Depende de (conocido) | Habilita | Confianza |
|--------|-----|------------------------|----------|-----------|
| RECEPCIÓN (`RECEPCION`) | no (slices cola) | Centro/puesto (gate); paciente; Identity | Cola, AGI, anuncio, atención | **slice** |
| TURNOS (`ATENCION_TURNOS`) | **sí** | Personal, hab, maestros, Identity | Agenda; AGI lista OTORGADO | **A–C** |
| DEMANDA_ESPONTANEA | no (CU-C) | Servicio/centro | Atención / medidor | **slice** |
| ATENCION_MEDICA | no | Recepción/demanda/turnos; HC | Evolución ambulatoria | **inferido** |
| ENFERMERIA_AMBULATORIA | no | Atención / servicio-centro | Indicaciones amb. | **hueco** |
| SERVICIO | no | Gate servicio-centro | — | **hueco** |

### Internación (tronco de Nutrición ops)

| Módulo | A–C | Depende de (conocido) | Habilita | Confianza |
|--------|-----|------------------------|----------|-----------|
| ADMISION_INTERNADOS | no | Centro/puesto admisión; paciente | Internación | **inferido** |
| INTERNACION | no | Admisión; sector/cama/censo | Enfermería piso, Nutrición N3–N5, fact. internado | **inferido** (bloqueante Nutrición ops) |
| ENFERMERIA_INTERNADOS | no | Internación | Registro enf.; consume indicaciones (incl. nutrición) | **inferido** |
| NUTRICION | **sí** | Rol `NUTRICIONISTA`; ABM Config 11804; **censo internación** para N3–N5 | Planilla / vigentes | **A–C** |
| HOSPITAL_DE_DIA | no | Servicio-centro | — | **hueco** |
| CENTRO_PROCEDIMIENTO | no | Servicio-centro (cirugía) | — | **hueco** |
| GUARDIA_Y_EMERGENCIAS | no | Gate GyE | HC / internación | **hueco** |
| INFECTOLOGIA | no | Internación (gate centros internación) | — | **hueco** |
| HOUSEKEEPING | no | Internación / camas | — | **hueco** |

### Diagnóstico / especialidades

| Módulo | A–C | Depende de (conocido) | Habilita | Confianza |
|--------|-----|------------------------|----------|-----------|
| LABORATORIO | no | Servicio-centro lab; **integraciones** | Órdenes | **inferido** (21 usuarios, alto esfuerzo) |
| DIAGNOSTICO_POR_IMAGENES | no | Servicio-centro | — | **hueco** |
| OTROS_ESTUDIOS | no | Servicio-centro | — | **hueco** |
| OFTALMOLOGIA | no | Servicio-centro | — | **hueco** |
| TERAPIA FISICA | no | Servicio-centro | — | **hueco** |
| ATENCION_MEDICA_DOMICILIARIA | no | Ambulatorio | — | **hueco** |

### Administración / dinero / depósito

| Módulo | A–C | Depende de (conocido) | Habilita | Confianza |
|--------|-----|------------------------|----------|-----------|
| ADMINISTRACION_GENERAL_NA | **sí (T0)** [`relevamiento-maestros/`](../relevamiento-maestros/) | Identity | Tronco centro/servicio/personal/convenio; **no** el tile entero (223 hojas) | **A–C** |
| DEPÓSITO (`DEPOSITO`, Farmacia) | no | Internación / indicaciones; `generico_equiv` | Dispensa | **inferido** |
| FACTURACION_AMBULATORIA | no | Prestaciones, convenios, atención | Caja | **inferido** |
| FACTURACION_INTERNADO | no | Internación, convenios | Caja | **inferido** |
| CAJA | no | Facturación | Cobro | **inferido** |
| COBRANZA_CONVENIO | no | Convenios / facturación | — | **hueco** |
| COMPRAS | no | Depósito | — | **hueco** |
| ANALISIS_DEBITOS | no | Facturación / convenios | — | **hueco** |
| LIQUIDACION_HONORARIO | no | Prestaciones / personal | — | **hueco** |

### Transversales y resto origin

| Módulo | A–C | Depende de (conocido) | Habilita | Confianza |
|--------|-----|------------------------|----------|-----------|
| HISTORIA_CLINICA | no | Casi todos los clínicos | Epicrisis, informes | **inferido** (máximo usuarios) |
| INFORMES | no | HC + reportes | Compaginación | **inferido** |
| ESTADISTICAS | no | Varios | — | **hueco** |
| PANEL_DE_CONTROL | no | Varios | — | **hueco** |
| ACREDITACION_PROFESIONALES | no | Personal / RRHH | — | **hueco** |
| AREA_ORGANIZACIONAL | no | Estructura org. | — | **hueco** |
| INCIDENTES | no | — | — | **hueco** |

Origin **no ve:** CRM, SEGURIDAD (sí en `MENU_APLICACION`, perfil admin).

### Satélites (no tile HIS)

| Pieza | A–C / estado | Depende de | Habilita |
|-------|----------------|------------|----------|
| Identity | oleadas | — | Login, roles, menús |
| AGI recepción/espera | slice (código hecho; data seed hoy) | Recepción / turnos OTORGADO | Autogestión |
| Anunciador + Tótem | **A–C** + slice | Cola / llamar | TV |
| Hospital-Reports | R3.1+ | Diseños + `ts` | PDF (ticket; BIRT nutrición diferido) |

---

## Lectura para Nutrición (ejemplo)

No hace falta migrar **Configuración** ni **Internación** enteros.

- **N1** (catálogo): hoja 11804 bajo Configuración; tronco = Identity + DDL `ts.nutricion`.
- **N0** (tile): rol + gate centro (personal–internación).
- **N3–N5:** bloqueante real = **censo internación** (módulo INTERNACION o slice `internacion-censo`), no el ABM de Configuración.

---

## Mantenimiento

Al cerrar un A–C, subir la fila de **hueco/inferido** → **A–C** y corregir aristas.
Dueño: el mismo que actualiza [`cobertura.md`](cobertura.md).
