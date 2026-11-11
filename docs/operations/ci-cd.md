# CI/CD

## 1. Principio

**Build once, promote many.** La imagen se construye una vez, se firma y se promueve por digest
entre entornos. Nunca se reconstruye para producción.

## 2. Workflows reutilizables

Viven en el repo `.github` de la organización:

| Workflow | Uso |
| --- | --- |
| `java-service-ci.yml` | Build, tests, integración, contrato, SAST, SBOM, imagen. |
| `node-service-ci.yml` | Lint, tests, build, imagen de frontend. |
| `docker-publish.yml` | Buildx, push, scan, SBOM, firma. |
| `helm-publish.yml` | Package y push de chart OCI. |
| `security-scan.yml` | CodeQL, Trivy, Gitleaks, dependency review. |
| `gitops-update.yml` | Abre PR en `gitops` con el digest. |
| `e2e.yml` | Playwright/API tests contra staging. |
| `docs-ci.yml` | Markdownlint y link check. |

Los repos de servicio llaman a estos workflows y mantienen un CI fino.

## 3. Pipeline de PR

```text
format → lint → unit → integration → contract → SAST → dependency scan
→ build (sin push) → SBOM → image scan → helm lint → policy check
```

Reglas:

- Todo PR corre tests y validaciones.
- No se publica imagen desde un PR.
- Se detectan breaking changes de OpenAPI con `oasdiff`.
- Se verifica Pact si el servicio es consumidor o productor.
- Se revisa que no haya secretos.
- Se exige al menos una aprobación humana.

## 4. Pipeline de `main`

```text
build image → SBOM → scan → sign (Cosign) → push (GHCR/ECR)
→ publish Helm chart OCI → abrir PR en gitops con digest
```

- La imagen se etiqueta con SHA y SemVer; se referencia por digest.
- Se publica procedencia y SBOM.
- El PR a `gitops` lo crea una GitHub App o un PAT con permisos acotados.
- Argo CD sincroniza staging automáticamente.

## 5. Promoción a producción simulada

1. Staging verde y smoke tests OK.
2. Se abre PR de promoción en `gitops` con el mismo digest.
3. Se requiere aprobación del environment `production`.
4. Argo CD sincroniza prod-sim.
5. Se ejecutan smoke tests.
6. Se registra el deployment y el changelog.

## 6. Ambientes y aprobaciones

| Ambiente | Trigger | Aprobación | Secretos |
| --- | --- | --- | --- |
| Local | Manual | No | `.env` local. |
| CI | PR/push | No | Efímeros + OIDC. |
| Staging | Push a `main` | No | Environment `staging`. |
| Prod-sim | PR de promoción | Sí | Environment `production`. |

## 7. Seguridad de la cadena de suministro

- Actions pinneadas por SHA.
- `GITHUB_TOKEN` con permisos mínimos.
- OIDC hacia AWS; sin claves estáticas.
- SBOM por imagen.
- Firma Cosign y verificación en admisión.
- Trivy/Grype para imágenes.
- CodeQL/Semgrep para código.
- Gitleaks para secretos.
- Dependency review para PRs.
- OpenSSF Scorecard.
- Políticas de admisión con Kyverno/OPA.

## 8. Branch protection

- `main` protegida.
- PR obligatorio.
- Checks requeridos.
- Historial lineal.
- Commits firmados.
- Sin force push.
- Conversaciones resueltas.
- CODEOWNERS para áreas sensibles.

## 9. Rollback

- Revertir el PR de `gitops` al digest anterior.
- Argo CD reconcilia.
- Verificar smoke tests.
- Si hay migración de DB, aplicar expand/contract para que el rollback sea seguro.
- Registrar incidente y postmortem.

## 10. Cambios cross-repo

Orden recomendado:

1. PR en `contracts` (compatible).
2. PR en el productor.
3. PR en el consumidor.
4. PR en `gitops`.
5. Promoción.

Si el cambio es breaking:

1. Nueva versión de contrato.
2. Productor soporta ambas versiones.
3. Consumidor migra.
4. Se retira la versión vieja.
5. Se actualiza el ADR.

## 11. Ejemplo de workflow de servicio

```yaml
name: ci
on:
  pull_request:
  push:
    branches: [main]
jobs:
  ci:
    uses: vela-bank/.github/.github/workflows/java-service-ci.yml@v1
    with:
      service: core-banking-service
    secrets: inherit
```

## 12. Métricas del pipeline

- Lead time de PR.
- Tiempo de pipeline.
- Tasa de fallos.
- Tiempo de promoción.
- Frecuencia de despliegue.
- MTTR.

## 13. Reglas

- No deploy manual a staging/producción.
- No `latest`.
- No secretos en logs.
- No desactivar checks para mergear.
- No promover sin smoke tests.
- No reconstruir la imagen para producción.
