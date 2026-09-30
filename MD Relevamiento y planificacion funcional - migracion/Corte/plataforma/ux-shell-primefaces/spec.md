---
title: Spec — UX shell acercamiento PrimeFaces
description: Definir paridad visual del shell Angular clínico vs HOSPITAL_2 (PF California/Thinksoft).
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.ux-shell-primefaces
---

# Spec — UX shell Hospital ≈ PrimeFaces

## Problema

1. Hospital-Web usa el **design system del starter** (`UI` + Tailwind + Flowbite):
   redondeado, aireado, look “SaaS moderno”.
2. HOSPITAL_2 usa **PrimeFaces 6.x** + layout **California** (tema Thinksoft y
   variantes): menú lateral denso, topbar, dataTables compactas, paneles
   `ui-widget`, tipografía Source Sans, cromática institucional.
3. El personal clínico juzga “¿es el hospital?” por **familiaridad visual** tanto
   como por flujos. El dossier cerró *un shell*; **no** cerró el look.

Sin este SDD, cada feature nueva (recepción, AGI, catálogo) consolida el look
starter y el costo de retema crece.

## Evidencia legacy (referencia visual)

| Pieza | Path |
|-------|------|
| Layout California | `HOSPITAL_2/WebRoot/contracts/california-layout/` (`template`, `sidebar`, `topbar`) |
| Tema Thinksoft | `…/css/layout-thinksoft.css` (+ scss) |
| Theme PF | `HOSPITAL_2/WebRoot/resources/primefaces-california-thinksoft/` |
| Login | `…/california-layout/login.xhtml` |
| Tablas densas | tags `nestedDataTable.xhtml`, grillas recepción |

Destino actual: `Hospital-Web/src/app/shared/ui/ui-classes.ts`, layouts
`_layout/authorized|anonymous`.

## Niveles de paridad (contrato de producto)

Hay que **elegir un nivel target** (recomendación en [plan.md](plan.md)):

| Nivel | Nombre | Qué incluye | Qué excluye |
|-------|--------|-------------|-------------|
| **L1** | Tokens | Color, tipografía, radios, espaciado base, botones primarios | Shell y tablas siguen starter |
| **L2** | Chrome | L1 + sidebar + topbar + login “california-like” | Componentes densos |
| **L3** | Kit clínico | L2 + DataTable / Dialog / Menu / Tabs / Messages estilo PF | Pixel de cada pantalla |
| **L4** | Pixel xhtml | Clonar layouts pantalla a pantalla | — (fuera de alcance salvo mandato) |

**“Acercalo bastante a PrimeFaces”** en lenguaje de este SDD = **L3** como
objetivo de programa; L2 como primer gate usable.

## Resultado deseado

1. Documento de **tokens** institucionales (Thinksoft / GEA) versionado.
2. Shell autorizado reconocible como “el hospital” (menú, topbar, densidad).
3. Kit de componentes para features clínicas nuevas **sin** reinventar CSS ad-hoc.
4. Excepción documentada: `/display/**` (TV) y tótems pueden no usar L3.
5. Criterios de aceptación visual (checklist + capturas side-by-side).

## Requisitos funcionales (UX)

| Id | Requisito |
|----|-----------|
| RF-1 | Tokens: primary/surface/border/font alineados a California-Thinksoft (o variante acordada) |
| RF-2 | Shell: sidebar colapsable + topbar + área contenido con densidad cercana a PF |
| RF-3 | Login anonymous retemado (no full-bleed starter si choca con marca hospital) |
| RF-4 | DataTable clínica: filas compactas, header sticky, scroll, filtros en barra |
| RF-5 | Dialog / confirm / messages / toast con jerarquía tipo `ui-widget` |
| RF-6 | Formularios: labels, inputs, botones coherentes con L3 (no mezclar 3 lenguajes visuales) |
| RF-7 | Features ya hechas (recepción cola, AGI, anunciadores ops) adoptan el kit en un pase |
| RF-8 | Design system único en código (`UI` o PrimeNG theme) — una fuente de verdad |

## No objetivos

- Clonar cada `.xhtml` 1:1 (L4).
- Portar JavaScript de California (`layout.js` / nanoscroller) tal cual.
- Forzar el look clínico en **display TV** fullscreen.
- Reimplementar PrimeFaces en el servidor (JSF).
- Dark mode como prioridad (legacy es mayormente claro).

## Criterios de aceptación (programa)

| CA | Cómo se verifica |
|----|------------------|
| CA-1 | Side-by-side: login + home shell vs captura HOSPITAL_2 (misma resolución) — L2 aceptado por producto |
| CA-2 | Pantalla Cola A / Cola B con DataTable L3 — densidad y jerarquía aprobadas |
| CA-3 | Ninguna pantalla clínica nueva introduce clases Flowbite “sueltas” fuera del kit |
| CA-4 | `/display/anunciadores/*` intacto o con waiver explícito |
| CA-5 | Doc `DESIGN_SYSTEM` / tokens actualizado; verify-report del corte |

## Clarify pendiente (bloquea U1 implementación fuerte)

1. ¿Target oficial = **L3**?
2. ¿Kit = **PrimeNG** (recomendado) o Tailwind retocado manteniendo Flowbite?
3. ¿Tema exacto = `thinksoft` u otra variante California en uso real del cliente?
4. ¿Quién aprueba capturas (producto / clínico / GEA)?
