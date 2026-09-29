# Pipeline — Anunciador (sala TV) + Tótem AGI

`phase_id:` **`sdd.hospital.relevamiento-node-anunciador.pipeline`**  
Fecha: **2026-08-26**  
Padre: [`README.md`](README.md) · Proceso: [`relevamiento-modular-funcional.md`](../../canon/relevamiento-modular-funcional.md)

Guion **configuración → operación**. Ninguna etapa en silencio.

---

## Diagrama

```text
Personal / Identity
    → Permisos menú (Config anunciador, Terminal AG, AGI, Recepción)
    → ABM Anunciador (+ assets logo/fondo)
    → Vínculo anunciador ↔ ambientes
    → ABM Terminal AG + opciones (tótem)
    → Diccionario TTS / pack logos (según centro)
         ↓
Operación: Tótem AGI (idTerminal) → cola/recepción → Llamar
         ↓
TV display ← ts.llamado_anunciador (+ WS)
```

---

## Fases A1–A9

| # | Etapa | Legacy (evidencia) | Destino | Estado |
|---|-------|--------------------|---------|--------|
| A1 | Identidad / personal | Sesión JSF + `Seguridad.tienePermiso` (`HOSPITAL_2`) | Hospital-Identity JWT; TV: `X-API-Key` | **Parcial** — auth ok; perfiles ≠ 1:1 URL legacy |
| A2 | Roles / perfiles | Menú HOSPITAL_2 por permiso/URL | Claims / `menuKey` `ANUNCIADOR`, `AGI`, `RECEPCION` (`Hospital-Web` `hospital-menu.catalog.ts`) | **Parcial** — DEMO puede mostrar todo el menú |
| A3 | Maestros | `ts.anunciador`, `ambiente_amb`, `sector_*`, `terminal_ag`, `opcion_terminal_ag`, `diccionario_anunciador`, `pack_logos` | Flyway V27–V32; seed `db/dev-seed/ts_anunciador_demo.sql` | **DDL migrado**; **datos = seed** (sync UAT diferido) |
| A4 | Habilitación | `ANUNCIADOR_AMBIENTE_AMB` (`BBAnunciadorAmbienteAmb`); terminal→ambiente; `activo` | Lectura en Api (activo = EXISTS vínculo); **sin ABM** | **Seed**; escritura **diferida** |
| A5 | Parámetros | Flags voz/multimedia, BLOBs logo/fondo, opciones terminal, diccionario | GET flags/assets/diccionario; sin POST config | **Read migrado**; write **diferido** |
| A6 | Materialización | Packages/Java → `LLAMADO_ANUNCIADOR`; cola AGI | `AnunciadorWritePort` → `ts.llamado_anunciador` | **Migrado** |
| A7 | Operación diaria | Vue sala + AGI tótem + recepción llamar | `/display/anunciadores/{id}`, `/agi/*`, `POST …/llamar`, WS | **Migrado** (dev) |
| A8 | Ciclo de vida | PL/SQL anular / quitar llamado | Consume-on-read + safety 1h + puerto `quitar*` | **Migrado** núcleo TV; Llamar clínico **gate-done** [`cu-llamar-atencion-medica/`](../../cortes/recepcion/cu-llamar-atencion-medica/); enganche `quitar*` = Fase 4 |
| A9 | Side-effects | Socket.IO, ticket BIRT, pack logos tótem | WS Quarkus; ticket PDF G1-d; pack logos **ausente** | **Parcial** |

---

## Pantallas legacy de configuración (no operación)

| Función | Path típico |
|---------|-------------|
| ABM anunciador | `HOSPITAL_2/WebRoot/pages/configuracion/anunciador/*.xhtml` · `BBAnunciador`, `BBDatosAnunciador` |
| Vínculo ambientes | `anunciadorAmbienteAmb.xhtml` · `BBAnunciadorAmbienteAmb` |
| Flags config avanzada (misma fila) | `configuracionAvanzada.xhtml` |
| Vínculo serv / triage / esp-serv | `anunciadorServ.xhtml`, `anunciadorTriage.xhtml`, `anunciadorEspServ.xhtml` |
| Diccionario | `diccionarioAnunciador.xhtml` · `BBDiccionarioAnunciador` |
| Terminal AG | `…/terminalAutogestion/*.xhtml` · `BBTerminalAutoGestion`, `BBDatosTerminalAutogestion` |
| Opciones terminal | `opcionTerminalAutogestion.xhtml` · `BBOpcionTerminalAutogestion` |
| Pack logos (Node) | `ANUNCIADOR/anunciador/services/routers/configuracion/packLogos.js` |

Runtime tótem (no config): `AGI/.../BBInicioTerminalAg.java` (`?idTerminal=`).

---

## PASS / gaps de esta fase

| Criterio | Resultado |
|----------|-----------|
| A1–A9 inventariadas | **Sí** |
| A1–A5 sin tratar seed como “cerrado” | **Sí** — ver [`maestros.md`](maestros.md) |
| Operación A6–A7 como única evidencia de paridad | **Prohibido** — perímetro cerrado exige P3 config |
