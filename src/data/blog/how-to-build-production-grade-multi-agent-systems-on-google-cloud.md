---
title:  how to build production grade multi-agent systems on Google Cloud
author: Sunny
pubDatetime: 2026-11-17T04:06:31Z
slug: how-to-build-production-grade-multi-agent-systems-on-google-cloud
featured: false
draft: false
tags:
  - multi agent
  - Google Cloud
  - Agentic
description:
  What it covers:🔹 Multi-agent delegation (orchestrator + specialist subagents) 🔹 Model tiering (Gemini 3.6 Flash + 3.5 Flash Lite) 🔹 Safety policies — dev / staging / prod modes 🔹 Guardrails & defense-in-depth 🔹 Observability hooks 🔹 Evals & Cloud Run deployment
ogImage: https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Gemini_Generated_Image_l3szd5l3szd5l3sz.png
---
 Building Enterprise AI Agent Teams with _Google's Antigravity SDK_



---

![Angular Agentic Engineering](https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Gemini_Generated_Image_l3szd5l3szd5l3sz.png)

Building a production-grade multi-agent system with the **Google Antigravity SDK** (`pip install google-antigravity`) separates agent reasoning (system instructions and tools) from governance (declarative policies and observability hooks).

Based on the reference architecture for the **Financial Audit Agent Team** on Google Cloud (BigQuery, Cloud Storage, Cloud Logging, Cloud Trace, and Cloud Run), the following blueprint outlines how to structure, secure, and deploy the system, along with how the pattern adapts to **Insurance Claims Adjudication** and **HR Onboarding Compliance**.

---

### 1. Multi-Agent Architecture & Least-Privilege Delegation

Instead of giving a single monolithic agent access to all databases, storage buckets, and write APIs, the architecture enforces strict separation of concerns across a 4-phase workflow (`trigger` $\rightarrow$ `gather` $\rightarrow$ `reconcile` $\rightarrow$ `report`):

* **Audit Orchestrator (Manager):** Holds the high-level objective and state machine. It has **zero** direct access to BigQuery or Cloud Storage; it only holds delegation tools that spawn isolated specialist subagents.
* **Data Researcher Agent (Read-Only):** Scoped strictly to `query_vendor_transactions` (BigQuery) and `list_invoices_in_gcs`.
* **Invoice Analyzer Agent (Read-Only):** Scoped strictly to `read_invoice_from_gcs` to extract structured line-item data, tax rates, and totals from multimodal PDF invoices in Cloud Storage.
* **Reconciliation Engine Agent (Analytical Core & Controlled Write):** Receives the structured outputs from the Researcher and Analyzer, cross-checks line items and tax math, flags discrepancies, and invokes `write_audit_result` (which is gated by the active safety policy tier).

**Delegation Tool Implementation (`tools/delegation_tools.py`):**
In the Antigravity SDK, subagents are spawned inside standard Python functions with type hints and docstrings, keeping subagent context windows isolated so raw PDF data doesn't bloat the Orchestrator's context:

```python
from google.antigravity import Agent
from tools.bigquery_tools import query_vendor_transactions
from tools.gcs_tools import list_invoices_in_gcs, read_invoice_from_gcs
from policies.audit_policies import get_readonly_policy

def delegate_to_researcher(quarter: str, project_id: str) -> str:
    """Spawns a read-only Data Researcher subagent to fetch pending transactions for a quarter."""
    researcher = Agent(
        name="data_researcher",
        model="gemini-3.5-flash-lite",
        system_instruction="You are a financial data researcher. Query BigQuery for pending vendor transactions and list matching GCS invoice paths.",
        tools=[query_vendor_transactions, list_invoices_in_gcs],
        policies=get_readonly_policy(),
    )
    response = researcher.run(f"Fetch all pending transactions and available invoice files for {quarter} in project {project_id}.")
    return response.text

```

---

### 2. Model Tiering Strategy

Right-sizing models to each agent's cognitive load minimizes latency and token spend across multi-turn agent loops:

* **Gemini 3.5 Flash Lite (`gemini-3.5-flash-lite`):** Assigned to the **Data Researcher** and **Invoice Analyzer**. These agents execute deterministic tool calls (SQL queries, GCS object listing) and structured PDF extraction where speed and cost efficiency are paramount.
* **Gemini 3.6 Flash (`gemini-3.6-flash`):** Assigned to the **Audit Orchestrator** and **Reconciliation Engine**. Multi-step state coordination, multi-source discrepancy analysis, tax calculation verification, and edge-case reasoning benefit from stronger reasoning depth (configurable via `GenerationConfig` thinking levels: `LOW`, `MEDIUM`, `HIGH`).

---

### 3. Safety Policies (`dev` / `staging` / `prod` Modes) & Guardrails

The Antigravity SDK uses **Decide** hooks (blocking, read-only interceptors) to enforce runtime policies before any tool executes. Combined with Least-Privilege GCP IAM Service Accounts, this creates **defense-in-depth**: both the SDK policy layer and GCP IAM must permit an action.

* **Development Mode (`--mode=dev`):** Uses `allow_all()` for rapid local iteration and debugging.
* **Staging Mode (`--mode=staging`):** Enables human-in-the-loop (HITL) verification for all state-mutating calls (`write_audit_result`), pausing execution to prompt for approval.
* **Production Mode (`--mode=prod`):** Enforces a **deny-by-default** posture for destructive or shell tools, allows read-only tools automatically, and activates the **Human Compliance Gate**—a declarative policy hook that automatically intercepts and escalates any financial discrepancy exceeding **$1,000** for compliance officer sign-off before committing to BigQuery.

```python
from google.antigravity.hooks import pre_tool_call, Decision

@pre_tool_call
def human_compliance_gate(context):
    """Pauses execution and escalates discrepancies > $1,000 for human review."""
    if context.tool_name == "write_audit_result":
        discrepancy = abs(float(context.tool_args.get("discrepancy_amount", 0.0)))
        if discrepancy > 1000.0:
            return Decision.require_approval(
                reason=f"Discrepancy of ${discrepancy:,.2f} exceeds $1,000 threshold. Escalating to Compliance Officer."
            )
    return Decision.allow()

```

---

### 4. Observability Hooks (Cloud Logging & Cloud Trace)

Using **Inspect** hooks (non-blocking, read-only), you can attach compliance telemetry across the agent lifecycle (`pre_turn`, `post_turn`, `post_tool_call`, `on_tool_error`) without cluttering business logic:

```python
from google.antigravity.hooks import post_tool_call, post_turn
import google.cloud.logging

@post_tool_call
def audit_trail_logger(context):
    """Streams an immutable compliance record of every tool invocation to Cloud Logging."""
    log_payload = {
        "agent_name": context.agent_name,
        "tool_name": context.tool_name,
        "tool_args": context.tool_args,
        "execution_ms": context.duration_ms,
        "status": "SUCCESS" if not context.error else "ERROR",
    }
    cloud_logger.log_struct(log_payload, severity="INFO")

@post_turn
def track_token_consumption(context):
    """Captures prompt, candidate, cached, and thinking tokens per turn via usage_metadata."""
    usage = context.response.usage_metadata
    trace_span.set_attribute("antigravity.tokens.total", usage.total_token_count)
    trace_span.set_attribute("antigravity.tokens.thinking", usage.thinking_token_count)

```

---

### 5. Provisioning, Evaluation & Cloud Run Deployment

#### Step 1: Provision GCP Infrastructure & Seed Discrepancies

```bash
export PROJECT_ID="your-gcp-project-id"
gcloud config set project $PROJECT_ID

gcloud services enable bigquery.googleapis.com storage.googleapis.com \
  logging.googleapis.com cloudtrace.googleapis.com aiplatform.googleapis.com run.googleapis.com

# Create BigQuery dataset & tables
bq mk --dataset $PROJECT_ID:financial_audit
bq mk --table $PROJECT_ID:financial_audit.vendor_transactions \
  vendor_id:STRING,vendor_name:STRING,invoice_num:STRING,amount:FLOAT64,currency:STRING,tax_rate:FLOAT64,status:STRING,quarter:STRING,transaction_date:DATE

# Create GCS Bucket & upload generated sample PDFs
gsutil mb -l us-central1 gs://$PROJECT_ID-audit-invoices
python scripts/generate_sample_invoices.py
gsutil -m cp data/invoices/*.pdf gs://$PROJECT_ID-audit-invoices/Q3/

```

#### Step 2: Run the Evaluation Suite (`eval/run_eval.py`)

Before deploying, run deterministic evaluations against planted test discrepancies (e.g., overbilled line items, mismatched tax rates, missing invoices, and $> \$1,000$ escalation triggers) to verify precision and policy adherence:

```bash
python eval/run_eval.py --project-id=$PROJECT_ID

```

#### Step 3: Deploy to Cloud Run

Package the agent behind a lightweight Flask/Gunicorn server (`server.py`) using a dedicated least-privilege service account (`audit-agent-sa`):

```bash
gcloud run deploy financial-audit-agent \
  --source . \
  --region us-central1 \
  --set-env-vars PROJECT_ID=$PROJECT_ID \
  --timeout 300 \
  --memory 1Gi \
  --service-account audit-agent-sa@$PROJECT_ID.iam.gserviceaccount.com \
  --no-allow-unauthenticated

```

Invoke the production service with an IAM identity token:

```bash
SERVICE_URL=$(gcloud run services describe financial-audit-agent --region us-central1 --format 'value(status.url)')
curl -X POST "$SERVICE_URL/audit" \
  -H "Authorization: Bearer $(gcloud auth print-identity-token)" \
  -H "Content-Type: application/json" \
  -d '{"quarter": "Q3"}'

```

---

### 6. Adapting the Blueprint: Bonus Sample Applications

The exact same 4-agent pattern (Orchestrator + DB Researcher + Document Analyzer + Adjudication/Compliance Engine + Policy Gate) ports directly to the two companion domain architectures in the `samples/` directory:

1. **Insurance Claims Adjudication (`samples/insurance-claims-agent/`):**
* **Orchestrator:** Manages the claims processing pipeline (`intake` $\rightarrow$ `policy_lookup` $\rightarrow$ `evidence_extraction` $\rightarrow$ `adjudicate`).
* **Policy Researcher (`gemini-3.5-flash-lite`):** Queries BigQuery for claimant policy coverage limits, deductibles, and active status.
* **Claim Document Analyzer (`gemini-3.5-flash-lite`):** Uses multimodal extraction on uploaded medical bills, repair estimates, and incident PDFs stored in GCS.
* **Adjudication Engine (`gemini-3.6-flash`):** Cross-references claim line items against policy exclusions and deductibles, calculating the approved payout.
* **Safety Policy Gate:** Automatically approves low-risk claims under the threshold while escalating high-value payouts (e.g., $> \$5,000$) or suspected fraud flags to a human claims adjuster.


2. **HR Onboarding Compliance (`samples/hr-onboarding-agent/`):**
* **Orchestrator:** Drives the pre-start compliance verification state machine.
* **HRIS Researcher (`gemini-3.5-flash-lite`):** Queries BigQuery employee records for role requirements, jurisdiction rules, and pending background check statuses.
* **Document Verifier (`gemini-3.5-flash-lite`):** Parses uploaded identity documents, signed tax forms, I-9s, and certifications from GCS.
* **Compliance Engine (`gemini-3.6-flash`):** Validates that signatures, expiration dates, and mandatory disclosures match regional labor requirements, updating the employee readiness status.
* **Safety Policy & Transform Hooks:** Uses **Transform** hooks to redact PII/SSN fields from logs before streaming to Cloud Logging, and uses **Decide** hooks to escalate expired work authorizations or missing background clearances to HR Legal.

