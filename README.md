# Vela Bank

Banco minorista simulado, construido como proyecto de ingeniería para practicar un entorno
bancario real: movimiento de dinero correcto, identidad segura, auditoría, operación, DevOps,
Kubernetes e infraestructura como código.

> **Estado:** fase de documentación. Todavía no hay código ejecutable.
> **Naturaleza:** prototipo con datos sintéticos. No opera con dinero real ni con PII real.

## Qué vas a encontrar

- Un MVP con **dos microservicios** conectados:
  - `core-banking-service`: clientes, cuentas, ledger de doble entrada, saldos y extractos.
  - `transfers-service`: transferencias, idempotencia, orquestación y eventos.
- Frontends de cliente y backoffice.
- Una organización **multi-repositorio** con ownership, contratos y GitOps.
- Plataforma local con **Floci** (AWS emulado), **kind** (Kubernetes local), **Keycloak**,
  PostgreSQL, Redis, Argo CD y observabilidad.
- Prácticas de banca: idempotencia, outbox, auditoría, cuatro ojos, promoción por digest,
  SLOs, runbooks y seguridad de cadena de suministro.

## Documentación

La fuente de verdad está en [`docs/`](docs/README.md). Puntos de entrada:

| Documento | Para qué |
| --- | --- |
| [Visión](docs/vision.md) | Propósito, principios y métricas de éxito. |
| [Alcance del MVP](docs/scope.md) | Qué entra, qué no y criterios de aceptación. |
| [Roadmap](docs/roadmap.md) | Fases y criterios de salida. |
| [Plan de acción](docs/action-plan.md) | Trabajo vivo y próximos pasos. |
| [Arquitectura](docs/architecture/overview.md) | Vista general del sistema. |
| [Mapa de repos](docs/architecture/repo-map.md) | Organización multi-repositorio. |
| [Límites de servicios](docs/architecture/service-boundaries.md) | Responsabilidades y datos. |
| [Flujo de transferencia](docs/architecture/transfer-flow.md) | Secuencia, idempotencia y fallos. |
| [Ledger](docs/domain/ledger.md) | Modelo contable de doble entrada. |
| [Contratos](docs/contracts/) | OpenAPI y eventos v0. |
| [ADRs](docs/adr/) | Decisiones de arquitectura. |
| [Operación](docs/operations/) | Desarrollo local, CI/CD, seguridad, observabilidad y testing. |
| [Gobierno](docs/governance/) | Organización y trabajo multi-repo. |

## Cómo se trabaja

- Reglas humanas: [`CONTRIBUTING.md`](CONTRIBUTING.md).
- Reglas para agentes de IA: [`AGENTS.md`](AGENTS.md).
- Decisiones abiertas: [`docs/decisions-pending.md`](docs/decisions-pending.md).

Flujo resumido:

1. La IA o el humano propone un plan.
2. El plan se revisa y ajusta.
3. Se ejecuta en cambios pequeños.
4. Se verifica.
5. Se resume qué se hizo, qué se decidió y qué queda pendiente.

## Estado actual

- [x] Documentación fundacional.
- [x] Protocolo de IA.
- [x] Arquitectura y límites de servicios.
- [x] Contratos v0.
- [x] ADRs iniciales.
- [x] Decisiones fundacionales (ADR-0008 a ADR-0011).
- [ ] Crear la organización y los repositorios.
- [ ] Walking skeleton en kind.
- [ ] Ledger y transferencias.
- [ ] Frontend y backoffice.
- [ ] Plataforma production-like.

## Próximo paso

Cerrar las decisiones pendientes (nombre de la org, moneda, stack y nube), crear la organización y
empezar por el **walking skeleton**: dos servicios vacíos conectados, desplegados en kind vía
Argo CD, con CI, métricas, logs y trazas.

## No objetivos

- Operar como banco real.
- Procesar pagos o tarjetas reales.
- Usar datos personales reales.
- Certificar PCI-DSS, SOC 2 o ISO 27001 en esta etapa.
