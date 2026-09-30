# SDD — UX shell Hospital (acercamiento a PrimeFaces)

`phase_id:` **`sdd.hospital.ux-shell-primefaces`**  
Estado: **spec/plan draft → review** · 2026-08-18

No es un “mini-ajuste”. Acercar Hospital-Web (starter Tailwind/Flowbite) al look
de HOSPITAL_2 (PrimeFaces **California / Thinksoft**) es un **programa de diseño
de sistema** con varios cortes.

| Doc | Rol |
|-----|-----|
| [spec.md](spec.md) | Problema, niveles de paridad, RF, no-objetivos |
| [plan.md](plan.md) | Opciones técnicas + cortes U0–U4 |
| [tasks.md](tasks.md) | Checklist (ops + app) |
| [complejidad.md](complejidad.md) | Esfuerzo, riesgos, qué es “bastante cerca” |

## Relación con decisiones previas

| Decisión previa | Cómo encaja |
|-----------------|-------------|
| Paridad UX = **un shell**, una sesión ([mapa](../../../arquitectura/mapa-productos-destino.md) §7) | Este SDD define el **look** de ese shell |
| Starter Angular como andamiaje | Se **retema / sustituye kit**; no se abandona el repo |
| Satélites (tótem, display TV) | Pueden quedar fuera del look clínico (excepción explícita) |

## Veredicto de complejidad (una línea)

**Alta** si el objetivo es “bastante parecido a PrimeFaces California-Thinksoft”
en shell + tablas + diálogos + densidad de pantallas clínicas.  
**Media** si solo tokens + chrome (menú/topbar) y se tolera Flowbite en forms.  
**Muy alta / no recomendada** si se pide pixel-perfect de cada `.xhtml`.
