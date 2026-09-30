---
title: Inventario interacción UI — T5.1c elegibilidad
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-08
phase_id: sdd.hospital.turnos-agenda-elegibilidad-cobros
---

# Inventario interacción UI — Elegibilidad (G0)

Fuente: `agenda.xhtml` · `BBAgenda.validarElegibilidad` · `infoConvenio`.  
Complementa T5.1b chrome info convenio.

## North — afiliado / documento

| Control legacy | Disparador | Efecto | Web |
|----------------|------------|--------|-----|
| `p:inputMask` afiliado | `disabled` si `!controlElegibilidad && !validaNroDocumento` | No se tipea | enable condicional |
| máscara | `Mascara` desde plan (`#`→9, `a`→*) | Formato carnet | `plan_convenio.mascara_nro_afi` |
| `p:ajax` change afiliado | change | `validarElegibilidad`; onstart/oncomplete spinner | POST/GET validar |
| icono afiliado | render si `controlElegibilidad && !validaNroDocumento` | check verde / times rojo / plug naranja / exclamation rojo | icono + title |
| input documento | `rendered=validaNroDocumento` editable; else disabled | valida o solo display | dos modos HIS |
| icono documento | `controlElegibilidad && validaNroDocumento` | mismo semáforo | |

## Spinner

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| `$validandoElegibilidad` | onstart ajax | modal, no X, gif | overlay + copy `validandoElegibilidad` |

## Info convenio (ya T5.1b)

| Control | Disparador | Efecto | Web |
|---------|------------|--------|-----|
| click info plan | `infoConvenio` | carga `listDocReq` | GET doc-req al abrir (o cache north) |
| tabla vacía | sin filas | emptyMessage | **ya** chrome; **este slice** filas |

## Anti-regresión

| Error | Regla |
|-------|-------|
| Habilitar afiliado siempre | Prohibido — solo si HIS `controlElegibilidad` / valida doc |
| Cerrar P-ORA-010 porque el seed responde | Prohibido |
| Meter pagar/saldo en este PR | Prohibido → `turnos-agenda-cobros` |
| `@Output() select` | Prohibido |
| Pedir smoke sin geometría misma fila | Prohibido |
