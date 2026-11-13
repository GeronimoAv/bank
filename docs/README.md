# Documentación — Vela Bank

Este directorio es la fuente de verdad del proyecto. Si una decisión no está acá, no está tomada.

## Estado

**Fase de documentación.** Todavía no hay código ejecutable. Las decisiones marcadas como
`[PROPUESTO]` están pendientes de confirmación humana.

## Índice

### Producto y planificación

- [Visión](vision.md)
- [Alcance del MVP](scope.md)
- [Roadmap](roadmap.md)
- [Plan de acción](action-plan.md)
- [Decisiones pendientes](decisions-pending.md)
- [Glosario](glossary.md)

### Arquitectura

- [Vista general](architecture/overview.md)
- [Mapa de repositorios de la organización](architecture/repo-map.md)
- [Límites de los servicios](architecture/service-boundaries.md)
- [Flujo de transferencia](architecture/transfer-flow.md)

### Dominio

- [Ledger de doble entrada](domain/ledger.md)

### Contratos

- [OpenAPI — Core Banking v0](contracts/openapi/core-banking-v0.yaml)
- [OpenAPI — Transfers v0](contracts/openapi/transfers-v0.yaml)
- [Eventos v0](contracts/events-v0.md)

### Decisiones de arquitectura (ADR)

- [ADR-0001 — Organización multi-repositorio](adr/0001-polyrepo.md)
- [ADR-0002 — Límites de los servicios](adr/0002-service-boundaries.md)
- [ADR-0003 — Ledger de doble entrada](adr/0003-ledger.md)
- [ADR-0004 — Contratos e integración](adr/0004-contracts-and-integration.md)
- [ADR-0005 — Plataforma local y Floci](adr/0005-local-platform-and-floci.md)
- [ADR-0006 — GitOps y entornos](adr/0006-gitops-and-environments.md)
- [ADR-0007 — Colaboración con IA](adr/0007-ai-collaboration.md)
- [ADR-0008 — Gobierno de repositorios y nombres](adr/0008-repo-governance-and-naming.md)
- [ADR-0009 — Producto, moneda y operación simulada](adr/0009-product-currency-and-simulation.md)

### Operación

- [Desarrollo local](operations/local-development.md)
- [CI/CD](operations/ci-cd.md)
- [Seguridad](operations/security.md)
- [Observabilidad](operations/observability.md)
- [Estrategia de testing](operations/testing.md)

### Gobierno

- [Configuración de la organización](governance/org-setup.md)
- [Trabajo multi-repositorio](governance/multi-repo-workflow.md)

### Protocolo de IA

- [AGENTS.md](../AGENTS.md)

## Cómo se mantiene

- Todo documento nuevo se enlaza desde este índice.
- Toda decisión durable se registra en un ADR.
- Toda decisión abierta se registra en [decisions-pending.md](decisions-pending.md).
- Los cambios de arquitectura actualizan el ADR correspondiente y, si aplica, el mapa de
  repositorios.
- Los contratos se versionan y no se rompen sin un plan de migración.
