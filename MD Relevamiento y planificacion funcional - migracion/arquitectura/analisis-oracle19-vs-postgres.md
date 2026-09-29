# Análisis: Oracle 19 vs PostgreSQL (y el rol del starter)

Fecha: 2026-08-13  
Actualizado: 2026-08-13 (aclara C del otro chat; **corte big-bang** vs strangler)
Estado: **recomendación A** (alineada al dossier §6); B y C quedan documentadas
para reabrir solo con evidencia nueva.

Complementa:

- [`dossier-migracion.md`](dossier-migracion.md) §6 (estrategia recomendada)
- [`mapa-productos-destino.md`](mapa-productos-destino.md) (runtime de datos)
- [`migracion-package-general.md`](migracion-package-general.md) (costo PL/SQL → destino)
- [`hallazgos-golden-master-vpn-2026-08-14.md`](hallazgos-golden-master-vpn-2026-08-14.md) (oráculo 11.2 en práctica)
- [`analisis-identity-separado.md`](analisis-identity-separado.md) (Identity aparte)

No sustituye el dossier: es un **fork de decisión** sobre destino de plataforma.

---

## 1. Qué dijo el otro chat (y qué no)

En otro hilo se plantearon dos ideas. Conviene citarlas **sin distorsión**:

| Afirmación | Lectura correcta |
|------------|------------------|
| Migrar Hospital a Quarkus *vía starter* no es buena idea | El starter no debe ser el vehículo de la migración de dominio |
| Lo mejor es migrar Hospital + BD a Oracle 19 | **Alternativa C:** actualizar **stack y base**, **prescindiendo** de Quarkus, del starter y de PostgreSQL |

Esa segunda línea **no** es “Quarkus apuntando a Oracle 19”. Es **abandonar el destino
Quarkus + Angular + Postgres** y modernizar el mundo legacy (Java/JSF/Hibernate/Ant o
sus sucesores cercanos) **sobre Oracle 19**.

Este documento compara:

- **A** — plan dossier (Quarkus + Postgres; 11.2 = oráculo/copia; **corte** al final)
- **B** — híbrido: sí Quarkus, pero datasource Oracle 19 (no es lo que dijo el otro chat)
- **C** — **la alternativa del otro chat:** sin Quarkus / sin starter / sin Postgres

### Glosario (opción A) — no confundir

| Término | Significa | **No** significa |
|---------|-----------|------------------|
| **Dual-runtime (dev)** | Mientras se construye: legacy en 11.2 (prod o copia); stack nuevo en **Postgres** | Usuarios en prod usando viejo y nuevo a la vez |
| **Golden master** | Capturar I/O en 11.2 (copia) y comparar contra Quarkus/Postgres | Cablear Quarkus al Oracle de producción |
| **UAT en paralelo** | Probar el sistema nuevo **aislado**, sin unirlo al legacy | Sync en caliente / strangler en prod |
| **Corte (cutover)** | Cuando el nuevo esté completo y validado: apagar/reapuntar al nuevo | Convivencia operativa indefinida |

**Modelo de entrega preferido (acordado con producto, 2026-08-13):** migrar / construir
el destino **completo** → UAT en paralelo **sin unirlo al viejo** → **un corte**.
El strangler (módulos nuevos tomando tráfico de a uno en prod) queda como variante
opcional del dossier, no como el plan por defecto.

---

## 2. Dos afirmaciones distintas (no mezclarlas)

### 2.1 “No migrar Hospital *a través del starter*”

| Lectura | ¿Acertado? | Comentario |
|---------|------------|------------|
| El starter **no es el producto clínico** | Sí | Es andamiaje. No traduce `HOSPITAL-BUSINESS` ni packages `TS.*`. |
| Clonar el starter y arrastrar store de identidad al Api | Sí, malo | Ya corregido (Identity IdP; Api sin users). |
| Por tanto hay que **olvidar Quarkus** | No necesariamente | Se puede rechazar el starter como vehículo y **seguir** con Quarkus como runtime destino (opción A). |

**Conclusión parcial:** el starter como *vehículo de dominio* es mala idea. Eso **no**
implica por sí solo tirar Quarkus ni Postgres.

### 2.2 Alternativa C del otro chat (sin Quarkus)

Propuesta explícita:

```text
Olvidar:  Quarkus, starter, PostgreSQL, (y en la práctica el piloto Identity/Api/Web nuevo)
Hacer:    upgrade Oracle 11.2 → 19
          + actualizar el stack de aplicación legacy (o su evolución “misma familia”)
```

Es un **cambio de destino de producto**, no un ajuste de motor dentro del plan actual.

El dossier (2026-08-13) asume destino Quarkus + Angular + Postgres. C **contradice**
ese destino. Se documenta aquí para decidir con los ojos abiertos, no para suavizarla
como “variante de A”.

---

## 3. Hechos que condicionan cualquier opción

| Hecho | Implicancia |
|-------|-------------|
| Oracle **11.2** en legacy | Sin soporte moderno; upgrade a 19 es proyecto DBA real si se elige B o C |
| ~61 packages de negocio; bloque recursivo | La lógica sigue en PL/SQL si no se extrae a app nueva |
| Auth personal = usuarios nativos Oracle | C puede **aplazar** Identity nuevo; A/B lo reconstruyen sí o sí |
| Destino de producto **acordado en dossier** | Un shell Angular, una solución Quarkus, Identity aparte → encaja A (o B), **no C** |
| Piloto ya invertido (Identity, Api, Web, anunciador, G1) | C implica **sunk cost** o reutilizar solo aprendizajes, no el código |

A y B reescriben hacia stack nuevo. **C moderniza el stack viejo** (o uno “cerca del
viejo”) y **no** entrega el mapa de productos destino del dossier.

---

## 4. Tres opciones

### A — Quarkus + Postgres; corte al final (plan actual / dossier §6)

```text
Durante el build / golden master:
  Legacy (referencia)  ──► Oracle 11.2     (prod intacta o copia; solo captura)
  Quarkus + Angular    ──► PostgreSQL      (sistema nuevo completo)

UAT en paralelo:  stack nuevo aislado (sin unir al legacy)
Corte:            usuarios → nuevo; apagado controlado del viejo
```

Usa starter solo como **scaffold**. Destino de producto = dossier.
**No** implica convivencia de tráfico en producción.

| Pros | Contras |
|------|---------|
| Código nuevo viable desde día 1 | Hay que cargar/migrar datos a Postgres para UAT y corte |
| No obliga upgrade 11→19 para arrancar | Portar dialéctica Oracle→Postgres |
| Reduce lock-in a largo plazo | Disciplina de golden master |
| UAT paralelo claro para el hospital | Corte único = go-live más tenso (riesgo concentrado) |
| Piloto ya valida arquitectura | Consumidores externos a la BD a inventariar antes del corte |

### B — Quarkus sobre Oracle 19 (híbrido; **no** es el otro chat)

```text
Legacy ──► 11.2 y/o 19
Quarkus + Angular ──► Oracle 19
(sin PostgreSQL como destino)
```

Mantiene Quarkus/Angular/Identity; cambia solo el motor del mundo nuevo.

| Pros | Contras |
|------|---------|
| Hibernate Quarkus 3 habla con 19 | Hay que hacer **11→19** igual |
| Menos fricción SQL/PL/BIRT al inicio | Lock-in Oracle; Postgres queda “para nunca” |
| Reutiliza el piloto de arquitectura | Riesgo de seguir pegados a packages PL/SQL |

### C — Modernizar stack + BD; **sin** Quarkus / starter / Postgres  
*(alternativa propuesta en el otro chat)*

```text
Hospital legacy (JSF / Hibernate 4 / Ant / …)
        │
        ▼  actualizar stack de aplicación (misma línea o evolución cercana)
Oracle 11.2  ──upgrade──►  Oracle 19
```

| Incluye | Excluye de forma explícita |
|---------|----------------------------|
| Upgrade de motor a 19 | Quarkus como destino |
| Actualizar Java/app server/Hibernate/JSF (o reemplazo “tradicional”) | Starter / Identity-Api-Web nuevos como plataforma |
| Seguir con PL/SQL en Oracle el tiempo que haga falta | PostgreSQL |
| | Piloto Angular/Quarkus como producto destino |

| Pros | Contras |
|------|---------|
| Un solo vendor BD; DBA en terreno conocido | **No** cumple el destino de producto del dossier |
| Evita dual-runtime de *desarrollo* y el costo Postgres | No resuelve por magia el monolito: igual hay que tocar Java/JSF/Ant |
| Puede ser más barato *si* el objetivo es “seguir vivos en Oracle” | Identidad nativa Oracle, 1.815 triggers, bloque PL/SQL: se **arrastran** |
| | UX/arquitectura moderna (Angular multi-front, API resource-server) **no** llega |
| | Tire el aprendizaje/código del piloto o lo deja como experimento muerto |
| | Riesgo de “actualizar por actualizar” y seguir igual de atrapados en 3–5 años |

**Costo dominante:** proyecto de **plataforma legacy** (BD + stack app), no migración
hacia el mapa `Hospital-Identity` / `Hospital-Api` / `Hospital-Web`.

---

## 5. Comparación directa

| Criterio | A (PG + Quarkus, corte) | B (Quarkus→19) | **C (otro chat: sin Quarkus)** |
|----------|------------------------|----------------|--------------------------------|
| Quarkus / Angular destino | Sí | Sí | **No** |
| Starter / Identity nuevo | Scaffold / IdP | Scaffold / IdP | **Prescinde** |
| PostgreSQL | Sí (nuevo) | No | **No** |
| Oracle 19 | No obligatorio | Sí | **Sí (eje)** |
| Cumple dossier de producto | Sí | Parcial (motor) | **No** |
| Modelo de go-live | **UAT paralelo aislado → un corte** | Igual o strangler | Upgrade in-place del legacy |
| Convivencia tráfico prod viejo+nuevo | **No** (por defecto) | Opcional | N/A (sigue el mismo producto) |
| Reescritura app | A dominio nuevo | A dominio nuevo | Actualización del stack viejo |
| Valor de negocio “nuevo” | Tras corte (piloto valida arch.) | Tras 19 + app + corte | Solo “stack más soportado” |
| Lock-in Oracle | Decrece | Alto | **Máximo** |
| Encaje con trabajo hecho | Directo | Replantear datasource | Descarta o aparca el piloto |

---

## 6. Criterios para reabrir B o C

Mantener **A** salvo evidencia fuerte.

### Reabrir B (Quarkus + Oracle 19)

1. Mandato de que el **destino de app** sigue siendo Quarkus/Angular, pero el motor
   **debe** ser Oracle.
2. Spike: costo Postgres (`GENERAL`/BIRT/IDs) >> costo 11→19, y se acepta Oracle ≥5 años.

### Reabrir C (sin Quarkus — alternativa del otro chat)

Solo si el objetivo de negocio **deja de ser** el mapa de productos destino y pasa a ser
algo como:

1. “Mantener el HIS actual vivo y soportado el máximo tiempo con el menor cambio de
   paradigma”, o
2. Mandato explícito de **no** introducir Quarkus/Postgres/Angular como plataforma, o
3. Decisión de cancelar el piloto y el dossier §6.

Sin ese cambio de objetivo, elegir C no es “más barato”: es **otro proyecto**.

---

## 7. Relación con el starter

| Hacer (bajo A o B) | Bajo C |
|--------------------|--------|
| Starter = scaffold | Starter **no aplica** (se prescinde) |
| Features en Api + Identity aparte | Se invierte en el monorepo / WARs legacy |
| No apuntar Hibernate Quarkus a 11.2 | El legacy sigue en Oracle (idealmente 19) |

Rechazar el starter como vehículo **no obliga** a C. Obliga a no confundir template con
producto — que es lo que A ya hace.

---

## 8. Recomendación

| Opción | Veredicto |
|--------|-----------|
| **A** | **Seguir.** Quarkus/Postgres; 11.2 = oráculo/copia; **UAT paralelo aislado → un corte**. |
| **B** | Solo si el destino de *app* sigue siendo Quarkus pero el motor debe ser Oracle. |
| **C** | **No** como default. Es la alternativa del otro chat (sin Quarkus/starter/Postgres): solo si se **cancela o reemplaza** el destino de producto del dossier. |

**Sobre el otro chat, reformulado:**

- Acertado: no migrar el hospital *dentro* del starter.
- Su alternativa fuerte era **C** (actualizar stack + Oracle 19, olvidar Quarkus/Postgres),
  no “Quarkus sobre 19”.
- C es legítima como *otra estrategia de empresa*; **no** es un atajo del plan A.

**Sobre “dual-runtime”:** en A significa **dos entornos en el build** (11.2 referencia +
Postgres destino), **no** usuarios usando ambos sistemas en producción a la vez.

---

## 9. Próximos pasos si se acepta A

1. Construir el destino (piloto → verticales) sobre Postgres; legacy 11.2 intacto en prod.
2. Golden master desde **copia** 11.2 → replay en Quarkus/Postgres (sin unir runtimes).
3. Cuando el alcance pactado esté completo: **UAT en paralelo aislado** → **un corte**.
4. Spike acotado `GENERAL` / IDs para validar costo de portabilidad.
   - **Hecho 2026-08-13:** `f_get_edad_anio` — PASS
     ([spike-golden-master-edad](../spikes/spike-golden-master-edad/)).
   - **Hecho 2026-08-13:** `f_get_interleaved_2_5` — PASS (HEX)
     ([spike-golden-master-interleaved](../spikes/spike-golden-master-interleaved/)).
   - Siguiente: inventario VPN `f_next_id_tabla` (`RoInventory`) + diseño
     ([spike-next-id-tabla](../spikes/spike-next-id-tabla/)) — **re-correr con VPN**.
5. Si ops exige Oracle: track **paralelo** de upgrade 11→19 **sin** abandonar A
   (motor del legacy, no cambio de destino de producto).
6. Si alguien empuja C completo: exigir decisión explícita de **cambio de destino de
   producto** (acta), no un ajuste técnico silencioso.
7. Revisar este doc tras el primer spike `GENERAL` o si aparece mandato §6.

---

## 10. Referencias internas

| Tema | Dónde |
|------|--------|
| Principio runtime + corte | dossier §6.1–6.3 |
| Riesgos 11.2 / puente 19 | dossier §7 |
| Runtime por producto | mapa-productos-destino |
| Package `GENERAL` | migracion-package-general.md |
| Identity aparte | analisis-identity-separado.md |
| Api sin store identidad | `Hospital-Api/docs/decisions/API_SIN_STORE_IDENTIDAD.md` |
