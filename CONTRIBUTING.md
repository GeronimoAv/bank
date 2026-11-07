# Contributing

This repository follows a **bank-like trunk-based** workflow: small changes, short-lived
branches, a protected `main`, and a controlled promotion path from staging to production.

## 1. Principles

- **Small and reversible.** One concern per change. Prefer many small PRs over one large one.
- **Human approval.** A person reviews and approves every change before it reaches `main`.
- **`main` is always green.** Every commit must build, pass tests, and be deployable.
- **Tests are the specification.** Behaviour changes ship with tests.
- **No secrets, no real PII.** Use `.env.example` and synthetic data only.
- **AI assists, humans decide.** See section 11.

## 2. Branching model

```text
main ──●──●──●──●──●──●──●──►   protected, deployable to staging
        \        \
         feat/S01  release/0.1 ──► tag v0.1.0 ──► production
                      \
                       hotfix/0.1.1 ──► tag v0.1.1 ──► production
```

| Branch | Purpose | Lifetime |
| --- | --- | --- |
| `main` | Integration branch; always green | Permanent |
| `feat/<slice>-<slug>` | New capability, e.g. `feat/S01-accounts-domain` | ≤ 2 days |
| `fix/<slug>` | Bug fix | ≤ 2 days |
| `docs/<slug>` | Documentation only | ≤ 2 days |
| `chore/<slug>` | Tooling, dependencies, refactors without behaviour change | ≤ 2 days |
| `ci/<slug>` | Pipeline and automation changes | ≤ 2 days |
| `release/<major.minor>` | Stabilisation for a release | Until tagged |
| `hotfix/<major.minor.patch>` | Emergency fix from a release tag | Until tagged |

Rules:

1. `main` is protected. No direct pushes.
2. Branch from an up-to-date `main` and rebase before opening the PR.
3. One branch = one PR = one concern.
4. If a branch lives longer than two days, split the work.
5. Branches are preserved after merge for traceability; do not delete them.

## 3. Commits

Use [Conventional Commits](https://www.conventionalcommits.org/):

```text
<type>(<scope>): <description>
```

| Type | Use |
| --- | --- |
| `feat` | New behaviour |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `test` | Tests only |
| `refactor` | Internal change without behaviour change |
| `chore` | Tooling, dependencies, housekeeping |
| `ci` | CI/CD changes |
| `perf` | Performance improvement |
| `build` | Build system changes |

Examples:

```text
feat(accounts): add hold aggregate
fix(transfers): release hold when posting fails
docs(adr): record ledger ownership decision
ci: add integration test stage
```

Rules:

- Imperative mood, lower case, no trailing period, ≤ 72 characters.
- The body explains **why**, not what; wrap at 72 characters.
- Reference the issue in the footer when one exists: `Refs #12`.
- Do not add AI attribution trailers. The human author is accountable for the change.
- Signed commits are recommended once the remote is configured.

## 4. Pull requests

Every change goes through a pull request, including documentation.

**Size limit:** ≤ 300 changed lines and ≤ 5 files, excluding lockfiles and generated code.
If the change is larger, split it.

**Title:** follows Conventional Commits, because the squash merge uses it as the commit on
`main`.

**Description template:**

```markdown
## What
A short description of the change.

## Why
The problem it solves and the decision behind it.

## How verified
Commands run and their result, e.g. `make verify` or `markdownlint`.

## Risk and rollback
What can go wrong and how to revert.

## Checklist
- [ ] Acceptance criteria met
- [ ] Tests added or updated
- [ ] Documentation updated
- [ ] No secrets or real PII
```

**Review and merge:**

| Change type | Merge strategy |
| --- | --- |
| `feat`, `fix`, `docs`, `chore`, `ci`, `refactor` | **Squash merge** |
| `release` and `hotfix` backports | Merge commit or cherry-pick, then tag |

- At least one approval is required.
- CI must be green and all conversations resolved.
- Do not force-push a shared branch.
- Branches are preserved after merge; do not delete them.

## 5. Pull request workflow

Every change is integrated through a real pull request. The same steps apply to documentation
and code:

```bash
git switch main
git pull --ff-only
git switch -c docs/working-agreement
# edit files
git add CONTRIBUTING.md
git commit -m "docs: add contribution and branching guidelines"
git push -u origin docs/working-agreement
```

Then complete the integration in the platform:

1. Open a pull request against `main` and reference the related issue.
2. Wait for the required checks and at least one approval.
3. Use **squash merge** from the platform; do not integrate locally with `git merge`.
4. Preserve the branch after merge; do not delete it.

The platform records the PR, the review, the checks, and the merge as the audit trail for the
change.

## 6. CI/CD and promotion

The pipeline is designed around **build once, promote many**:

```text
Pull request
  format → lint → unit → integration → contract → SAST → dependency scan → build

main (merge)
  build immutable image → SBOM → image scan → push to ECR
  → update GitOps manifests → Argo CD syncs staging

production
  manual approval → Argo CD syncs the same image digest → smoke tests
```

Rules:

- The image is built once and promoted by digest. Never rebuild for production.
- Staging deploys automatically from `main`.
- Production requires an explicit approval and a recorded deployment.
- Use rolling deployments by default. Canary is reserved for `transfers-service` once the
  observability stack is in place.
- Use feature flags to separate deploy from release.
- Rollback means redeploying the previous digest; database migrations must be backward
  compatible (expand/contract).

## 7. Releases

- Versioning follows [Semantic Versioning](https://semver.org/).
- Cut `release/<major.minor>` from `main` when the milestone is ready.
- Only fixes enter a release branch; no new features.
- Tag the release as `v<major.minor.patch>` and update `CHANGELOG.md`.
- Hotfixes branch from the release tag as `hotfix/<major.minor.patch>`, are tagged, and are
  backported to `main`.
- A release is not complete until the tag, changelog, and deployment record exist.

## 8. Issues and slices

- One issue per unit of work (a "slice"), with acceptance criteria and a test plan.
- Link the PR with `Closes #<issue>`.
- Suggested labels: `type:*`, `area:*`, `priority:*`, `status:*`.
- A slice is ready when its scope, acceptance criteria, and verification steps are written.

## 9. Verification

Documentation changes:

- `markdownlint` and link checks (once the tooling exists).
- Human review for accuracy and scope.

Code changes:

- `make verify` locally: format, lint, unit, integration, contract, and architecture tests.
- CI must be green before merge.
- Every PR states how it was verified and includes the command output when relevant.

## 10. Security

- Never commit secrets. `.env.example` documents required variables.
- Report vulnerabilities privately to the maintainer; do not open a public issue.
- Run secret scanning and dependency scanning in CI.
- Use synthetic data only. This project must never contain real customer data.

## 11. AI-assisted work

AI is a tool, not the author. The human remains accountable for every change.
c/cccc
