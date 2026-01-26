# Copilot Instructions for Paved Path Code Reviews

> **Purpose**: These instructions guide GitHub Copilot when performing code reviews on repositories that should comply with Alight's Paved Paths standards.

---

## Review Context

You are reviewing code for compliance with Alight's **Paved Paths** framework - a standardized development approach requiring specific configuration files, CI/CD patterns, and coding standards.

---

## Required Files Checklist

When reviewing a repository, verify these files exist and are properly configured:

### 1. `.alit.json` (REQUIRED)
**Must contain:**
```json
{
    "build": {
        "artifact": {
            "name": "<lowercase-only>",
            "BusinessServiceName": "<non-empty-string>"
        },
        "paved_path": "<valid-type>"
    }
}
```

**Validation rules:**
- `build.artifact.name` MUST be lowercase letters, numbers, and hyphens only
- `build.artifact.name` MUST NOT contain uppercase letters (this becomes the ECR repo name)
- `build.paved_path` MUST be one of: `SpringbootAPI`, `PythonLambda`, `AngularApp`, `Docker`, `HTML`, `JavaApp`
- `build.artifact.BusinessServiceName` MUST NOT be a placeholder like "CHANGE THIS"

**Flag as non-compliant if:**
- File is missing
- `artifact.name` contains uppercase characters
- `paved_path` value is not in the allowed list
- Values still contain placeholder text

---

### 2. `.cz.json` (REQUIRED)
**Must contain commitizen configuration with:**
- `commitizen.name` = `cz_customize`
- `commitizen.version_scheme` = `semver2`
- `commitizen.tag_format` = `$version`

**Flag as non-compliant if:**
- File is missing
- Does not use `cz_customize` as the commitizen name
- Version scheme is not `semver2`

---

### 3. `.pre-commit-config.yaml` (REQUIRED)
**Must include these hooks:**
- `check-yaml` (from pre-commit-hooks)
- `end-of-file-fixer` (from pre-commit-hooks)
- `trailing-whitespace` (from pre-commit-hooks)
- `commitizen` (from commitizen-tools)
- `detect-secrets` (from Yelp/detect-secrets)

**Flag as non-compliant if:**
- File is missing
- Missing any of the required hooks
- `detect-secrets` hook is disabled or removed

---

### 4. `Makefile` (REQUIRED)
**Must define these targets:**
- `init` - Initialize development environment
- `build` - Build the application
- `test` - Run tests
- `pre-commit` - Run pre-commit hooks
- `pr` - Create pull request
- `push` - Push to feature branch

**Flag as non-compliant if:**
- File is missing
- Missing required targets
- Contains hardcoded credentials or secrets

---

### 5. `.github/workflows/` (REQUIRED)
**Must contain exactly these 4 workflow files:**
- `init.yml` - Repository initialization
- `pr.yml` - Pull request validation
- `push.yml` - Feature branch push
- `publish.yml` - Main branch publish

**Each workflow must:**
- Use reusable workflows from `AlightEngineering/dx-automation`
- Include `secrets: inherit`
- NOT contain hardcoded secrets or credentials

**Flag as non-compliant if:**
- Any of the 4 workflow files are missing
- Workflows don't reference `AlightEngineering/dx-automation`
- Workflows contain inline secret values

---

### 6. `.devcontainer/` (REQUIRED)
**Must contain:**
- `devcontainer.json` with proper configuration

**Flag as non-compliant if:**
- Directory is missing
- `devcontainer.json` is missing

---

### 7. `README.md` (REQUIRED)
**Should document:**
- Project purpose
- How to run locally
- Links to Paved Path documentation

**Flag as non-compliant if:**
- File is missing
- Contains only template placeholder text

---

## Code Quality Rules

### Secrets and Credentials
**CRITICAL: Flag immediately if you detect:**
- Hardcoded API keys, tokens, or passwords
- AWS access keys or secret keys
- Database connection strings with credentials
- Private keys or certificates
- Any string matching patterns like:
  - `AKIA[0-9A-Z]{16}` (AWS access key)
  - `password\s*=\s*["'][^"']+["']`
  - `secret\s*=\s*["'][^"']+["']`
  - `token\s*=\s*["'][^"']+["']`

**Recommendation:** Use environment variables or AWS Secrets Manager.

---

### Commit Messages
**Enforce conventional commit format:**
```
type(scope): description

body (optional)

footer (optional)
jira_ids: ABC-123
```

**Valid types:** `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`

**Flag as non-compliant if:**
- Commit messages don't follow conventional format
- Missing JIRA ID in footer for feature/fix commits

---

### Branching Strategy
**Enforce trunk-based development:**
- `main` is the only long-lived branch
- Feature branches: `feature/<jira-id>-description`
- Hotfix branches: `hotfix/<jira-id>-description`

**Flag as non-compliant if:**
- Repository has multiple long-lived branches (develop, staging, etc.)
- Branch names don't follow the pattern

---

### Docker/Container Rules
**For Dockerfiles, verify:**
- Base images are from approved registry: `755600509381.dkr.ecr.us-east-1.amazonaws.com`
- No `latest` tags used for base images
- Multi-stage builds preferred
- No secrets in build args or environment variables
- Health checks defined

**Flag as non-compliant if:**
- Using unauthorized base images
- Using `latest` tag
- Secrets exposed in Dockerfile

---

### Terraform Rules (for IaC repos)
**Verify:**
- Uses modules from `AlightEngineering/dx-template-terraform-modules-*`
- Variables have descriptions and type constraints
- No hardcoded values for environment-specific settings
- Uses `tfvars/` directory for environment configs

**Flag as non-compliant if:**
- Hardcoded AWS account IDs or ARNs
- Missing variable descriptions
- Not using approved Terraform modules

---

## Technology-Specific Rules

### Java/Spring Boot (`paved_path: "SpringbootAPI"`)
- Must use Gradle or Maven with approved plugins
- Spring Boot version should be current LTS
- Must include health check endpoint at `/actuator/health`
- No `System.out.println` for logging - use SLF4J

### Python (`paved_path: "PythonLambda"`)
- Must have `requirements.txt` or `pyproject.toml`
- No `print()` statements for logging - use `logging` module
- Lambda handler must follow AWS Lambda signature
- Type hints encouraged

### Angular (`paved_path: "AngularApp"`)
- Must use Angular CLI structure
- No inline styles or templates in components
- Environment files properly configured
- No hardcoded API URLs

### Docker (`paved_path: "Docker"`)
- Must have `Dockerfile` at repo root
- Must have `tests/` directory with container tests
- If external image, `.alit.json` must have `external_image` config

---

## Review Response Format

When reviewing code, structure your response as:

```markdown
## Paved Path Compliance Review

### ✅ Compliant Items
- [List items that pass validation]

### ❌ Non-Compliant Items
- [List items that fail validation with specific file:line references]

### ⚠️ Warnings
- [List items that are concerning but not blocking]

### 📋 Recommendations
- [Suggestions for improvement]

### Required Actions
1. [Numbered list of required fixes before merge]
```

---

## Quick Reference: Paved Path Types

| Type | Template | Key Files |
|------|----------|-----------|
| `SpringbootAPI` | dx-template-springboot | `build.gradle`, `application.yml` |
| `PythonLambda` | dx-template-python | `requirements.txt`, `handler.py` |
| `AngularApp` | dx-template-angular | `angular.json`, `package.json` |
| `Docker` | dx-template-docker | `Dockerfile`, `tests/` |
| `HTML` | dx-template-html | `index.html` |
| `JavaApp` | dx-template-java | `build.gradle`, `web.xml` |

---

## ECR Registry

All container images must be published to:
```
755600509381.dkr.ecr.us-east-1.amazonaws.com/{artifact.name}:{version}
```

Flag any references to other container registries as non-compliant unless explicitly whitelisted.

---

## Additional Resources

- Architecture Context: See `architecture_context.md` for full documentation
- Template Repos: `AlightEngineering/dx-template-*`
- Automation Workflows: `AlightEngineering/dx-automation`
