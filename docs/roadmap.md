# Roadmap

El roadmap prioriza tener un **walking skeleton** desplegado temprano. No se construye el dominio
completo antes de validar la organización, los contratos y la plataforma.

## Fase 0 — Fundaciones documentales

**Objetivo:** dejar decisiones, alcance, arquitectura y protocolo de trabajo registrados.

**Entregables:**

- `AGENTS.md` y protocolo de IA.
- Visión, alcance, roadmap y plan de acción.
- Mapa de repositorios y límites de servicios.
- ADRs iniciales.
- Contratos OpenAPI/eventos v0.
- Guías de plataforma local, CI/CD, seguridad, observabilidad y testing.

**Criterio de salida:** el equipo puede crear la organización y los repositorios sin improvisar
decisiones de arquitectura.

## Fase 1 — Walking skeleton

**Objetivo:** probar el camino completo de ingeniería con dos servicios vacíos pero conectados.

**Entregables:**

- Organización de GitHub y repo `.github`.
- `platform` con Docker Compose, Floci, Keycloak, Postgres, kind y Argo CD.
- `service-template` con CI, Dockerfile, Helm, tests y observabilidad.
- `core-banking-service` con health, DB, migración y un endpoint.
- `transfers-service` con health y una llamada stub a core banking.
- Imágenes publicadas y desplegadas en kind vía Argo CD.
- Trazas, métricas y logs visibles.

**Criterio de salida:** un cambio en cada servicio llega a kind por CI/CD sin pasos manuales.

## Fase 2 — Núcleo contable

**Objetivo:** implementar el ledger y las cuentas.

**Entregables:**

- Modelo de clientes y cuentas.
- Ledger de doble entrada con postings inmutables.
- Depósito simulado con aprobación de cuatro ojos.
- Saldos disponible/contable.
- Extracto CSV.
- Tests de invariantes y property-based testing.
- Reconciliación básica.

**Criterio de salida:** todo movimiento genera asientos balanceados y el saldo es reconstruible
desde el ledger.

## Fase 3 — Transferencias

**Objetivo:** transferencias internas idempotentes entre dos servicios.

**Entregables:**

- API de transferencias con `Idempotency-Key`.
- Máquina de estados y persistencia.
- Llamada interna a core banking.
- Outbox y eventos.
- Manejo de timeouts, reintentos y conciliación.
- Tests de contrato y E2E.

**Criterio de salida:** reintentos y fallos no duplican ni pierden dinero.

## Fase 4 — Frontend y backoffice

**Objetivo:** flujo usable de punta a punta.

**Entregables:**

- App cliente con OIDC PKCE.
- Saldo, movimientos, transferencia y extracto.
- Backoffice con búsqueda, congelamiento, auditoría y reverso.
- E2E con Playwright.
- Accesibilidad básica e i18n.

**Criterio de salida:** una persona puede completar el flujo completo desde el navegador.

## Fase 5 — Plataforma production-like

**Objetivo:** acercar la operación a un entorno bancario.

**Entregables:**

- Terraform/OpenTofu para AWS sandbox.
- ECR, RDS, S3, SQS/SNS, Secrets Manager e IAM.
- EKS o clúster staging real; kind para local.
- GitOps con promoción staging → producción simulada.
- Backups, restore y DR básico.
- SLOs, alertas y runbooks.
- Seguridad de supply chain completa.
- Políticas de admisión y network policies.

**Criterio de salida:** promoción por digest, rollback probado y restore verificado.

## Fase 6 — Extensiones

**Objetivo:** ampliar el producto solo con el MVP sólido.

**Candidatos:**

- Límites y comisiones configurables.
- Reglas de fraude/AML.
- Transferencias programadas.
- Notificaciones por email/SMS simulado.
- Tarjetas simuladas.
- Préstamos simples.
- Multi-moneda/FX (solo si hay una decisión explícita).
- Service catalog (Backstage).
- Caos engineering.

## Hitos

| Hito | Fase | Evidencia |
| --- | --- | --- |
| M0 — Documentación base | 0 | Docs y ADRs mergeados |
| M1 — Dos servicios en kind | 1 | Argo CD synced, trazas visibles |
| M2 — Ledger funcionando | 2 | Tests de invariantes verdes |
| M3 — Transferencia idempotente | 3 | E2E y contract tests verdes |
| M4 — Flujo web completo | 4 | Demo de punta a punta |
| M5 — Production-like | 5 | Promoción, rollback y restore |
| M6 — Extensiones | 6 | Backlog priorizado |

## Reglas de avance

- No se pasa de fase sin cumplir el criterio de salida.
- Cada fase actualiza `docs/action-plan.md`.
- Cada decisión nueva se registra en un ADR.
- El alcance puede recortarse, pero no ampliarse silenciosamente.
