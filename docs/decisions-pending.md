# Decisiones pendientes

Las decisiones marcadas como `[PROPUESTO]` son recomendaciones, no decisiones cerradas. Para
cerrar una decisión se crea o actualiza un ADR y se elimina de esta lista.

## Tabla de decisiones

No hay decisiones abiertas que bloqueen el MVP. Las decisiones fundacionales se cerraron en los
ADRs [0008](adr/0008-repo-governance-and-naming.md), [0009](adr/0009-product-currency-and-simulation.md),
[0010](adr/0010-application-stack.md) y [0011](adr/0011-platform-and-operations.md).

Las decisiones que aparezcan durante la implementación se agregan acá antes de resolverlas.

## Cómo cerrar una decisión

1. Elegir una opción concreta.
2. Escribir un ADR en `docs/adr/` con contexto, decisión y consecuencias.
3. Actualizar los documentos afectados.
4. Eliminar o marcar como cerrada la fila de esta tabla.
5. Informar en el resumen del cambio.

## Decisiones ya tomadas

| Fecha | Decisión | Evidencia |
| --- | --- | --- |
| 2026-11-09 | Trabajar con dos microservicios de negocio y multi-repo | [ADR-0001](adr/0001-polyrepo.md) |
| 2026-11-09 | Separar core banking de transfers | [ADR-0002](adr/0002-service-boundaries.md) |
| 2026-11-09 | Usar ledger de doble entrada | [ADR-0003](adr/0003-ledger.md) |
| 2026-11-09 | Contratos OpenAPI/eventos como fuente de verdad | [ADR-0004](adr/0004-contracts-and-integration.md) |
| 2026-11-09 | Floci para emulación local de AWS | [ADR-0005](adr/0005-local-platform-and-floci.md) |
| 2026-11-09 | GitOps con promoción por digest | [ADR-0006](adr/0006-gitops-and-environments.md) |
| 2026-11-09 | Protocolo de colaboración con IA | [ADR-0007](adr/0007-ai-collaboration.md) |
| 2026-11-13 | Org `vela-bank`, servicios `core-banking-service`/`transfers-service`, repos privados, licencia Apache-2.0, dominio solo local | [ADR-0008](adr/0008-repo-governance-and-naming.md) |
| 2026-11-13 | Moneda única COP (Colombia), Mailpit, KYC mock, SLO 99.9% en staging | [ADR-0009](adr/0009-product-currency-and-simulation.md) |
| 2026-11-13 | Java 21 + Spring Boot, React + TypeScript + Vite, PostgreSQL 16, SQS/SNS + outbox, Keycloak | [ADR-0010](adr/0010-application-stack.md) |
| 2026-11-13 | AWS, Terraform, kind, GHCR/ECR, Argo CD, Prometheus/Grafana/Loki/Tempo | [ADR-0011](adr/0011-platform-and-operations.md) |

> Las decisiones de esta tabla son de arquitectura/proceso y producto. Cualquier cambio requiere un
> nuevo ADR que superseda al anterior.
