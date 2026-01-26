# Paved Paths Knowledge Base

## Project Objective
Extract a contextual definition of "Paved Paths" from documentation and template repositories to enable automated onboarding of non-compliant repos.

---

## ✅ ANALYSIS COMPLETE

### What are Paved Paths?
**Status:** ✅ DEFINED

**Paved Paths** is Alight's standardized software development framework that provides:
- **Pre-configured CI/CD pipelines** via GitHub Actions with reusable workflows
- **Standardized project structures** with required configuration files
- **Automated container builds** and deployment to AWS ECS Fargate
- **Consistent tooling** for versioning (Commitizen), commits (conventional), and security (detect-secrets, Checkmarx)
- **Developer experience** via VS Code devcontainers with pre-configured environments

### Core Principles
1. **Trunk-based Development** - Single `main` branch, short-lived feature branches
2. **Semantic Versioning** - Enforced via Commitizen with conventional commit messages
3. **Security by Default** - Pre-commit hooks for secrets detection, Checkmarx scanning
4. **Infrastructure as Code** - Terraform-based deployments via AWS CodePipeline

---

## Paved Path Types

| Type | Template | Language | Runtime |
|------|----------|----------|---------|
| `SpringbootAPI` | dx-template-springboot | Java | Container (Fargate) |
| `PythonLambda` | dx-template-python | Python | Lambda |
| `AngularApp` | dx-template-angular | TypeScript | Static (S3/CloudFront) |
| `Docker` | dx-template-docker | Any | Container (Fargate) |
| `HTML` | dx-template-html | HTML | Static |
| `JavaApp` | dx-template-java | Java | Container (Fargate) |

---

## Required Files for Compliance

Every Paved Path repository **MUST** contain:

| File | Purpose | Can Modify? |
|------|---------|-------------|
| `.alit.json` | Paved path config (artifact name, BSN, type) | Values only |
| `.cz.json` | Commitizen semantic versioning | ❌ No |
| `.pre-commit-config.yaml` | Pre-commit hooks | ❌ No |
| `Makefile` | Standard dev commands | ❌ No |
| `.github/workflows/init.yml` | Repo initialization | ❌ No |
| `.github/workflows/pr.yml` | PR validation | ❌ No |
| `.github/workflows/push.yml` | Branch push | ❌ No |
| `.github/workflows/publish.yml` | Main branch publish | ❌ No |
| `.devcontainer/` | VS Code devcontainer | Limited |
| `README.md` | Project documentation | ✅ Yes |

---

## Key Configuration Details

### `.alit.json` Structure
```json
{
    "build": {
        "artifact": {
            "name": "<lowercase-artifact-name>",
            "BusinessServiceName": "<BSN from ServiceNow>"
        },
        "paved_path": "<SpringbootAPI|PythonLambda|AngularApp|Docker|HTML|JavaApp>"
    }
}
```

**Validation Rules:**
- `artifact.name` MUST be lowercase (becomes ECR repo name)
- `paved_path` MUST match a defined type

### CI/CD Architecture
- **Platform:** GitHub Actions
- **Central Workflows:** `AlightEngineering/dx-automation` repository
- **Pattern:** Local workflows call reusable workflows from dx-automation

### Container Registry
- **Registry:** AWS ECR in `alight-prod-devops` account
- **URI:** `755600509381.dkr.ecr.us-east-1.amazonaws.com`

### Infrastructure Deployment
- **IaC Tool:** Terraform
- **Template:** `dx-template-tfsolution-fargate`
- **Pipeline:** AWS CodePipeline (triggered from CodeCommit)
- **State Account:** `alight-prod-infra-state`

---

## Onboarding Checklist

To make a repo Paved Path compliant:

- [ ] Add `.alit.json` with valid `paved_path` type and lowercase `artifact.name`
- [ ] Add `.cz.json` with commitizen configuration
- [ ] Add `.pre-commit-config.yaml` with required hooks
- [ ] Add `Makefile` with standard targets (init, build, test, pr)
- [ ] Add `.github/workflows/` with 4 workflow files
- [ ] Add `.devcontainer/` configuration
- [ ] Ensure branching follows trunk-based development
- [ ] Run `detect-secrets scan` to verify no secrets in code
- [ ] Publish container image to approved ECR registry

---

## Artifacts Produced

| Artifact | Location | Description |
|----------|----------|-------------|
| `architecture_context.md` | results/ | Human-readable, audit-friendly documentation |
| `architecture_context.json` | results/ | Structured data for automation |
| `knowledge.md` | results/ | This file - project knowledge base |
| `inventory.md` | results/ | Complete repository inventory with descriptions |
| `copilot-instructions.md` | results/ | Copilot code review instructions |

---

## Tasks

### Completed
- [x] Task 0: Created initial knowledge.md
- [x] Task 1: Read documentation under /pages/*
- [x] Task 2: Read manifest files
- [x] Task 2a: Clone supporting tool repositories (dx-tools/)
- [x] Task 3: Clone template repositories
- [x] Task 4: Analyze template repo structures
- [x] Task 5: Analyze dx-tools repos (dx-automation, dx-cli, dx-release-management, etc.)
- [x] Task 6: Write architecture_context.md
- [x] Task 7: Write architecture_context.json
- [x] Task 8: Create inventory.md
- [x] Task 9: Update knowledge.md with findings

---

## Release Management Service (RMS)

A key discovery from analyzing dx-tools repositories:

| Aspect | Details |
|--------|---------|
| **Repository** | dx-release-management |
| **Technology** | FastAPI (Python) |
| **Purpose** | Quality gates, JIRA/ServiceNow integration |

### RMS Capabilities
- Injects quality gates into deployment process
- Updates JIRA tickets with BuildArtifacts and DeployStatus
- Validates ServiceNow change requests
- Enables natural-language queries for release metrics

### Quality Gates
| Gate | Description |
|------|-------------|
| SNOW Change Compliance | Validates ServiceNow change request is approved |
| BSN Approver Validation | Validates deployer has approval rights |

---

## Evidence Log

| Claim | Source | Confidence |
|-------|--------|------------|
| 6 active paved path types | Template `.alit.json` files | High |
| GitHub Actions is CI/CD platform | Workflow files in templates | High |
| Reusable workflows in dx-automation | Workflow `uses:` declarations | High |
| ECR URI is 755600509381.dkr.ecr.us-east-1.amazonaws.com | Makefile `ecr-login` target | High |
| Trunk-based development required | Workflow triggers, documentation | High |
| Commitizen enforces semantic versioning | `.cz.json`, pre-commit hooks | High |
| detect-secrets prevents credential leaks | `.pre-commit-config.yaml` | High |
| Fargate is deployment target | `dx-template-tfsolution-fargate` | High |
| Terraform via CodePipeline | tfsolution-fargate README | High |
| Zscaler cert handling in devcontainers | devcontainer.json env vars | High |
| RMS integrates with JIRA/SNOW | dx-release-management README | High |
| dx-cli creates repos from templates | dx-cli README | High |
| 19 supporting tool repos | repositories.manifest.json | High |
| Quality gates block non-compliant deploys | dx-release-management architecture.md | High |

---

## Repository Summary

### Template Repos (7 cloned)
Located in `/repositories/`:
- dx-template-springboot
- dx-template-python
- dx-template-angular
- dx-template-docker
- dx-template-html
- dx-template-java
- dx-template-tfsolution-fargate

### Supporting Tool Repos (19 cloned)
Located in `/repositories/dx-tools/`:

| Category | Repositories |
|----------|--------------|
| **CI/CD** | dx-automation, dx-action-runner |
| **Developer Tools** | dx-cli, dx-userscripts, dx-developer-doc |
| **Services** | dx-release-management, dx-release-metrics-dx-service-image |
| **Base Images** | dx-alpine, dx-node, dx-python, dx-distroless, dx-base-fastapi, dx-containers |
| **Libraries** | dx-lib-java, dx-mcpcommonshared-util |
| **Infrastructure** | dx-tf-alight-provider, dx-vms |
| **Examples** | spark-dx-springboot, data-platform-kafka-admin-layer |

---

## Open Questions (Resolved)

| Question | Answer |
|----------|--------|
| Minimum requirements for compliance? | 10 required files/folders - see checklist |
| What CI/CD platform? | GitHub Actions |
| What are security gates? | detect-secrets, Checkmarx, pre-commit hooks |
| How do templates differ? | Same structure, different `paved_path` type in `.alit.json` and language-specific workflows |
| What is RMS? | Release Management Service - quality gates and JIRA/SNOW integration |
| Where are reusable workflows? | dx-automation repository |
| How do quality gates work? | RMS validates SNOW change requests and BSN approvals before deployment |

---

*Last Updated: Auto-generated*
*Status: ✅ COMPLETE - Architecture context extracted and documented*
