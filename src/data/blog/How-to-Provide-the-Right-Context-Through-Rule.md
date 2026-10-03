---
title: How to Provide the Right Context Through Rules, Skills, MCP, and Hooks, How to Evaluate Modularization with AI and Architecture Tools, and How to Use Deterministic Checks as Guardrails for Coding Agents
author: Sunny
pubDatetime: 2026-09-26T04:06:31Z
slug: How-to-Provide-the-Right-Context-Through-Rule
featured: false
draft: false
tags:
  - AI
  - Programming
  - Engineering
description:
  Building reliable AI coding workflows requires treating the model not as an omniscient engineer, but as a probabilistic reasoning engine bounded by deterministic guardrails, structured interfaces, and dynamic context injection.  
---


## 1. Structuring Context: Rules, Skills, MCP, and Hooks

Context windows decay in utility as irrelevant noise increases ("needle in a haystack" degradation). Context must be divided into persistent standards, on-demand domain playbooks, standardized data/tool interfaces, and event-driven automation.

```
+-----------------------------------------------------------------------+
| Context & Tooling Layer                                               |
|                                                                       |
|  [Rules (.cursorrules, CLAUDE.md)]   --> Always-on global constraints |
|  [Skills / Prompts]                  --> Task-activated procedures    |
|  [Model Context Protocol (MCP)]      --> Dynamic runtime data & APIs  |
|  [Hooks & Triggers]                  --> Deterministic phase gates    |
+-----------------------------------------------------------------------+

```

### Rules: Always-On Global Baselines

* **Purpose:** Set rigid, project-wide project constraints (e.g., `.cursorrules`, `CLAUDE.md`, or repository instructions).
* **Scope:**
* Tech stack definitions (e.g., "TypeScript 5.x strict mode, Tailwind CSS v4, Node 22").
* Negative constraints ("Never mutate database schema files directly; generate a migration using Prisma").
* File and directory naming conventions.


* **Best Practice:** Keep rules minimal and non-narrative. Every token in global rules applies to every turn; bloated rules crowd out prompt attention.

### Skills: Dynamic, Task-Specific Playbooks

* **Purpose:** Modular operational recipes loaded on demand rather than persisting permanently in context.
* **Scope:** Complex, multi-step domain tasks (e.g., "Add an authenticated Stripe checkout session", "Migrate a Next.js Pages router page to App router").
* **Implementation:** Expose skills via explicit trigger words, specialized slash commands, or selective agent tools (e.g., retrieving a `refactor-guidelines.md` or a skill instruction bundle only when editing API routes).

### Model Context Protocol (MCP): Standardized Data & Tool Bridges

* **Purpose:** An open standard to connect local or remote tools, resources, and live context directly to the LLM agent without custom bespoke scrapers.
* **Use Cases:**
* **Database Introspection:** Providing an MCP server that reads PostgreSQL live schema definitions and column types instead of pasting SQL dumps.
* **Issue Tracking & Observability:** Fetching live Sentry error stack traces or Linear tickets on demand.
* **Static Dependency Graphs:** Serving symbol definitions and call graphs via language-server protocol (LSP) integrations.


* **Best Practice:** Ensure MCP tools are read-heavy, schema-strict, and limit payloads via pagination or targeted summaries to prevent context bloat.

### Hooks: Event-Driven Pre/Post-Action Interceptors

* **Purpose:** Deterministic scripts executed immediately before an agent takes an action or after it modifies files.
* **Pre-action hooks:** Block destructive operations (e.g., intercepting a shell execution attempting `rm -rf` or editing protected paths like `.env*` or `.git/`).
* **Post-action hooks:** Trigger automated formatting, type-checking, or AST verification immediately upon file save, returning the error output straight back to the model context.

---

## 2. Evaluating Modularization with AI and Architecture Tools

When refactoring code or evaluating architectural modularity (monolith decomposition, bounded contexts, micro-frontends), human intuition often misses hidden cross-domain couplings. Combining deterministic dependency mapping with LLM semantic reasoning yields the best evaluation pipeline.

### Step 1: Deterministic Dependency Graph Extraction

Never rely on an LLM to guess the file tree or import chain. Use static analysis tools to generate objective dependency graphs:

* **Dependency Analysis:** Tools like `dependency-cruiser`, `Madge` (JS/TS), `ArchUnit` (Java), or `cargo-depgraph` (Rust).
* **Package Boundaries:** Tools like `Nx` or `Turborepo` boundary rules (e.g., `@nx/enforce-module-boundaries` to prevent circular dependencies between libs).

```bash
# Example: Generate an architectural violation report in JSON
npx depcruise --validate .dependency-cruiser.js src --output-type json > violations.json

```

### Step 2: AI-Assisted Architecture Evaluation

Feed the static analysis output (not the entire codebase) into the LLM alongside domain boundary descriptions.

* **Coupling & Cohesion Audits:** Supply the LLM with module exports, cross-module import counts, and folder structures. Prompt the model to identify:
* Low cohesion (modules doing multiple unrelated tasks).
* High coupling (two packages frequently importing private internals).
* Leaky abstractions (e.g., persistence layer schemas surfacing directly inside frontend components).


* **Bounded Context Verification (DDD):** Evaluate whether ubiquitous domain terms change meaning across modules (e.g., `User` as an identity concept vs. `Customer` as a billing entity).

---

## 3. Deterministic Checks as Guardrails for Coding Agents

LLMs generate plausible-looking code that frequently introduces subtle syntax errors, API hallucinations, or security regressions. **Do not use another LLM to review an LLM's code when a compiler or linter can do it deterministically.**

```
+---------------------------------------------------------------------------+
|                          Self-Healing Feedback Loop                       |
|                                                                           |
|   [ Agent Suggestion ]                                                    |
|           │                                                               |
|           ▼                                                               |
|   [ File Write / Git Stash ]                                              |
|           │                                                               |
|           ▼                                                               |
|   [ Deterministic Tooling: Biome / ESLint / tsc / Pyright / Tests ]       |
|           │                                                               |
|     ┌─────┴─────┐                                                         |
|   PASS        FAIL                                                        |
|     │           │                                                         |
|     │           └───> [ Feed Error Output to LLM ] ──> [ Auto-Correct ]   |
|     ▼                                                                     |
| [ Commit / Apply ]                                                        |
+---------------------------------------------------------------------------+

```

### 1. Static Type Checking & Linting (Fastest Gate)

Run strict type checkers as non-negotiable verification gates immediately after an agent edits code:

* TypeScript (`tsc --noEmit`), Python (`pyright` or `mypy --strict`), Rust (`cargo check`).
* **Linter/Formatter:** Run tools like `Biome`, `ruff`, or `ESLint` with fix flags enabled automatically, so the agent does not waste turns correcting formatting issues.

### 2. AST and Structural Linting

Enforce structural code constraints that standard linters might ignore:

* Prevent deprecated patterns or unauthorized library additions (e.g., using `ast-grep` or Semgrep rules to block raw `fetch` calls if an internal `apiClient` wrapper exists).
* Disallow mock bypasses or test skips (e.g., blocking `it.skip` or `jest.mock` on critical integration specs).

### 3. Isolated Automated Testing

* **Test Runners:** Run scoped unit tests relevant to the modified files (`npm test -- --findRelatedTests path/to/changedFile.ts`).
* **Self-Healing Loop:** If tests fail, feed only the failing test name and the stack trace back into the agent context, prompting: *"The changes introduced the following type/test failure. Resolve it without removing or skipping the test."*

### 4. Sandbox Isolation & Safety Permissions

* **Containerization:** Run the agent and its tools inside an ephemeral Docker container or MicroVM (e.g., via Dev Containers or e2b) to prevent local workspace contamination or unintended network calls.
* **Git Worktrees:** Have the agent write changes to an isolated Git worktree or branch. Only merge or apply to the main working tree if all deterministic checks pass cleanly.

Build an example setup for a self-healing CLI script that runs tsc and passes errors to the agent?

To make context management work reliably, implement a **hierarchical context pipeline** that segregates information by scope, lifecycle, and access mode.

```
┌─────────────────────────────────────────────────────────────┐
│                   Context Lifecycle Stack                   │
│                                                             │
│  [1. Static / Pinned]      Global rules, tech stack, types  │
│  [2. On-Demand Skills]     Procedural SOPs loaded per task  │
│  [3. Dynamic MCP Servers]  Live queries (DB, AST, git, PRs) │
│  [4. Ephemeral Feedback]   Compiler & linter errors         │
└─────────────────────────────────────────────────────────────┘

```

---

## 1. Directory Structure for Agent Configurations

Organize agent context directly in your repository so it lives alongside code and evolves via version control.

```text
.agent/
├── rules/
│   ├── base.md               # Immutable constraints & conventions
│   └── architecture.md       # Layering rules & prohibited cross-imports
├── skills/
│   ├── add-api-endpoint.md   # Step-by-step SOP with examples
│   ├── db-migration.md       # Checklists & safety checks
│   └── refactor-module.md    # Boundary-preservation workflow
├── hooks/
│   ├── pre-write.sh          # Path protection & sanity validation
│   └── post-write.sh         # Linter, formatter, typecheck gate
└── mcp.json                  # Server configuration for runtime context

```

---

## 2. Layer 1: Rules (Static & Minimal)

Rules must never be narrative essays. Write them as **bulleted assertions** focused on negative constraints and invariants that the model cannot infer from reading the code.

```markdown
<!-- .agent/rules/base.md -->
# Core Directives

## Invariants
- Language target: Node 22 / TypeScript 5.6 (strict mode).
- Prefer functional immutability (`readonly`, `const`) over mutable state.
- Never write database raw queries inside UI components; use `@server/services/*`.

## Prohibitions
- DO NOT edit files under `src/core/generated/`.
- DO NOT install new runtime dependencies without explicit approval.
- DO NOT use `any`; use `unknown` with runtime type narrowing.

```

---

## 3. Layer 2: Skills (On-Demand Retrieval)

Instead of packing all procedures into your primary prompt, store procedures as **Skills** that the agent pulls only when triggered by relevant tasks.

### Structure of a Skill File

```markdown
<!-- .agent/skills/add-api-endpoint.md -->
---
name: add-api-endpoint
description: Procedure for creating a typed REST endpoint in Hono
triggers: ["new endpoint", "create route", "api route"]
---

# Workflow
1. Define the input/output schema in `src/schemas/<domain>.ts` using Zod.
2. Register the route handler in `src/routes/<domain>.ts`.
3. Wire the route into the primary router in `src/index.ts`.
4. Run `pnpm test:routes` to verify contract compliance.

# Canonical Template
```typescript
import { zValidator } from '@hono/zod-validator';
import { Hono } from 'hono';

export const route = new Hono().post(
  '/',
  zValidator('json', inputSchema),
  async (c) => {
    const data = c.req.valid('json');
    return c.json({ success: true, payload: data });
  }
);

```

### Loading Mechanism

Give your agent a lightweight tool (or system prompt instruction) to read skills:

```typescript
// Tool: load_skill(skillName: string)
async function loadSkill({ skillName }: { skillName: string }): Promise<string> {
  const filePath = path.join('.agent/skills', `${skillName}.md`);
  if (!fs.existsSync(filePath)) {
    throw new Error(`Skill ${skillName} not found.`);
  }
  return fs.readFileSync(filePath, 'utf-8');
}

```

---

## 4. Layer 3: Model Context Protocol (MCP) for Live Data

MCP allows the agent to fetch surgical snapshots of state instead of ingesting huge log files or entire source trees.

```json
// .agent/mcp.json
{
  "mcpServers": {
    "ast-grep": {
      "command": "ast-grep",
      "args": ["lsp", "--stdio"]
    },
    "git-diff": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git", "--repository", "."]
    },
    "postgres-schema": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost:5432/app_dev"]
    }
  }
}

```

### MCP Query Strategy

* **Schema on Demand:** Use `postgres-schema` to inspect column types only when writing database queries.
* **Symbol Navigation:** Use language server protocol (LSP) or `ast-grep` via MCP to find callers and type definitions across files rather than searching text blindly.

---

## 5. Layer 4: Hooks (Deterministic Context Injection)

Hooks bridge the deterministic terminal with the agent's prompt loop.

### Pre-Action Hook (`pre-write.sh`)

Blocks changes before they occur.

```bash
#!/usr/bin/env bash
TARGET_FILE="$1"

# Prevent modification to security-sensitive or auto-generated files
FORBIDDEN_PATTERNS=("package-lock.json" "pnpm-lock.yaml" ".env" "prisma/migrations/*")

for pattern in "${FORBIDDEN_PATTERNS[@]}"; do
  if [[ "$TARGET_FILE" == $pattern ]]; then
    echo "ERROR: Direct editing of $TARGET_FILE is prohibited." >&2
    exit 1
  fi
done

```

### Post-Action Hook (`post-write.sh`)

Feeds syntax, formatting, and type errors immediately back into context for iterative correction.

```bash
#!/usr/bin/env bash
FILE="$1"

# 1. Deterministic fast format/lint
npx biome check --write "$FILE" > /dev/null 2>&1

# 2. Type-check affected area
ERRORS=$(npx tsc --noEmit --project tsconfig.json 2>&1 | grep "$FILE")

if [ -n "$ERRORS" ]; then
  echo "TYPECHECK_FAILED:"
  echo "$ERRORS"
  exit 1
fi

echo "VERIFICATION_SUCCESS"
exit 0

```

---

## 6. Context Window Budget Allocation

Maintain strict budgeting per interaction turn to avoid context saturation:

| Context Slice | Target Size | Budget Policy |
| --- | --- | --- |
| **System & Rules** | 1,000 – 2,500 tokens | Static. Never include long explanations or history. |
| **Active Skill** | 500 – 1,500 tokens | Transient. Unload when task finishes. |
| **MCP Live Query Data** | 1,000 – 3,000 tokens | Aggregated/filtered. Strip formatting fluff. |
| **Conversation / Code Diffs** | 4,000 – 12,000 tokens | Prune older turns aggressively using sliding window. |
| **Deterministic Error Feedback** | 200 – 1,000 tokens | Truncate stack traces; return only the relevant 5–10 error lines. |


### Building a complete production system for building serious software alongside AI agents, covering:
1. **Scoping & Spec Writing:** Forcing models to operate inside strict architectural boundaries before writing a single line of code.
2. **Directed Architecture:** Designing agentic workflows that produce predictable, production-ready code instead of creative hallucinations.
3. **Legacy Code & Recovery:** Safely injecting AI agents into existing, undocumented codebases without breaking load-bearing logic.
4. **Systemic Workflows:** Moving past simple autocomplete to orchestrate end-to-end features safely.

## 1. Scoping & Spec Writing: The Contract-First Gate

High-autonomy coding fails when an agent writes code while simultaneously deciding *what* to build. To guarantee architectural compliance, the agent must pass through an immutable **Contract-First Gate** before touching source files.

```
┌────────────────────────────────────────────────────────────────────────┐
│                        Contract-First Pipeline                         │
│                                                                        │
│  [Human Intent] ──> [Spec Engine] ──> [RFC / Invariants]               │
│                                               │                        │
│                                               ▼                        │
│  [Source Code]  <── [Implementation] <── [Strict Type/Test Contracts] │
└────────────────────────────────────────────────────────────────────────┘

```

### The Three-Document Spec Standard

Every feature, migration, or fix must generate three artifacts stored under `.agent/specs/<feature-slug>/`:

1. **`requirements.md` (Domain Boundaries & Non-Goals):**
* Functional goals defined as input/output pairs.
* **Explicit Non-Goals:** What the agent is expressly forbidden to touch or optimize (e.g., *"Do not refactor the session storage mechanism while adding the tenant header"*).


2. **`architecture.md` (Structural Invariants):**
* Module dependency graph constraints (e.g., *"Layer `billing` may import `identity`, but `identity` must never import `billing`"*).
* Storage and wire contracts (strict JSON schemas, Prisma/Drizzle schemas, or gRPC definitions).


3. **`verification-plan.md` (Deterministic Acceptance Criteria):**
* Exact CLI commands, test file paths, and benchmark thresholds that define completion.



### Enforcing the Spec Verification Phase

Use a pre-flight checklist script that validates the spec *before* the agent unlocks filesystem write permissions:

```markdown
<!-- .agent/specs/billing-webhooks/requirements.md -->
# Specification: Idempotent Webhook Handler

## 1. Domain Invariants
- Handler MUST check Redis key `idempotency:${event_id}` using `SET NX EX 86400`.
- If key exists, return HTTP 200 with payload `{"status": "duplicate"}`.
- If processing fails down-stream, key MUST NOT be deleted (poison pill quarantine).

## 2. Prohibitions & Architectural Bounds
- DO NOT add external dependencies (use existing ioredis and zod).
- DO NOT modify `src/middleware/auth.ts`.
- File writes restricted exclusively to:
  - `src/features/webhooks/*`
  - `tests/features/webhooks/*`

```

---

## 2. Directed Architecture: Deterministic Agent Execution

"Creative hallucinations" occur when agents operate without type contracts, linters, or structural compilers in their immediate feedback loops. Directed Architecture constrains the LLM’s search space through **type-first compilation** and **AST structural boundaries**.

```
                ┌───────────────────────────────────────┐
                │          Write Phase: Draft           │
                └──────────────────┬────────────────────┘
                                   │
                                   ▼
                ┌───────────────────────────────────────┐
                │ AST & Semantic Linting (Biome/Semgrep)│ ──FAIL──┐
                └──────────────────┬────────────────────┘         │
                                   │ PASS                         │
                                   ▼                              │
                ┌───────────────────────────────────────┐         │ (Error &
                │ Strict Typecheck (tsc / pyright)      │ ──FAIL──┤ AST context)
                └──────────────────┬────────────────────┘         │
                                   │ PASS                         │
                                   ▼                              │
                ┌───────────────────────────────────────┐         │
                │ Targeted Unit / Integration Tests     │ ──FAIL──┘
                └──────────────────┬────────────────────┘
                                   │ PASS
                                   ▼
                ┌───────────────────────────────────────┐
                │     Git Stash / Finalize Commit       │
                └───────────────────────────────────────┘

```

### Type-First Contract Generation

Before writing business logic, the agent generates and locks TypeScript/Zod/Pydantic interfaces:

```typescript
// src/features/webhooks/contracts.ts
import { z } from "zod";

export const StripeWebhookPayloadSchema = z.object({
  id: z.string().startsWith("evt_"),
  type: z.enum(["invoice.payment_succeeded", "customer.subscription.deleted"]),
  data: z.record(z.unknown()),
  created: z.number().int().positive(),
});

export type StripeWebhookPayload = z.infer<typeof StripeWebhookPayloadSchema>;

export interface WebhookProcessor {
  process(payload: StripeWebhookPayload): Promise<{ success: boolean; error?: string }>;
}

```

### AST Guardrails & Semgrep Rules

Standard linters check syntax; structural linters enforce architecture. Place Semgrep or `ast-grep` rules into `.agent/rules/architecture.yml`:

```yaml
rules:
  - id: prohibit-raw-db-access-in-routes
    patterns:
      - pattern-either:
          - pattern: $DB.query(...)
          - pattern: prisma.$MODEL.$ACTION(...)
    paths:
      include:
        - "src/api/routes/**"
    message: "Direct DB queries in routes violate layered architecture. Delegate to src/services/*."
    severity: ERROR
    languages: [typescript, javascript]

```

---

## 3. Legacy Code & Recovery: The Strangler & Characterization Pattern

Running an agent loose on legacy, undocumented codebases without safeguards creates cascading regressions. The safe pattern involves three stages: **Dependency Cartography**, **Characterization Harnessing**, and the **Strangler Fig Pattern**.

```
Legacy Monolith
┌──────────────────────────────────────────────┐
│  Target Service (Undocumented logic)         │
│         ▲                                    │
│         │ (Intercept calls & snapshot)       │
├─────────┴────────────────────────────────────┤
│  Characterization Test Suite (Golden Master) │
│         │                                    │
│         ▼                                    │
│  Modernized Agent-Refactored Module          │
└──────────────────────────────────────────────┘

```

### Step 1: Automated Dependency Cartography

Extract objective dependencies and call graphs via CLI tools rather than asking the LLM to inspect files manually:

```bash
# 1. Map all inbound and outbound edges for the legacy module
npx madge --circular --image graph.svg src/legacy/payment-calculator.js
npx madge --depends src/legacy/payment-calculator.js --json > dependencies.json

```

### Step 2: Golden Master / Characterization Harnessing

Before changing a single line, prompt the agent to write a **Characterization Test Suite** that records the behavior of the system as it currently exists (including its quirks and bugs):

```typescript
// tests/characterization/payment-calculator.golden.test.ts
import { calculateOrderTotal } from "../../src/legacy/payment-calculator";
import goldenFixtures from "./fixtures/golden-orders.json";

describe("Legacy Payment Calculator - Golden Master", () => {
  it.each(goldenFixtures)(("matches legacy outputs for order scenario %s"), (scenario) => {
    // Freezes existing edge cases, tax calculation oddities, and legacy return signatures
    const result = calculateOrderTotal(scenario.input);
    expect(result).toEqual(scenario.expectedOutput);
  });
});

```

### Step 3: Strangler Facade Implementation

Have the agent implement a typed facade around the legacy module:

1. Wrap the legacy function call inside a strongly typed boundary adapter.
2. Direct all calls to the adapter while the agent rewrites internal logic.
3. Verify that 100% of the characterization tests pass continuously against the adapter.

---

## 4. Systemic Workflows: Orchestrating Safe End-to-End Features

To move beyond autocomplete, orchestrate development across specialized sub-agent states using ephemeral Git worktrees and automated feedback loops.

### The Multi-Stage Agent Topology

| Role | Permissions | Input | Output Artifact |
| --- | --- | --- | --- |
| **Architect Agent** | Read-Only (LSP, Docs) | Product Request + Repo Map | `requirements.md`, `contracts.ts` |
| **Test Engine Agent** | Scoped FS (`tests/**`) | `contracts.ts` + `requirements.md` | Failing TDD Integration Suite |
| **Worker Agent** | Scoped FS (`src/**`) | Contracts + Failing Tests | Implementation passing tests |
| **Audit Agent** | Read-Only (Linters, AST) | Git Diff | Static compliance verdict (Pass/Fail) |

### Automated Feedback Loop Orchestration

Implement an orchestrator script that manages the agent execution loop and feeds compiler failures directly back into the context window:

```bash
#!/usr/bin/env bash
# .agent/orchestrate.sh
set -eo pipefail

FEATURE_BRANCH="agent-feature-$(date +%s)"
git checkout -b "$FEATURE_BRANCH"

MAX_ATTEMPTS=4
ATTEMPT=1
SUCCESS=0

echo "[+] Step 1: Agent implementing changes..."
# Dispatch agent execution tool here (e.g., Claude Code, Cursor, Aider)

while [ $ATTEMPT -le $MAX_ATTEMPTS ]; do
  echo "[+] Run verification loop (Attempt $ATTEMPT/$MAX_ATTEMPTS)..."

  # Run deterministic type checks & AST linters
  LINT_OUTPUT=$(npx biome check src/ 2>&1 || true)
  TYPE_OUTPUT=$(npx tsc --noEmit 2>&1 || true)
  TEST_OUTPUT=$(pnpm test:targeted 2>&1 || true)

  if [[ -z "$LINT_OUTPUT" && -z "$TYPE_OUTPUT" && "$TEST_OUTPUT" =~ "passed" ]]; then
    echo "[✔] All deterministic gates passed cleanly."
    SUCCESS=1
    break
  fi

  echo "[!] Verification failed. Formatting compiler payload for agent repair..."
  FEEDBACK_PAYLOAD=$(cat <<EOF
Deterministic verification gates failed. You must fix these errors without changing the test requirements:
### Linter Output
$LINT_OUTPUT

### Compiler / Typecheck Output
$TYPE_OUTPUT

### Test Suite Output
$TEST_OUTPUT
EOF
)

  # Pass FEEDBACK_PAYLOAD directly back into the agent context
  echo "$FEEDBACK_PAYLOAD" > .agent/context/compiler-error.txt
  # Trigger model auto-repair step
  ((ATTEMPT++))
done

if [ $SUCCESS -ne 1 ]; then
  echo "[✘] Agent reached max repair iterations. Aborting and resetting worktree."
  git reset --hard HEAD
  exit 1
fi

echo "[✔] Feature successfully verified. Ready for PR generation."

```

### Context Budgeting Across Workflow Phases

Keep token spending focused on the active phase to prevent context degradation:

```
[Phase 1: Architecture] ──> Inject: High-level repo map + Domain docs
                           Prune: All implementation details

[Phase 2: Scaffolding]   ──> Inject: Contracts + Type definitions
                           Prune: Verbose architectural discussions

[Phase 3: Logic & Fix]   ──> Inject: Single file diff + Compiler output
                           Prune: Unrelated repository source files

```

By enforcing strict spec boundaries, isolating legacy behavior behind golden masters, and wrapping write operations in deterministic AST and compiler gates, agents shift from unpredictable assistants into dependable, autonomous engineering pipelines.