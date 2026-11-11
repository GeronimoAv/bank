# ADR-0006 — GitOps y entornos

- **Estado:** Aceptado
- **Fecha:** 2026-11-09
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [CI/CD](../operations/ci-cd.md), [Mapa de repositorios](../architecture/repo-map.md)

## Contexto

El proyecto necesita promover artefactos entre entornos de forma auditable, reproducible y
reversible. También necesita separar build de deploy y aplicar controles tipo banca.

## Decisión

Adoptar **build once, promote many** con GitOps:

- CI construye una imagen inmutable y publica SBOM/firma.
- La imagen se referencia por **digest**, nunca por tag mutable.
- CI abre un PR al repo `gitops` actualizando el digest.
- Argo CD sincroniza el clúster con el estado declarado en Git.
- Staging se sincroniza automáticamente desde `main`.
- Producción simulada requiere aprobación manual y queda registrada.
- Terraform/OpenTofu gestiona la infraestructura; Helm/Kustomize empaqueta aplicaciones.
- Los secretos se gestionan con External Secrets/Sealed Secrets, no en Git.

## Consecuencias

### Positivas

- Auditoría completa de qué se desplegó y cuándo.
- Rollback por digest.
- Separación build/deploy.
- Deriva detectable y reconciliación automática.
- Se practican controles de cambio y aprobación.

### Negativas

- Más repos y pasos que un deploy manual.
- Hay que gestionar PRs automáticos y credenciales de bot.
- Argo CD y Helm agregan complejidad.
- La promoción requiere disciplina.

### Mitigaciones

- Automatizar el PR de GitOps con GitHub App/PAT.
- Checks obligatorios en `gitops`.
- Documentar rollback y break-glass.
- Un solo entorno productivo simulado para controlar costos.

## Entornos

| Entorno | Propósito | Aprobación | Datos |
| --- | --- | --- | --- |
| Local | Desarrollo | No | Sintéticos |
| CI | Tests efímeros | No | Sintéticos |
| Staging | Integración | Automática desde `main` | Sintéticos |
| Prod-sim | Simulación de producción | Manual | Sintéticos |

## Alternativas consideradas

1. **Deploy directo desde CI con `kubectl`/`helm`.** Simple, pero sin reconciliación ni auditoría
   declarativa.
2. **Flux.** Válido; se elige Argo CD por su UI y adopción.
3. **Tags mutables.** Rechazado: impide rollback confiable y auditoría.

## Reglas

- Nunca reconstruir la imagen para producción.
- Nunca desplegar `latest`.
- Todo cambio de producción tiene PR, aprobación y registro.
- Los secretos no se guardan en Git.
- El rollback es redeploy del digest anterior.
