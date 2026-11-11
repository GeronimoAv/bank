# Configuración de la organización

> Organización propuesta: `vela-bank` `[PROPUESTO]`.

## 1. Crear la organización

1. Crear la org desde la UI de GitHub.
2. Elegir plan (Free/Team según necesidades de rulesets y permisos).
3. Configurar autenticación de dos factores obligatoria.
4. Invitar miembros y crear equipos.
5. Configurar políticas de seguridad.

## 2. Equipos

| Equipo | Repos | Responsabilidad |
| --- | --- | --- |
| `platform` | `.github`, `docs`, `contracts`, `platform`, `service-template`, `gitops`, `infra` | Plataforma, CI/CD, documentación, contratos. |
| `core-banking` | `core-banking-service` | Dominio contable. |
| `payments` | `transfers-service` | Transferencias. |
| `frontend` | `web`, `admin` | Experiencia de cliente y backoffice. |
| `security` | Revisión transversal | Cambios sensibles. |

En un proyecto unipersonal, los equipos pueden ser placeholders, pero deben existir para que
`CODEOWNERS` y los permisos queden listos.

## 3. Repositorio `.github`

Contenido:

```text
.github/
├── profile/README.md
├── CONTRIBUTING.md
├── SECURITY.md
├── CODE_OF_CONDUCT.md
├── SUPPORT.md
├── ISSUE_TEMPLATE/
│   ├── bug.md
│   ├── feature.md
│   └── config.yml
├── PULL_REQUEST_TEMPLATE.md
└── .github/
    └── workflows/
        ├── java-service-ci.yml
        ├── node-service-ci.yml
        ├── docker-publish.yml
        ├── helm-publish.yml
        ├── security-scan.yml
        ├── gitops-update.yml
        └── docs-ci.yml
```

GitHub usa automáticamente los archivos de comunidad del repo `.github` de la organización cuando
un repo no tiene los propios.

## 4. Rulesets y branch protection

Para `main` de cada repo:

- PR obligatorio.
- Al menos una aprobación.
- Checks requeridos.
- Historial lineal.
- Commits firmados.
- Sin force push.
- Conversaciones resueltas.
- CODEOWNERS para áreas sensibles.
- Incluir administradores cuando sea posible.

Para producción:

- Environment `production`.
- Required reviewers.
- Wait timer.
- Restricción a tags/ramas de release.
- Secretos solo en ese environment.

> Si el plan de GitHub no permite org-level rulesets, aplicar por repo con Terraform.

## 5. Environments

| Environment | Reglas |
| --- | --- |
| `staging` | Deploy automático desde `main`. |
| `production` | Aprobación manual, wait timer, tags restringidos. |

Los secretos de AWS se gestionan con OIDC y roles, no con access keys.

## 6. Seguridad de la organización

- 2FA obligatorio.
- Secret scanning + push protection.
- Dependabot alerts y security updates.
- CodeQL.
- Dependency review.
- OpenSSF Scorecard.
- Actions allowlist.
- Pin actions por SHA.
- Permisos mínimos de `GITHUB_TOKEN`.
- Restringir creación de repos.
- Auditoría de acceso.

## 7. Org as code

Terraform con el provider de GitHub en `infra/github`:

- repos y visibilidad;
- equipos y memberships;
- rulesets y branch protection;
- labels;
- environments;
- secrets (nombres, no valores);
- topics y descripciones.

Ventajas:

- reproducible;
- auditable;
- menos drift;
- cambios por PR.

## 8. Labels

```text
type:feature
type:bug
type:docs
type:chore
type:security
area:core-banking
area:transfers
area:platform
area:frontend
priority:high
priority:medium
priority:low
status:ready
status:in-progress
status:blocked
```

## 9. GitHub Projects

Un solo Project para el roadmap:

- Backlog.
- Ready.
- In progress.
- In review.
- Done.
- Blocked.

Cada issue referencia el slice y el repo afectado.

## 10. Repository template

`service-template` debe incluir:

- Spring Boot + actuator.
- OpenTelemetry.
- Flyway.
- Testcontainers.
- ArchUnit.
- OpenAPI.
- Dockerfile distroless.
- Helm chart.
- CI calling reusable workflow.
- CODEOWNERS.
- README y runbook.
- Renovate.
- `catalog-info.yaml` (opcional).

## 11. Migración del repo actual

```bash
# Ejemplo conceptual; ejecutar con revisión humana
gh repo transfer GeronimoAv/bank vela-bank
gh repo rename docs --repo vela-bank/bank
git remote set-url origin git@github.com:vela-bank/docs.git
```

Pasos:

1. Crear org.
2. Transferir repo.
3. Renombrar a `docs`.
4. Mover `CONTRIBUTING.md` y `SECURITY.md` a `.github`.
5. Actualizar remotos y enlaces.
6. Crear los demás repos.

## 12. Solo developer

GitHub no permite autoaprobar un PR. Opciones:

- Usar una segunda cuenta/bot como reviewer.
- Configurar rulesets con bypass de administrador.
- Relajar la aprobación en repos de documentación, manteniendo checks.

Documentar la excepción en `CONTRIBUTING.md`.

## 13. Checklist

- [ ] Org creada.
- [ ] 2FA obligatorio.
- [ ] Equipos creados.
- [ ] `.github` creado con templates y workflows.
- [ ] `docs` migrado.
- [ ] Rulesets aplicados.
- [ ] Environments creados.
- [ ] Secret scanning y Dependabot activos.
- [ ] OIDC configurado.
- [ ] Projects y labels creados.
- [ ] Terraform de org aplicado.
- [ ] Repos iniciales creados.
