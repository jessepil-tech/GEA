# Despliegue AWS — Hospital-Reports (sidecar BIRT)

Análisis y estado del despliegue del sidecar de reportes en AWS.
Código IaC y Docker viven en el repo **Hospital-Reports** (`Dockerfile`, `terraform/`, `terraform/poc/`).

**Decisión:** ECS Fargate. **PoC:** desplegada y verificada (2026-08-25). **Prod:** Terraform completa diseñada, no aplicada.

---

## Quick path

| Escenario | Qué usar |
|-----------|----------|
| PoC pública (IP task) | `Hospital-Reports/terraform/poc/` |
| Prod / privada | `Hospital-Reports/terraform/` con `exposure_mode=private` + Secrets Manager |
| Análisis de alternativas | §2 abajo |

---

## 1. Perfil técnico (condiciona las opciones)

| Componente | Qué es | Implicancia en AWS |
|---|---|---|
| App Quarkus | Jar JVM (fast-jar), puerto 8082, `POST /api/v1/reports/run` | Contenedor con JDK 21 |
| Runtime BIRT | ~95 MB en jars (`ReportEngine/`), **no versionado** | Materializar en la **imagen** en build |
| Worker BIRT | JVM hijo vía `ProcessBuilder` por render | Spawn de procesos + JDK en runtime |
| JDBC | PostgreSQL por env / secrets | No hardcodear passwords |
| Assets | Logos + font DANI + `fontsConfig.xml` | Dentro de la imagen |
| Auth | Sidecar sin auth propia | VPC privada o PoC pública acotada |

**Problema central:** no es serverless-friendly (spawn + filesystem BIRT pesado).

---

## 2. Alternativas

| Opción | Veredicto |
|--------|-----------|
| **ECS Fargate** | **Recomendada** — ProcessBuilder + runtime en imagen |
| ECS EC2 | Solo si ya hay cluster EC2 |
| Lambda | **No** — cold start BIRT + spawn frágil |
| EKS | OK si ya hay Kubernetes |
| App Runner / Beanstalk | Posible; menos control que Fargate |

---

## 3. Problemas transversales

1. Runtime BIRT → materializar en imagen (no descargar al arranque).
2. Dockerfile multi-stage (sin plugin contenedor en el pom).
3. Rutas assets/fonts deterministas (`WORKDIR /app`).
4. Secrets DB → Secrets Manager / SSM (prod).
5. Red → PoC pública o prod privada + Hospital-API en misma VPC.
6. Memoria → Quarkus + JVM hijo; limitar concurrencia.
7. JDK 21 en imagen (no JDK 25 local).
8. IAM execution role ECS: ECR pull (`GetAuthorizationToken`, `BatchGetImage`, `GetDownloadUrlForLayer`) + logs.

---

## 4. PoC desplegada (estado 2026-08-25)

### Recursos

| Recurso | Valor |
|---------|--------|
| Cuenta | `107556471622` · profile AWS `sdilenardo` |
| Región | `us-east-1` |
| ECR | `grupogea/reports` · tag `0.2.0` |
| Cluster ECS | `osw-gea-reports` (existente, referenciado) |
| VPC | default `vpc-f6afc58c` |
| RDS | `grupogea-hospital` (PostgreSQL, `PubliclyAccessible=true`) |
| Service | `hospital-reports-poc-service` · task def `hospital-reports-poc:2` |

### Verificación E2E

- `GET /q/health` → **UP**
- `POST /api/v1/reports/run` `{"reportId":"NroColaEsperaRecep"}` → **200** + PDF ~2329 bytes
- Metadata PDF: `Creator(BIRT Report Engine … org.eclipse.birt.runtime_4.24.0)` → **BIRT real** (no fallback OpenPDF)
- IP pública de la task: **efímera** (cambia en redeploy); re-obtener con `list-tasks` + `describe-tasks` + ENI

### Gotchas

- En PowerShell, usar `curl --data-binary @body.json` (body inline rompe el JSON → 400 falso).
- Ingress SG de RDS: `tcp/5432` desde SG de la task (+ IP admin si hace falta pgclient).
- Driver PostgreSQL debe estar en `.birt-runtime/ReportEngine/lib` dentro de la imagen.

### Terraform completa (aún no aplicada)

`Hospital-Reports/terraform/`: workspaces `dev`/`qa`/`prod`, `exposure_mode=public|private`, Secrets Manager, ALB. Solo se validó a fondo `terraform/poc/`.

Detalle IaC: `Hospital-Reports/terraform/README.md` y `terraform/poc/README.md`.

---

## 5. Checklist hacia prod

- [ ] Commit del código del sidecar (imagen reproducible desde git)
- [ ] `terraform validate` de `Hospital-Reports/terraform/` (no solo poc)
- [ ] `exposure_mode=private` + Secrets Manager
- [ ] Cliente Hospital-API en la misma VPC
- [ ] Confirmar driver JDBC en la imagen

## Next step

Bitácora del frente reportes: [`birt-runtime-destino.md`](birt-runtime-destino.md) §4.3.
