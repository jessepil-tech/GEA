# Pipeline — orientación (no es A1–A9 de negocio)

Recorrido de **quién usa el HIS todos los días** para saber *dónde está* cada cosa.

| # | Etapa | Pregunta | Evidencia origin 2026-08-31 |
|---|--------|----------|------------------------------|
| P0 | Portal | ¿Hay paso antes del login? | Sí: Ingresar al sistema / Descargas |
| P1 | Login | Labels vs placeholder; dónde sale el error | Placeholder; growl lejos |
| P2 | Home módulos | ¿Cuántos, orden, agrupación, buscador? | 37 tiles, 3 col, A–Z, MAYÚSCULAS, sin buscador |
| P3 | Entrada a módulo | ¿Aterriza en el módulo o en un CU? | Módulo (Turnos `inicio.faces`; Recepción **gate** de puesto) |
| P4 | Menú del módulo | ¿Horizontal / west? ¿Hojas? | Turnos: 4 raíces, ~48 hojas en DOM (hover) |
| P5 | Contexto de sesión | ¿Centro, box, call center? | Recepción: CEDIM / RECEPCION CEDIM / PUESTO 1. Turnos: CC en chrome |
| P6 | Satélites | ¿Otra app? | AGI / TV no en grilla origin |
| P7 | Perfil | ¿El home es el catálogo completo? | No: origin 37 tiles; no ve **SEGURIDAD** ni **CRM**. Sí ve `ADMINISTRACION_GENERAL_NA` (config). |

**PASS:** ninguna etapa en silencio. Completar P2–P5 para **admin** y un perfil clínico
cuando haya dump O1.
