# ADR-0005 — Plataforma local y Floci

- **Estado:** Aceptado
- **Fecha:** 2026-11-09
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [Desarrollo local](../operations/local-development.md), [CI/CD](../operations/ci-cd.md)

## Contexto

El proyecto necesita un entorno local reproducible, barato y seguro para desarrollar y probar
servicios AWS sin una cuenta real. También necesita Kubernetes local para practicar despliegues.

## Decisión

Usar:

- **Docker Compose** para dependencias: PostgreSQL, Redis, Keycloak, Floci y Mailpit.
- **Floci** para emular servicios AWS: S3, SQS/SNS, DynamoDB, Secrets Manager, ECR y RDS.
- **kind** (o k3d) para Kubernetes local.
- **Argo CD** para GitOps local.
- **Testcontainers** para tests de integración con Postgres/Redis.
- **Tilt o Skaffold** para desarrollo iterativo dentro de Kubernetes.
- **Makefile** como interfaz única (`make dev-up`, `make test`, `make kind-up`).

## Consecuencias

### Positivas

- No se necesitan credenciales reales para desarrollar.
- Costo cero en el loop local.
- Entorno descartable y reproducible.
- Se practica IaC y GitOps localmente.
- Los tests de integración corren en CI sin cuenta AWS.

### Negativas

- Floci no es AWS: no reproduce IAM, cuotas, latencia ni fallos reales.
- kind no es EKS: faltan integraciones de red, IAM y almacenamiento reales.
- El entorno local puede consumir recursos.
- Hay que mantener versiones de imágenes y configuración.

### Mitigaciones

- Validación final en AWS sandbox.
- Contratos y tests de integración realistas.
- Documentar límites de Floci.
- CI con contenedores efímeros.
- Versiones pinneadas.

## Alternativas consideradas

1. **LocalStack.** Similar, pero Floci es MIT, sin token y más liviano.
2. **Moto.** Útil para tests unitarios, menos completo para integración.
3. **AWS real desde el inicio.** Costo y riesgo innecesarios.
4. **minikube.** Válido, pero kind es más rápido y reproducible en CI.

## Reglas

- Nunca usar credenciales reales en local.
- Floci usa credenciales descartables.
- El estado de Floci es descartable.
- Los tests no dependen de servicios externos de pago.
- La validación de producción simulada se hace en AWS sandbox.
