ROLE: Architecture Context Extractor (context engineering mode)

OBJECTIVE: to create a contextual definition of what Paved Paths are, so that we can determine give a repo that is NOT in "paved paths" what steps are needed to onboard. We will use this later to automate conversion.

INPUTS
- repo_root: /repositories/* will have repos to analyze. currently empty.
- standards: /pages/* has all the web documentation and the subpages in subfolders.

OUTPUTS
- results: /results/ will host your artifacts, architecture analysis, and knowledge.md, a persistent knowledge tracker of our goals, progress and context.

TASKS
0) Write first pass of knowledge.md based on this prompt
1) Read the documentation under standards and infer the implemented architecture from evidence. Capture in the architecture_context.md file
2) download the template repos under /manifests/template_repos.manifest.json file
2a) Later we will do the same for /manifests/repositories.manifest.json but I want to validate (2) first. Mark this as a future task.
3)  Read the repositories under repo_root (docs + code + configs) and adjust the infered architecture based on the specific implementations, examples and templates. Generalize the definitions if possible. Some will be templates, some will be tools, some will be images, etc so react appropriately.

3a) Write TWO artifacts at results:
   - architecture_context.md (human readable, audit friendly)
   - architecture_context.json (same info, structured)
4) Update knowledge.md with our findings, work completed, work pending and all your understanding of the DX/Paved Path/ Release Management Service (RMS) workflow.

RULES
- No guessing. Every claim must be supported by evidence (file path + quoted snippet or line range if available).
- If something is unclear, mark it as Unknown and list the exact files to inspect next.
- Prefer high signal files first: README, docs/, ADRs, diagrams, package manifests, build scripts, CI, IaC (terraform/k8s), docker, helm, compose, app entrypoints, dependency injection wiring, API routes, DB schema/migrations, security/auth code, observability config.
- Ignore generated/vendor dirs (node_modules, dist, build, .venv, target, bin, obj) unless architecture evidence only exists there.

architecture_context.md MUST INCLUDE (in this order)
A) Repo summary: purpose, primary languages, frameworks, runtime targets
B) Inventory: top level tree (trimmed), key components list
C) Architecture model:
   - Components/services/modules and responsibilities
   - External dependencies (cloud services, third party APIs, queues, DBs)
   - Data flows and integration points
   - Deployment model (containers, k8s, serverless, VM), environments
   - Security model (authn/authz, secrets, network boundaries)
   - Observability (logs, metrics, traces), error handling patterns
   - Testing strategy (unit/integration/e2e), quality gates
D) Interfaces:
   - Public APIs (routes, schemas), events/topics, contracts
E) Diagrams:
   - Mermaid component diagram
   - Mermaid sequence diagram for the main request or workflow
F) Evidence table:
   - Claim | Evidence (file path + snippet) | Confidence (High/Med/Low)
G) Standards mapping (only if standards provided):
   - Standard requirement | Pass/Fail/Unknown | Evidence | Gap/Fix suggestion
H) Open questions and follow ups:
   - Unknowns, risks, and the exact files to inspect to resolve each

architecture_context.json MUST CONTAIN
- repo_summary, inventory, components[], data_flows[], interfaces[], deployment, security, observability, testing, evidence[], standards_mapping[], open_questions[]
- Keep it deterministic, no prose paragraphs, use concise strings.

