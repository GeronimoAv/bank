# Decisiones pendientes

Las decisiones marcadas como `[PROPUESTO]` son recomendaciones, no decisiones cerradas. Para
cerrar una decisión se crea o actualiza un ADR y se elimina de esta lista.

## Tabla de decisiones

| # | Decisión | Opciones | Recomendación | Bloquea | Estado |
| --- | --- | --- | --- | --- | --- |
| D-01 | Nombre de la organización | `vela-bank`, `vela-bank-labs`, otro | `vela-bank` | Creación de repos | Abierta |
| D-02 | Moneda y mercado | ARS, USD, EUR, multi-moneda | Moneda única; ejemplos en USD | Dominio, UI, límites | Abierta |
| D-03 | Nube principal | AWS, Azure, GCP, OCI | AWS | IaC, CI/CD, staging | Abierta |
| D-04 | Stack backend | Java/Spring Boot, Node/NestJS, otro | Java 21 + Spring Boot | Servicios | Abierta |
| D-05 | Stack frontend | React/Vite, Next.js, Angular | React + TypeScript + Vite | Frontends | Abierta |
| D-06 | Nombres de servicios | `core-banking-service` + `transfers-service`; `accounts-service` + `transfers-service` | `core-banking-service` + `transfers-service` | Repos, contratos | Abierta |
| D-07 | Base de datos | PostgreSQL, MySQL, otra | PostgreSQL 16 | Ambos servicios | Abierta |
| D-08 | Mensajería | SQS/SNS, Kafka, RabbitMQ | SQS/SNS con Floci; outbox | Eventos | Abierta |
| D-09 | Autenticación | Keycloak, Auth0, Spring Authorization Server | Keycloak | Seguridad, frontend | Abierta |
| D-10 | IaC | Terraform, OpenTofu, Pulumi | Terraform (o OpenTofu) | Plataforma | Abierta |
| D-11 | Kubernetes local | kind, k3d, minikube | kind | Plataforma local | Abierta |
| D-12 | Registry de imágenes | GHCR, ECR, Floci ECR | GHCR en local/CI; ECR en AWS | CI/CD | Abierta |
| D-13 | GitOps | Argo CD, Flux | Argo CD | Despliegue | Abierta |
| D-14 | Observabilidad | Prometheus/Grafana/Loki/Tempo, SigNoz | Stack Prometheus/Grafana/Loki/Tempo | Operación | Abierta |
| D-15 | Visibilidad de repos | Públicos, privados, mixto | Privados al inicio; públicos si es portfolio | GitHub org | Abierta |
| D-16 | Licencia | MIT, Apache-2.0, propietaria | A definir | Repos públicos | Abierta |
| D-17 | Dominio web | `vela.bank`, subdominio propio, solo local | Solo local al inicio | Frontend, TLS | Abierta |
| D-18 | Proveedor de email/SMS | Mailpit local, SES, Twilio | Mailpit local | Notificaciones | Abierta |
| D-19 | KYC/AML | Mock, proveedor sandbox | Mock determinista | Onboarding | Abierta |
| D-20 | SLOs iniciales | 99.9%, 99.5%, sin SLO | 99.9% staging | Operación | Abierta |

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

> Las decisiones de esta tabla son de arquitectura/proceso. Las decisiones de producto y stack
> siguen abiertas hasta que el humano las confirme.
