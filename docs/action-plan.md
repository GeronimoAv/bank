# Plan de acción

Este es el plan de trabajo vivo. Se actualiza al cerrar cada hito. Las tareas grandes se dividen
en slices pequeños, con criterios de aceptación y verificación.

## 1. Objetivo inmediato

Dejar la documentación fundacional en este repositorio antes de renombrarlo y crear la
organización multi-repositorio.

**Estado:** en progreso.

## 2. Workstreams

### WS-0 — Documentación y gobierno

| # | Tarea | Entregable | Estado |
| --- | --- | --- | --- |
| 0.1 | Protocolo de IA | `AGENTS.md` | Hecho |
| 0.2 | Visión y alcance | `docs/vision.md`, `docs/scope.md` | Hecho |
| 0.3 | Roadmap y plan | `docs/roadmap.md`, este archivo | Hecho |
| 0.4 | Arquitectura y repos | `docs/architecture/` | Hecho |
| 0.5 | Ledger | `docs/domain/ledger.md` | Hecho |
| 0.6 | Contratos v0 | `docs/contracts/` | Hecho |
| 0.7 | ADRs | `docs/adr/` | Hecho |
| 0.8 | Operación | `docs/operations/` | Hecho |
| 0.9 | Gobierno de org | `docs/governance/` | Hecho |
| 0.10 | Reparar `CONTRIBUTING.md` | Sección 11 + texto basura | Hecho |

### WS-1 — Organización y repositorios

| # | Tarea | Entregable | Depende de |
| --- | --- | --- | --- |
| 1.1 | Crear organización | Org `vela-bank` `[PROPUESTO]` | Decisión de nombre |
| 1.2 | Crear repo `.github` | Perfil, templates, reusable workflows | 1.1 |
| 1.3 | Transferir `bank` → `docs` | Repo de documentación | 1.1 |
| 1.4 | Crear `contracts` | OpenAPI/eventos v0 | 0.6 |
| 1.5 | Crear `platform` | Compose, Floci, kind, Argo CD | 0.7 |
| 1.6 | Crear `service-template` | Esqueleto de servicio | 1.2 |
| 1.7 | Configurar equipos, rulesets y environments | Gobierno | 1.2 |
| 1.8 | Terraform de la org | Org as code | 1.2 |

### WS-2 — Walking skeleton

| # | Tarea | Entregable | Depende de |
| --- | --- | --- | --- |
| 2.1 | `core-banking-service` mínimo | Health + DB + migración | 1.6 |
| 2.2 | `transfers-service` mínimo | Health + llamada stub | 1.6 |
| 2.3 | CI de ambos servicios | Build, test, imagen, SBOM | 1.2 |
| 2.4 | Helm charts | Deploy en kind | 2.3 |
| 2.5 | Argo CD apps | Sync automático | 1.5 |
| 2.6 | Observabilidad mínima | Métricas, logs, trazas | 1.5 |

### WS-3 — Núcleo contable

| # | Tarea | Entregable | Depende de |
| --- | --- | --- | --- |
| 3.1 | Modelo de clientes/cuentas | Migraciones + endpoints | 2.1 |
| 3.2 | Ledger | Journal entries + postings | 3.1 |
| 3.3 | Depósito simulado | Flujo de cuatro ojos | 3.2 |
| 3.4 | Saldos y extracto | Consultas + CSV | 3.2 |
| 3.5 | Tests de invariantes | Unit + property-based | 3.2 |
| 3.6 | Reconciliación | Job + alerta | 3.4 |

### WS-4 — Transferencias

| # | Tarea | Entregable | Depende de |
| --- | --- | --- | --- |
| 4.1 | API de transferencias | OpenAPI + implementación | 3.2 |
| 4.2 | Idempotencia | Unique key + request hash | 4.1 |
| 4.3 | Orquestación | Llamada interna a core | 4.1 |
| 4.4 | Outbox y eventos | Relay + consumidor idempotente | 4.3 |
| 4.5 | Timeouts y conciliación | Reintentos + estado `UNKNOWN` | 4.2 |
| 4.6 | Contract tests | Pact + `can-i-deploy` | 4.1 |

### WS-5 — Frontend y backoffice

| # | Tarea | Entregable | Depende de |
| --- | --- | --- | --- |
| 5.1 | App cliente | Login, cuentas, movimientos | 3.4 |
| 5.2 | Transferencias UI | Formulario + estado | 4.1 |
| 5.3 | Backoffice | Búsqueda, congelar, auditar | 3.3 |
| 5.4 | Reverso UI | Motivo + aprobación | 5.3 |
| 5.5 | E2E | Playwright | 5.2 |

### WS-6 — Plataforma production-like

| # | Tarea | Entregable | Depende de |
| --- | --- | --- | --- |
| 6.1 | Terraform AWS | Red, EKS, RDS, S3, SQS/SNS, ECR | 2.5 |
| 6.2 | Floci para local | Recursos AWS emulados | 1.5 |
| 6.3 | GitOps entornos | staging + prod-sim | 2.5 |
| 6.4 | Backups/restore | RDS + S3 + pruebas | 6.1 |
| 6.5 | SLOs y alertas | Dashboards + runbooks | 2.6 |
| 6.6 | Supply chain | SBOM, firma, admisión | 2.3 |
| 6.7 | DR básico | RTO/RPO probados | 6.4 |

## 3. Decisiones que bloquean

| Decisión | Bloquea | Recomendación |
| --- | --- | --- |
| Nombre de la org | WS-1 | `vela-bank` `[PROPUESTO]` |
| Moneda/mercado | WS-3, WS-5 | Moneda única; ejemplos en USD `[PROPUESTO]` |
| Stack backend | WS-2 | Java 21 + Spring Boot `[PROPUESTO]` |
| Nube principal | WS-6 | AWS `[PROPUESTO]` |
| Nombres de servicios | WS-1 | `core-banking-service` + `transfers-service` `[PROPUESTO]` |
| Visibilidad de repos | WS-1 | Privados al inicio, públicos si es portfolio |

Ver [decisions-pending.md](decisions-pending.md).

## 4. Orden de ejecución recomendado

1. Cerrar decisiones de la sección 3.
2. Crear organización y `.github`.
3. Transferir este repo a `docs`.
4. Crear `contracts` y `platform`.
5. Crear `service-template`.
6. Generar los dos servicios y desplegarlos en kind.
7. Crear `gitops` e `infra`.
8. Crear `web`.
9. Endurecer seguridad y observabilidad.
10. Agregar extensiones solo con el MVP estable.

## 5. Riesgos

| Riesgo | Impacto | Mitigación |
| --- | --- | --- |
| Sobre-fragmentación de repos | Alto | Crear solo repos con owner y ciclo de vida claro. |
| Deriva de contratos | Alto | `contracts` como fuente de verdad, Pact y `oasdiff`. |
| CI duplicado | Medio | Reusable workflows y `service-template`. |
| Entorno local complejo | Medio | `platform` con `make dev-up` y scripts. |
| Costos de nube | Medio | Floci + kind; AWS sandbox acotado. |
| Alcance demasiado grande | Alto | Fases con criterios de salida; recortar sin culpa. |
| Decisiones no registradas | Medio | ADR obligatorio y `decisions-pending.md`. |

## 6. Definition of Done de un slice

- [ ] Alcance y criterios de aceptación escritos.
- [ ] Contrato actualizado si aplica.
- [ ] Código y tests.
- [ ] Documentación y ADR si aplica.
- [ ] Observabilidad mínima.
- [ ] CI verde.
- [ ] Desplegado en staging o kind.
- [ ] Runbook si es operación sensible.
- [ ] Resumen de cambios y decisiones.
