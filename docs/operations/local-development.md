# Desarrollo local

## 1. Objetivo

Levantar un entorno bancario completo en una máquina de desarrollo, sin credenciales reales y sin
costos de nube.

## 2. Prerrequisitos

| Herramienta | Uso |
| --- | --- |
| Docker o Podman | Contenedores. |
| Docker Compose | Dependencias locales. |
| Java 21 + Maven | Backend `[PROPUESTO]`. |
| Node.js 22 + pnpm | Frontend `[PROPUESTO]`. |
| kubectl | Kubernetes. |
| kind o k3d | Clúster local. |
| Helm | Charts. |
| Argo CD CLI | Bootstrap de GitOps. |
| Floci CLI | Emulación AWS. |
| make | Interfaz de comandos. |
| Tilt o Skaffold | Loop de desarrollo en Kubernetes (opcional). |

## 3. Repositorios

El workspace recomendado:

```text
~/src/vela-bank/
├── .github/
├── docs/
├── contracts/
├── platform/
├── service-template/
├── core-banking-service/
├── transfers-service/
├── gitops/
├── infra/
└── web/
```

`platform/scripts/clone-all.sh` clona los repos hermanos con `gh repo clone`.

## 4. Servicios locales

| Servicio | Puerto | Uso |
| --- | --- | --- |
| PostgreSQL core | 5432 | Base `core_banking`. |
| PostgreSQL transfers | 5433 | Base `transfers`. |
| Redis | 6379 | Cache/sesiones. |
| Keycloak | 8080 | OIDC/OAuth2. |
| Floci | 4566 | AWS emulado. |
| core-banking-service | 8081 | API core. |
| transfers-service | 8082 | API transfers. |
| web | 3000 | Cliente. |
| admin | 3001 | Backoffice. |
| Grafana | 3002 | Dashboards. |
| Prometheus | 9090 | Métricas. |
| Loki | 3100 | Logs. |
| Tempo | 3200 | Trazas. |
| Argo CD | 8083 | GitOps UI. |

## 5. Comandos

```bash
make dev-up          # dependencias + red
make dev-down        # baja todo
make dev-reset       # borra volúmenes y recrea
make services-up     # corre servicios desde checkouts
make test            # tests locales
make lint            # linters
make kind-up         # clúster local
make argocd-bootstrap
make e2e             # flujo completo
```

## 6. Floci

```bash
floci start
eval $(floci env)
aws --endpoint-url http://localhost:4566 s3 mb s3://vela-statements
aws --endpoint-url http://localhost:4566 sqs create-queue --queue-name transfers-events
```

Recursos locales esperados:

- bucket `vela-statements`;
- cola `transfers-events` + DLQ;
- tópico `domain-events`;
- secreto `local/core-banking/db`;
- repositorio ECR `core-banking-service`, `transfers-service`.

El estado es descartable: `floci stop && floci start` deja la nube local limpia.

## 7. Keycloak

- Realm: `vela-bank`.
- Clientes:
  - `web` (público, PKCE).
  - `admin` (público, PKCE).
  - `transfers-service` (confidential, client credentials).
  - `core-banking-service` (resource server).
- Roles: `customer`, `operator`, `auditor`.
- Usuarios sintéticos: `cliente1`, `cliente2`, `operador1`, `auditor1`.
- Passwords solo en `.env` local, nunca en Git.

## 8. Variables de entorno

Nombres esperados en `.env.example` (sin valores reales):

```text
POSTGRES_CORE_URL=
POSTGRES_TRANSFERS_URL=
REDIS_URL=
KEYCLOAK_URL=
KEYCLOAK_REALM=
KEYCLOAK_CLIENT_ID=
KEYCLOAK_CLIENT_SECRET=
AWS_ENDPOINT_URL=
AWS_REGION=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
SQS_TRANSFERS_QUEUE_URL=
S3_STATEMENTS_BUCKET=
OTEL_EXPORTER_OTLP_ENDPOINT=
```

## 9. Kubernetes local

```bash
kind create cluster --name vela-bank
kubectl apply -f platform/kind/namespaces.yaml
make argocd-bootstrap
argocd app list
```

Namespaces:

- `vela-system` (Keycloak, observabilidad, Argo CD).
- `vela-staging` (servicios).
- `vela-local` (desarrollo).

## 10. Observabilidad local

- OpenTelemetry Collector recibe OTLP.
- Prometheus scrapea servicios y collector.
- Grafana con dashboards base.
- Loki para logs.
- Tempo para trazas.
- Un dashboard por servicio y uno de flujo de transferencia.

## 11. Troubleshooting

| Problema | Causa probable | Solución |
| --- | --- | --- |
| Puerto ocupado | Otro servicio local | Cambiar puerto en `.env`. |
| Floci no responde | Docker/Podman detenido | `floci stop && floci start`. |
| Keycloak tarda | Arranque inicial | Esperar healthcheck. |
| Imagen no encontrada | Registry/digest | Verificar `gitops` y credenciales. |
| Servicio no conecta a DB | Migración o URL | Revisar logs y `make dev-reset`. |
| Argo CD OutOfSync | Git desactualizado | Revisar PR de GitOps. |

## 12. Reglas

- Sin credenciales reales.
- Sin datos reales.
- No commitear `.env`.
- No usar `latest` en imágenes.
- No desactivar TLS/verificación para “que funcione”.
- El entorno local no reemplaza staging para validar IAM, cuotas o carga.

## 13. Limitaciones

Floci y kind no reproducen AWS/EKS al 100%. La validación final de IAM, red, RDS, límites y
comportamiento bajo carga se hace en una cuenta AWS sandbox.
