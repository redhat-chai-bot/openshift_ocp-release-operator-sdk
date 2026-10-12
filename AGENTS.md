# ocp-release-operator-sdk

| Field | Value |
|-------|-------|
| **Component** | Operator SDK (downstream) |
| **Repository** | `openshift/ocp-release-operator-sdk` |
| **Upstream** | [`operator-framework/operator-sdk`](https://github.com/operator-framework/operator-sdk) |
| **Upstream version** | See [`UPSTREAM-VERSION`](UPSTREAM-VERSION) |
| **Primary deliverable** | Helm Operator image (`release/helm/`) |
| **Default branch** | `main` |

## Purpose

Downstream mirror of the Operator SDK that builds the **Helm Operator** image for OpenShift releases. Upstream tags are merged (not rebased) via [`UPSTREAM-MERGE.sh`](UPSTREAM-MERGE.sh), with downstream patches applied during CI builds.

## Critical Warnings

1. **NEVER edit upstream source directly** — apply changes as patches in `patches/` using the `gendiff` workflow (see [README.md](README.md#patching))
2. **NEVER run downstream builds without patching first** — use `make -f ci/prow.Makefile patch build` for downstream/CI builds; plain `make build` is fine for upstream-compatible local development (see [`ai-docs/DEVELOPMENT.md`](ai-docs/DEVELOPMENT.md))
3. **NEVER remove the `vendor/` directory** — downstream vendors dependencies (upstream does not)
4. **NEVER hand-edit generated testdata** — run `make generate` to regenerate
5. **ALWAYS use build tag `containers_image_openpgp`** — required for all Go builds

## Architecture at a Glance

```
cmd/                    # Entrypoints: operator-sdk, helm-operator
internal/               # Core packages: helm controller, OLM, plugins, scorecard
patches/                # Downstream-only patches (applied sequentially)
release/helm/           # Helm Operator Dockerfile + scripts
ci/prow.Makefile        # Downstream CI build (patch → build)
hack/auto-rebase.sh     # Automated upstream rebase periodic (OAPE-829)
UPSTREAM-MERGE.sh       # Manual upstream merge script
```

## Documentation

| Need | Start Here |
|------|-----------|
| Architecture & internals | [`ai-docs/ARCHITECTURE.md`](ai-docs/ARCHITECTURE.md) |
| Build, patch, rebase | [`ai-docs/DEVELOPMENT.md`](ai-docs/DEVELOPMENT.md) |
| Testing | [`ai-docs/TESTING.md`](ai-docs/TESTING.md) |
| Design proposals | [`ai-docs/ENHANCEMENTS.md`](ai-docs/ENHANCEMENTS.md) |
| Upstream docs | [sdk.operatorframework.io](https://sdk.operatorframework.io) |
| Platform conventions | [openshift/enhancements](https://github.com/openshift/enhancements) `dev-guide/`, `CONVENTIONS.md` |

## Key Files

| File | Purpose |
|------|---------|
| [`UPSTREAM-VERSION`](UPSTREAM-VERSION) | Current upstream tag tracked |
| [`Makefile`](Makefile) | Primary build (use for local dev) |
| [`ci/prow.Makefile`](ci/prow.Makefile) | CI build (patch + build) |
| [`.ci-operator.yaml`](.ci-operator.yaml) | CI builder image pin |
| [`release/helm/Dockerfile`](release/helm/Dockerfile) | Helm Operator image |
