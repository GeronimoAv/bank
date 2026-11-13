# ADR-0011 — Plataforma, IaC y operación

- **Estado:** Aceptado
- **Fecha:** 2026-11-13
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [ADR-0005](0005-local-platform-and-floci.md), [ADR-0006](0006-gitops-and-environments.md), [Operación](../operations/), [Mapa de repositorios](../architecture/repo-map.md)

## Contexto

El proyecto necesita una plataforma local reproducible y un camino creíble hacia AWS sin gastar
dinero real. ADR-0005 y ADR-0006 fijaron Floci y GitOps, pero quedaron abiertos la nube, la
herramienta de IaC, el clúster local, el registry, la herramienta de GitOps y el stack de
observabilidad.

## Decisión

| Decisión | Valor |
| --- | --- |
| D-03 — Nube principal | AWS (sandbox) |
| D-10 — IaC | Terraform |
| D-11 — Kubernetes local | kind |
| D-12 — Registry | GHCR en local/CI; ECR en AWS |
| D-13 — GitOps | Argo CD |
| D-14 — Observabilidad | Prometheus + Grafana + Loki + Tempo |

Reglas derivadas:

- La misma imagen se promueve entre entornos por digest.
- Staging despliega automáticamente desde `main`; producción simulada requiere aprobación.
- Floci emula S3, SQS/SNS, Secrets Manager y ECR en local.
- OpenTelemetry instrumenta servicios; el colector exporta a Prometheus, Loki y Tempo.
- Terraform administra red, EKS, RDS, S3, SQS/SNS, ECR e IAM en AWS sandbox.

## Consecuencias

### Positivas

- La mayor parte del desarrollo corre en local con costo casi nulo.
- Terraform y Argo CD dejan el despliegue declarativo y auditable.
- kind reproduce razonablemente Kubernetes sin infraestructura dedicada.
- El stack de observabilidad es estándar y portable.

### Negativas

- Floci no valida IAM real ni todos los comportamientos de AWS.
- kind no reproduce el aislamiento de red ni la escala de EKS.
- Mantener Terraform + Argo CD + observabilidad agrega componentes a operar.
- Prometheus/Grafana/Loki/Tempo consume recursos en una máquina de desarrollo.

### Mitigaciones

- Probar IAM y backups en AWS sandbox antes de confiar en Floci.
- Usar `platform` con `make dev-up` y perfiles de recursos acotados.
- Versionar dashboards y alertas como código.

## Alternativas consideradas

1. **Azure o GCP.** Válidas, pero el objetivo y la experiencia previa apuntan a AWS.
2. **OpenTofu o Pulumi.** OpenTofu es compatible; Pulumi agrega otro lenguaje. Terraform es el
   camino directo.
3. **k3d o minikube.** Válidos; kind es el más simple de automatizar en CI.
4. **Flux.** Válido, pero Argo CD ya está documentado y tiene UI útil para el aprendizaje.
5. **SigNoz.** Interesante, pero el stack Prometheus/Grafana/Loki/Tempo es más estándar.

## Notas

Este ADR cierra D-03, D-10, D-11, D-12, D-13 y D-14 de
[decisiones pendientes](../decisions-pending.md).
