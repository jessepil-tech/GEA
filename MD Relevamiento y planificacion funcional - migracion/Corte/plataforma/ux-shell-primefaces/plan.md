---
title: Plan — UX shell acercamiento PrimeFaces
version: 0.1.0
status: draft
owner: grupogea
last_updated: 2026-08-18
phase_id: sdd.hospital.ux-shell-primefaces
---

# Plan — UX shell

## Opciones técnicas

| Opción | Idea | Pros | Contras | Encaje “bastante PF” |
|--------|------|------|---------|----------------------|
| **A — PrimeNG + theme custom** | Sustituir Flowbite en shell clínico por PrimeNG; theme tokens desde California-Thinksoft | Misma familia mental que PrimeFaces; DataTable/Dialog maduros; menos CSS artesanal | Migrar pantallas existentes; curva equipo; bundle | **Mejor para L3** |
| **B — Retema Tailwind/`UI`** | Reescribir `ui-classes.ts` + layout authorized imitando PF | Sin nueva lib; control total | DataTable densa = mucho trabajo custom; riesgo de “casi pero no” | OK L1–L2; L3 caro |
| **C — Híbrido** | Chrome L2 en Tailwind; tablas/diálogos PrimeNG | Incremental | Dos sistemas hasta limpiar | Transición válida |

**Recomendación de plan:** **A** (o **C** como puente corto).  
PrimeFaces ≠ PrimeNG, pero es el camino estándar Angular para “sentirse PF”
sin clonar JSF.

## Cortes

| Corte | Nombre | Entrega | Gate |
|-------|--------|---------|------|
| **U0** | Clarify + inventario visual | Respuestas spec § Clarify; mosaico capturas legacy; decisión A/B/C | Producto firma nivel L2 vs L3 y opción kit |
| **U1** | Tokens + foundation | CSS variables / theme; tipografía; botones; doc tokens | Diff visual botones/forms básicos |
| **U2** | Chrome shell | Sidebar + topbar + login california-like; ruta authorized | CA-1 |
| **U3** | Kit L3 | DataTable, Dialog, Menu, Tabs, Messages; guía de uso | Story/gallery o pantallas piloto |
| **U4** | Adopción features | Pase recepción / AGI / anunciadores ops al kit; limpia Flowbite clínico | CA-2, CA-3 |

Satélites display/tótem: waiver en U0; no bloquean U2–U4.

## Dependencias

- No bloquea M5 Cola B funcional, pero **U4** debería seguir de cerca features UX nuevas para no doblar retrabajo.
- Identity / Api: sin cambio de contrato.
- Design system actual: `Hospital-Web/docs/architecture/DESIGN_SYSTEM.md` (actualizar en U1).

## Orden sugerido vs backlog recepción

1. U0 (1–3 días, decisión).
2. En paralelo a M5: U1–U2 (chrome) — bajo riesgo funcional.
3. U3 antes de muchas pantallas clínicas nuevas.
4. U4 continuo (cada feature nueva nace en kit).

## Riesgos (plan)

| Riesgo | Mitigación |
|--------|------------|
| “Bastante cerca” subjetivo | Checklist CA + 3 pantallas referencia firmadas |
| Doble sistema visual | Opción A/C con fecha de apagado Flowbite en rutas `/recepcion`, `/agi` |
| Scope creep L4 | Explicit no-objetivo en spec |
| Accesibilidad / teclado | Incluir en U3 (PF legacy tampoco es perfecto; no empeorar) |
