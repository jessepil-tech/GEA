---
title: Inventario DDL gaps — M1b servicio / vínculo
status: active
owner: grupogea
last_updated: 2026-09-16
phase_id: sdd.hospital.maestros-m1b-servicio.ddl
---

# Inventario DDL gaps — M1b

`ts.servicio` y `ts.servicio_centro` están en el dump. Flyway **n/a**.

`servicio` NOT NULL: solo `id_servicio` (el nombre `(*)` es UI/API).

`servicio_centro` NOT NULL dump: `id_servicio` · `id_centro_ate` · `atiende_turnos` · `req_vademecum_prescrip` · `envia_mail_informe` · `comentarios_enriquecidos_lab` · `lugar_espera_servicio` · `atencion_amb_sin_recep` · `fact_observacion_guardia_amb` · `genera_atencion_aut_llamar`. Insert v1 (hoja datos) debe mandar default legacy (`N`/`S` según bean), no `ALTER`. El job que escribe `atiende_turnos` **no** se reabre.

Gap de objeto: ninguno para el CU.
