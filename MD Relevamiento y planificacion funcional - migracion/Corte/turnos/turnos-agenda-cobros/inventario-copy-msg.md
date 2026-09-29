---
title: Inventario copy — T5.1d cobros
version: 1.0.0
status: active
owner: grupogea
last_updated: 2026-09-09
phase_id: sdd.hospital.turnos-agenda-cobros.copy
---

# Inventario copy — Cobros agenda (G0)

Fuente: `Hospital-Legacy/HOSPITAL_2/src/ar/com/thinksoft/resources/Resources.properties` + `MessageBundle.LEYENDA_CONVENIO_DEFAULT`.  
Web: `turnos-agenda-labels.ts`.

| msg.key / constante | Valor | En UI este slice |
|---------------------|-------|------------------|
| `informacion` | Información | header los 3 dialogs |
| `estado` | Estado | popup obs |
| `respuesta` | Respuesta | popup obs |
| `cod_error` | Cód. Error | popup obs |
| `error` | Error | popup obs |
| `LEYENDA_CONVENIO_DEFAULT` | Se tomará el convenio por defecto: {%1} y la práctica puede tener coseguro. | rojo, FontBold |
| `saldo_cta_cte` | Saldo Cta. Cte. | popup + infoTurno |
| `deuda` | Deuda | dialog saldo |
| `observaciones` | Observaciones | textarea paciente |
| `el_paciente_tiene_prescripciones_medicas` | El paciente tiene prescripciones médicas | texto si flag |
| `aceptar` | Aceptar | botones HIS |
| `cerrar` | Cerrar | pagar |
| `coseguro_voluntario` | Coseguro Voluntario | infoTurno |
| `coseguro_obligatorio` | Coseguro Obligatorio | infoTurno |
| `desea_continuar` | ¿Desea continuar? | pagar |
| `el_paciente_debera_abonar_coseguro` | El paciente deberá abonar un coseguro de | pagar |
| `el_paciente_debera_abonar_coseguro_obligatorio` | El paciente deberá abonar un coseguro obligatorio de | pagar (HIS tiene espacio inicial; Web recorta) |
| `el_paciente_debera_abonar_coseguro_voluntario` | El paciente deberá abonar un coseguro voluntario de | pagar |
| `si_es_voluntario_y_de` | si es voluntario y de | pagar |
| `si_es_obligatorio` | si es obligatorio | pagar |
