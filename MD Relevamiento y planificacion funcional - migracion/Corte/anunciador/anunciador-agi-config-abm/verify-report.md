---
title: Verify — ABM Anunciador + Terminal AG
version: 0.4.0
status: draft
owner: grupogea
last_updated: 2026-09-18
phase_id: sdd.hospital.anunciador-agi-config-abm
---

# Verify — `anunciador-agi-config-abm`

**Gate:** C0 inventarios · C1–C3 API en repo · C4 UI en repo · C5 **`diferido(anunciador-agi-config-abm-c5)`**. **No cerrado**. Escritura vigente 2026-09-18 en dump `grupogea-hospital_dev`: `ts.anunciador` **id=1** (`P3-COBRO`). Terminal `diferido(fixture)`.

**Evidencia (2026-09-18):** `POST /api/v1/anunciadores` **201** id=1 · `POST …/diccionario` **201** `P3COBRO` · sin Bearer **401**. PG `grupogea-hospital_dev` (postgresql_01). El cobro 2026-09-15 id=3 era `hospital_api` bootstrap (renombrado a `hospital_api_bootstrap_20260917`); **no** rige.

## Capacidades legacy

| Capacidad | Estado | Ref |
|-----------|--------|-----|
| Alta/edición anunciador | done | IT C1 8 PASS 2026-09-15 · cobro dump **id=1** `P3-COBRO` 2026-09-18 |
| Vínculo anunciador↔ambiente | done | IT C1 POST/DELETE `/{id}/ambientes` (DevServices) |
| Upload logo / fondo | done | IT C1 PUT/DELETE `/{id}/logo` |
| Flags config avanzada (misma fila `ts.anunciador`) | done | IT C1 POST/PUT flags + GET `/{id}/config` |
| Catálogo ambientes para buscador vínculo | done | GET `/api/v1/anunciadores/ambientes-catalogo` IT C1 |
| Diccionario write | done | IT C3 6 PASS · cobro PK `P3COBRO` dump 2026-09-18 |
| Terminal AG CRUD | done | IT C2 5 PASS · `/api/v1/agi/terminales` (DevServices) |
| Opciones terminal | done | IT C2 POST/PUT/DELETE `/{id}/opciones` |
| Pack logos | diferido(`anunciador-pack-logos`) | inventario |
| Vínculo serv / triage / esp-serv | diferido(`anunciador-config-avanzada`) | inventario |
| Display / WS / Llamar | N/A este corte | otro slug, no este |

## Paridad UI (xhtml)

| Artefacto G0 | Estado |
|--------------|--------|
| [inventario-copy-msg.md](inventario-copy-msg.md) | done 2026-09-08 |
| [inventario-validaciones.md](inventario-validaciones.md) | done 2026-09-08 |
| [inventario-geometria.md](inventario-geometria.md) | done 2026-09-08 |
| [inventario-interaccion-ui.md](inventario-interaccion-ui.md) | done 2026-09-08 |
| Template Angular / `*-labels.ts` | C4-1 + C4-2 en repo (`anunciador-abm-labels.ts`, `terminal-ag-abm-labels.ts`, `diccionario-anunciador-abm-labels.ts`) |
| Click-through ABM | C4-1/C4-2 **histórico** 2026-09-12 · no re-click 2026-09-15 |

## Evidencia C4-1 (2026-09-11)

- `npx ng build --configuration=development` — PASS (`anunciador-abm-component` lazy chunk).
- `AnunciadorConfigResourceIT` — **8** PASS (incluye `ambientesCatalogo_includesSeedConsultorio`).
- Ruta `/anunciadores` → `AnunciadorAbmComponent`; interceptor `auth` **no** redirige 404/500 de `/v1/anunciadores` a páginas de error.
- GET staff `/ambientes-catalogo` para buscador de vínculo (no ABM `ambiente_amb`).
- No se ejecutó C5 smoke.

## Click-through C4-1 (2026-09-11 · Playwright MCP · Web `:4210`)

Login `admin` · `/anunciadores`. PG local `hospital_api` (sin seed ANU-DEMO ni `ts.ambiente_amb`).

| Paso | Resultado |
|------|-----------|
| Shell north/west, breadcrumb ANUNCIADOR → Anunciador, sin title duplicado | PASS |
| Agregar + Aceptar datos → POST 201, toast «Registro insertado con éxito.», URL display, west habilitado | PASS |
| Avanzada: check multimedia habilita URL; PUT flags 200 | PASS |
| Cargar logo PNG → PUT `/logo` 200; GET logo data-URI OK por API | PASS API · preview UI quedó «Sin Imagen» (re-chequear) |
| Hoja Ambiente + dialog catálogo 1200×550 | PASS; catálogo vacío (0 filas `ts.ambiente_amb` en este PG) |
| Buscar `ANU-DEMO` | dialog vacío (no hay seed) — esperado en este PG |
| Buscar lista / 1 match | **FAIL** — GET `/anunciadores` 401 luego 200; el subscribe del Buscar no carga la fila |
| Eliminar | PASS copy **¿Desea eliminar el Anunciador?** · DELETE físico · toast «Registro eliminado con éxito.» · `ts.anunciador` count 0 |
| Dialog buscador | footer Cancelar poco alcanzable (`max-h` sin altura fija); overlay no se cerraba al pasar a Eliminar — **parche** `h-[min(550px,90vh)]` + `agregar()`/`loadId()` cierran overlays |

`TSK-web-c4-1` **cerrada 2026-09-12** (re-clic Buscar + preview logo). C4-2 y C5 no ejecutados.

## Click-through C4-1 re-clic (2026-09-12 · Playwright MCP · Web `:4210`)

Fixes en repo: interceptor refresh **antes** del GET si el JWT está vencido (`auth.interceptor.ts`); GET logo `text/plain` + `normalizeDataUri`; `previewSeq` + FileReader; Buscar `type="button"` + `(click)="buscarNorth()"`.

| Paso | Resultado |
|------|-----------|
| Clic **Buscar** (north `C4-1 FIX`) | **PASS** — GET `/anunciadores` 200 → GET `/{id}/config` 200; nombre `C4-1 FIX`; URL `/display/anunciadores/2`; Eliminar habilitado |
| Hoja avanzada tras Buscar | **PASS** — `logoPreview` `data:image/png;base64,…`; `<img>` `naturalWidth` 189; Fondo «Sin Imagen» (sin PUT fondo; esperado) |
| JWT vencido forzado en `localStorage` | no re-probado en browser (bloqueo de aprobación); código de pre-refresh queda en interceptor |
| DELETE del registro de prueba `C4-1 FIX` | no ejecutado (bloqueo de aprobación); fila puede seguir en PG local |

Vitest: `anunciadores.repository.impl.spec.ts` + `auth-session.service.spec.ts` — 8 PASS (tanda del fix).

## Click-through C4-2 (2026-09-12 · Playwright MCP · Web `:4210`)

Rutas `/anunciadores/diccionario` y `/anunciadores/terminales` bajo satélite ANUNCIADOR. Limpieza: DELETE SQL de `C4-1 FIX` (id 2) → `ts.anunciador` count 0.

| Paso | Resultado |
|------|-----------|
| Menú Diccionario + Terminal Auto Gestión habilitados | PASS |
| Diccionario: Agregar `C42TEST` → POST 201; editar equivalente → PUT 200; confirm **¿Desea eliminar el registro?** → DELETE 204 | **PASS** |
| Terminal: north 300+Buscar+centro readonly; west 2 hojas; opciones/Eliminar disabled sin id; datos 2-up; lupa centro | PASS |
| Buscar terminal | PASS — GET `/agi/terminales/config` 200; toast sin registros (0 filas) |
| Dialog centros 1200×550 | PASS; vacío (0 `ts.ambiente_amb`) |
| Alta terminal + popup opciones | no re-clicado — faltan padres `ambiente_amb` / `recepcion` en este PG (datos, no UI) |

Destinos de opción (recepción / triage espera) van como id numérico: no hay catálogo API v1 (terminal triage diferido).

`TSK-web-c4-2` cerrada en código+diccionario. C5 no ejecutado.

## Viaje Playwright

| Viaje | ¿Pantalla? | Decisión | Spec / nota |
|-------|------------|----------|-------------|
| ABM anunciador (alta / buscar / eliminar) | sí | **diferido(fixture)** | Click MCP 2026-09-12; no hay spec en `Hospital-Web/e2e/` ni re-click esta tanda |
| ABM diccionario | sí | **diferido(fixture)** | Click MCP 2026-09-12; la fila UI `C42TEST` se borró; cobro API = `P3COBRO` |
| Alta terminal + opciones | sí | **diferido(fixture)** | No re-clicado. C5 **sin pedido** |
| Legacy HIS | — | **N/A** | Sin fixture Oracle vigente |

## Evidencia de escritura

PG `grupogea-hospital_dev` (consulta 2026-09-18):

```sql
SELECT id_anunciador, anunciador FROM ts.anunciador WHERE id_anunciador = 1;
-- 1 | P3-COBRO
SELECT palabra FROM ts.diccionario_anunciador WHERE palabra = 'P3COBRO';
-- P3COBRO
```

Origen: `POST /api/v1/anunciadores` **201** y `POST /api/v1/anunciadores/diccionario` **201** (JWT `admin`, Api `:8081`). NextId: `ts.sec_id_tabla` fila `ANUNCIADOR` (`Hospital-Api/scripts/sql/seeds/sec-id/anunciador.sql`; no inserta el anunciador). **No** borrar id=1 ni `P3COBRO`.

El id=3 de 2026-09-15 vivía en `hospital_api` bootstrap; esa base ya no es la de trabajo.

## Ledger de evidencia

Canon: [`regla-evidencia-ejecutable.md`](../../../canon/regla-evidencia-ejecutable.md).

| # | Afirmación | Clase | Artefacto registrado | Ref | Verdicto |
|---|-----------|-------|----------------------|-----|----------|
| 1 | Build Web del ABM compila | build | `npx ng build --configuration=development` · exit **0** · lazy `anunciador-abm-component` 85.73 kB · `dist/hospital-web` | 2026-09-16T00:07:03Z | **verificado** |
| 2 | API anunciador + flags + ambientes + assets | test | `mvn -pl presentation-api -am test -Dtest=AnunciadorConfigResourceIT,…` · `AnunciadorConfigResourceIT` **8 PASS** (1 skip cobro) | 2026-09-15 21:07:33 | **verificado** |
| 3 | API terminal AG + opciones | test | mismo comando · `TerminalAgConfigResourceIT` **5 PASS** | 2026-09-15 21:07:33 | **verificado** |
| 4 | API diccionario | test | mismo comando · `DiccionarioAnunciadorResourceIT` **6 PASS** (1 skip cobro) | 2026-09-15 21:07:33 | **verificado** |
| 5 | POST anunciador sin el rol (sin Bearer) | acceso / endpoint | Live dump 2026-09-18: `POST /api/v1/anunciadores` sin `Authorization` → **401**. IT 2026-09-15 `create_withoutBearer_returnsUnauthorized` · **401** | 2026-09-18 | **verificado** |
| 6 | Escritura anunciador vigente en dump | escritura | `POST /api/v1/anunciadores` **201** · **id=1** · `anunciador=P3-COBRO` · `SELECT id_anunciador, anunciador FROM ts.anunciador WHERE id_anunciador = 1` → `1 \| P3-COBRO` · PG `grupogea-hospital_dev`. NextId `sec_id_tabla` ANUNCIADOR (prox_id quedó 2) | 2026-09-18 | **verificado** |
| 7 | Escritura diccionario vigente | escritura | `POST …/diccionario` **201** · PK `P3COBRO` · `SELECT palabra FROM ts.diccionario_anunciador WHERE palabra = 'P3COBRO'` → `P3COBRO` | 2026-09-18 | **verificado** |
| 8 | Unit Web (vitest / `ng test`) | test | `npx ng test --watch=false` · **58** files · **134 PASS** (incluye `anunciadores.repository.impl` + `auth-session.service`) | 2026-09-15 21:07:28 | **verificado** |
| 9 | Buscar north carga la fila | e2e | — | — | **no ejecutado** (no re-click esta tanda) |
| 10 | Preview de logo en UI | e2e | — | — | **no ejecutado** |
| 11 | Diccionario CRUD desde UI | e2e | — | — | **no ejecutado** |
| 12 | Interceptor JWT vencido en browser | e2e | código en `auth.interceptor.ts`; no re-probado | — | **no ejecutado** |
| 13 | Alta terminal + popup opciones | e2e / escritura | `diferido(fixture)` · no re-clicado | — | **no ejecutado** |
| 14 | C5 smoke: alta → display + GET terminal | escritura + G6 | **diferido(`anunciador-agi-config-abm-c5`)** · id=1 cobrado en C1 no sustituye C5; 0 `ambiente_amb`/`recepcion` | 2026-09-18 | **no ejecutado** (sin pedido) |

### Lectura (por qué el gate sigue abierto)

1. API + **id=1** vigente en el dump cubren fuente #1 de anunciador/diccionario. No sustituyen C4 re-click ni C5.
2. Viaje Playwright está **declarado** (`diferido(fixture)`): no hay spec CI. Declararlo no es `e2e-migrado`.
3. Filas 9–14 son `no ejecutado`, no silencio.
4. `ANU-DEMO` 1001 **no** está en este dump; no usarlo como cobro.

### Para cerrar el gate

Spec Playwright en `Hospital-Web/e2e/` **o** re-click con Web UP; C5 se cobra en [`anunciador-agi-config-abm-c5/`](../anunciador-agi-config-abm-c5/); no borrar `id=1`. Terminal sigue `diferido(fixture)` hasta padres `ambiente_amb` / `recepcion`.
