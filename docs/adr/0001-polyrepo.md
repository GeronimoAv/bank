# ADR-0001 — Organización multi-repositorio

- **Estado:** Aceptado
- **Fecha:** 2026-11-09
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [Mapa de repositorios](../architecture/repo-map.md), [Trabajo multi-repo](../governance/multi-repo-workflow.md)

## Contexto

El proyecto busca parecerse a un entorno bancario real: equipos con ownership, ciclos de vida
independientes, auditoría de cambios, pipelines por servicio y despliegues desacoplados. Un
monorepo simplifica el desarrollo inicial, pero no ejercita esas prácticas.

## Decisión

Usar una organización de GitHub con **múltiples repositorios**:

- repos de gobierno y documentación (`.github`, `docs`, `contracts`);
- repos de plataforma (`platform`, `infra`, `gitops`, `service-template`);
- repos de servicios (`core-banking-service`, `transfers-service`);
- repos de frontend (`web`, y `admin` cuando se justifique).

Cada repo tiene owner, `CODEOWNERS`, pipeline propio y ciclo de vida independiente. Los contratos
viven en `contracts`; los despliegues se declaran en `gitops`.

## Consecuencias

### Positivas

- Ownership y permisos por dominio.
- Pipelines y releases independientes.
- Historial de cambios más auditable.
- Se practica versionado de contratos y trabajo cross-repo.
- Se parece más a una organización real.

### Negativas

- Más overhead de coordinación.
- Riesgo de deriva de contratos y versiones.
- CI duplicado si no se usan workflows reutilizables.
- Entorno local más complejo.
- Cambios cross-repo requieren orden y compatibilidad.

### Mitigaciones

- `contracts` como fuente de verdad y contract testing.
- Reusable workflows en `.github` y `service-template`.
- `platform` con `make dev-up` y scripts de clonado.
- GitOps como punto de integración.
- ADRs y `decisions-pending.md` para evitar decisiones implícitas.
- Compatibilidad hacia atrás y feature flags.

## Alternativas consideradas

1. **Monorepo único.** Más simple al inicio, pero no ejercita ownership, pipelines independientes
   ni promoción por servicio.
2. **Híbrido con monorepo para servicios.** Válido si el equipo es pequeño, pero diluye el objetivo
   de aprender organización multi-repo.
3. **Un repo por capa técnica.** Rechazado: genera acoplamiento y no representa dominios.

## Notas

El nombre de la organización (`vela-bank`) y la visibilidad de los repos están pendientes de
confirmación. Ver [decisiones pendientes](../decisions-pending.md).
