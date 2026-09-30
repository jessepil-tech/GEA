---
title: Complejidad — UX shell ≈ PrimeFaces
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.ux-shell-primefaces
---

# Complejidad real

## Respuesta corta

**Sí, es más que un mini-SDD.** Si el pedido es “acercarlo **bastante** a
PrimeFaces”, el orden de magnitud es el de un **design system + adopción**, no
un cambio de CSS de una tarde.

| Ambición | Complejidad | Orden de magnitud* |
|----------|-------------|--------------------|
| Solo colores/botones (L1) | Media-baja | ~3–8 días-persona |
| Shell menú/topbar/login (L2) | **Media** | ~2–4 semanas-persona |
| Kit tablas/diálogos/forms densos (L3) | **Alta** | ~1–2,5 meses-persona (con PrimeNG); más si Tailwind puro |
| Pixel cada pantalla legacy (L4) | **Muy alta** | multi-mes / no recomendado |

\*Equipo pequeño (1 front + revisión producto). No incluye reescritura de lógica.

## Por qué pesa

1. **Dos gramáticas distintas:** Flowbite/Tailwind (aire, radius grandes) vs
   PrimeFaces California (denso, `ui-widget`, tablas scroll).
2. **Ya hay pantallas** (recepción, AGI, anunciadores) en el look starter → U4
   es pase de adopción, no solo “theme nuevo en vacío”.
3. **Componentes difíciles:** DataTable anidada / scroll / filtros es el 50%+
   del “sentirse hospital”.
4. Legacy trae **layout + theme PF custom** (`california-layout`,
   `primefaces-california-thinksoft`) — hay que **interpretar**, no copiar JARs.

## Qué reduce complejidad (recomendado)

| Decisión | Efecto |
|----------|--------|
| Target **L3**, no L4 | Corta el 80% del trabajo inútil |
| Kit **PrimeNG** (opción A) | Reutiliza patrones cercanos a PrimeFaces |
| Un solo tema (`thinksoft`) | Evita portar 15 skins California |
| Exentar `/display/**` y tótems | Menos superficie |
| U2 gate temprano | Producto valida “¿se siente hospital?” antes de U3 caro |

## Qué la dispara

| Decisión | Efecto |
|----------|--------|
| “Que se vea **igual** que cada xhtml” | L4 |
| Mantener Flowbite **y** clonar PF a mano | Doble costo, peor resultado |
| Seguir mergeando features clínicas en look starter **sin** U1–U3 | Deuda visual compuesta |
| Exigir parity en dark mode + todos los skins | Scope explosivo |

## Comparación honesta con otros frentes

| Trabajo | Relativo a este UX |
|---------|---------------------|
| M5 acciones Cola B (API + UI funcional) | Similar o menor que **solo U2**; menor que U3+U4 |
| Mirror 1:1 `LLAMADO_ANUNCIADOR` | Otro tipo de costo (datos); no sustituye UX |
| Port A3 ocupación Node | Feature acotada ≪ L3 completo |

## Definición operativa de “bastante cerca”

Aprobado si un usuario de HOSPITAL_2, en 10 segundos:

1. Reconoce el **menú/topbar** como el hospital.
2. En una grilla, encuentra densidad y jerarquía familiares.
3. No pregunta “¿esto es otra aplicación de demos?”.

No aprobado si solo cambió el color primario sobre el layout starter.

## Próximo paso humano (U0)

Firmar en una reunión corta:

1. Target **L3** (sí/no).
2. Kit **PrimeNG** (sí/no).
3. Tema **thinksoft** (sí/no).
4. Quién aprueba capturas CA-1/CA-2.
