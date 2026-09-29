# Mapa de repositorios

Proyecto Azure DevOps: **GrupoGEA** · rama de trabajo: **`dev/dev`**

| Repo | URL | Responsabilidad | Puerto dev |
|------|-----|-----------------|------------|
| **Hospital-Migration** | https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-Migration | Docs de programa e índice SDD | — |
| **Hospital-Identity** | https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-Identity | Único emisor de identidad (JWT, login, `/me`) | 8080 |
| **Hospital-Api** | https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-API | API de negocio; valida Bearer / API Key | 8081 |
| **Hospital-Web** | https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-Web | Shell Angular (auth → Identity; negocio → Api) | 4200 |
| **Hospital-Reports** | https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-Reports | Sidecar HTTP + worker BIRT (PDF tickets) | 8082 |
| **Hospital-Legacy** | https://dev.azure.com/originsw/GrupoGEA/_git/Hospital-Legacy | Referencia legacy (AGI, ANUNCIADOR, HOSPITAL_2, …) | — |

## Decisiones transversales (resumen)

1. **Identity es el único IdP.** Hospital-Api no emite ni persiste usuarios/sesiones.
2. **Hospital-Api** es resource server (`mp.jwt.verify` + API Key para display/TV).
3. **Reportes BIRT** no se embeben en Api: proceso aparte vía Hospital-Reports.
4. **Piloto** mide velocidad y valida stack; features nuevas = paridad legacy o waiver
   documentado (ver `docs/sdd/regla-waiver-paridad-legacy.md`).

## Dónde documentar qué

| Tipo de cambio | Dónde |
|----------------|--------|
| Slice que toca 2+ repos / orden del piloto | `Hospital-Migration/docs/sdd/<slice>/` |
| Frente reportes BIRT (plan R*, avance, AWS, guía R5) | `Hospital-Migration/docs/birt-runtime-destino.md` · `docs/despliegue-hospital-reports-aws.md` · `docs/sdd/register-report-sidecar.md` · `docs/sdd/sidecar-reports-avance.md` |
| Contrato o invariante de un solo servicio | `Hospital-*/docs/sdd/` de ese repo (puntero al programa si aplica) |
| ADR local de un servicio | `Hospital-*/docs/decisions/` (si existe) |
| Código / IaC del servicio | Repo del producto (p.ej. `Hospital-Reports/terraform/`) |
