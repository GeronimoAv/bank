# ADR-0008 — Gobierno de repositorios y nombres

- **Estado:** Aceptado
- **Fecha:** 2026-11-13
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [ADR-0001](0001-polyrepo.md), [Mapa de repositorios](../architecture/repo-map.md), [Gobierno de la organización](../governance/org-setup.md), [Trabajo multi-repo](../governance/multi-repo-workflow.md)

## Contexto

ADR-0001 adoptó una organización multi-repositorio, pero dejó abiertos el nombre de la
organización, los nombres definitivos de los servicios, la visibilidad de los repos, la licencia y
el dominio web. Sin esos valores no se pueden crear los repositorios ni configurar `CODEOWNERS`,
rulesets y enlaces.

## Decisión

| Decisión | Valor |
| --- | --- |
| D-01 — Nombre de la organización | `vela-bank` |
| D-06 — Nombres de servicios | `core-banking-service` y `transfers-service` |
| D-15 — Visibilidad de repos | Privados al inicio; públicos solo si el proyecto se usa como portfolio |
| D-16 — Licencia | Apache-2.0 |
| D-17 — Dominio web | Solo local al inicio (`*.local`); sin dominio público |

Convenciones derivadas:

- Repos de gobierno y documentación: `.github`, `docs`, `contracts`.
- Repos de plataforma: `platform`, `infra`, `gitops`, `service-template`.
- Repos de servicios: `core-banking-service`, `transfers-service`.
- Repos de frontend: `web` y, cuando se justifique, `admin`.
- Nombres en kebab-case; servicios con sufijo `-service`; tags `v<major>.<minor>.<patch>`.

## Consecuencias

### Positivas

- Los nombres quedan estables para contratos, imágenes, Helm charts y URLs.
- La licencia Apache-2.0 permite publicar el proyecto sin reescribir avisos legales.
- Repos privados reducen el riesgo de exponer configuración antes de tiempo.

### Negativas

- Si el proyecto se publica, hay que revisar visibilidad, licencia y datos de ejemplo.
- `admin` como repo separado queda postergado; el backoffice vive en `web` al inicio.

### Mitigaciones

- Revisar visibilidad y licencia antes de hacer públicos los repos.
- Mantener `CODEOWNERS` desde el primer repo, aunque los equipos sean placeholders.

## Alternativas consideradas

1. **`vela-bank-labs`.** Válido si se prevén varios proyectos bajo la misma organización.
2. **`accounts-service` + `transfers-service`.** Más acotado, pero `core-banking-service` refleja
   mejor el alcance (clientes, cuentas, ledger y saldos).
3. **Repos públicos desde el inicio.** Útil como portfolio, pero expone decisiones y configuración
   antes de que el MVP esté estable.

## Notas

Este ADR cierra D-01, D-06, D-15, D-16 y D-17 de
[decisiones pendientes](../decisions-pending.md).
