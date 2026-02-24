---
name: llm-api-review-and-redesign
description: Review an existing REST API (or OpenAPI spec) and produce prioritized, actionable recommendations plus an implementation plan to create an LLM-optimized version of the API. Use when asked to audit endpoints for agent/tool reliability (token efficiency, composability, discoverability, self-correcting errors, safe side effects), propose an LLM-specific surface (e.g., /llm/v1), and outline concrete endpoint/schema/error patterns inspired by MCP.
---
# LLM API Review and Redesign

## Goal

Assess an existing API through an LLM/agent lens and deliver:

1. A **prioritized list of changes** (must-have → recommended → optional)
2. A **concrete target design** for an LLM-optimized API surface
3. A **phased implementation plan** (quick wins, medium, long) with migration strategy

Optimize for LLM success: composability, low token footprint, discoverability, self-correction, and safe side effects.

---

## Inputs to collect

Prefer these artifacts (use what is available; proceed with partial info if necessary):

* OpenAPI 3.0/3.1 spec (JSON/YAML), or API docs
* Representative request/response examples (especially “list” and “detail” endpoints)
* Core resources/entities (e.g., tasks, tickets, users, workflows)
* Common user intents (top 5–10 “agent tasks”)
* Auth model (scopes/roles), rate limits, and side-effect semantics
* Any “screen endpoints” or UI-optimized endpoints in production
* Known pain points (large payloads, retries, bulk ops, workflows, errors)

---

## Output contract (always follow)

Produce a report with these sections (use headings exactly):

1. **Executive summary**
2. **Top risks for LLM clients**
3. **Prioritized recommendations**
4. **Target LLM API surface**
5. **Implementation plan**
6. **Migration and compatibility**
7. **OpenAPI and schema guidance**
8. **Appendix: examples**

Keep recommendations specific: name endpoints, propose new shapes, and include example payloads.

---

## Prioritized checklist (evaluate in this order)

### Must-haves (highest impact)

1. **Tool-like endpoints (atomic + intention-revealing)**

   * Prefer `POST /{resource}/{id}/actions/{action}` over ambiguous partial `PATCH` for workflow mutations.
2. **Schema-first OpenAPI**

   * Explicit enums/formats/constraints + examples; versioned and contract-tested.
3. **Token-efficient defaults**

   * Summary views, `fields=`, `include=` off by default, pagination on every list/search.
4. **Typed search/filter with sort + cursor pagination**

   * Avoid “list all then filter client-side”; provide `POST /{resource}/search`.
5. **Actionable errors for self-repair**

   * Stable `code`, `field`, `allowed_values`, `example_fix`, plus `request_id`.
6. **Explicit side-effect contract**

   * Clearly identify read-only vs write; destructive vs non; idempotent vs non.
7. **Idempotency keys for writes**

   * Safe retries without duplicate side effects.
8. **Preview/dry-run for destructive or bulk actions**

   * Two-phase: preview → execute; return per-item results; no hidden partial failures.
9. **Workflow discoverability**

   * `allowed-actions` / transitions endpoints; never require UI tribal knowledge.
10. **Determinism**

* Document default sort; stable cursors; explicit timezone handling; avoid nondeterministic “latest”.

### Strongly recommended (production reliability)

* **AuthZ least privilege** (separate read vs write scopes; stricter for destructive/bulk)
* **Rate-limit + retry contract** (`429` + `Retry-After`, stable throttling error code)
* **Long-running jobs** (`202` + job handle + progress)
* **Optimistic concurrency** (ETag/If-Match or version field)
* **Data minimization & sensitive fields** (safe-by-default summary views; explicit opt-in for sensitive fields)
* **Audit logging** (request correlation ids, actor identity, operation metadata; avoid logging secrets)

### Optional (nice-to-have)

* Batch endpoint for multi-step agent operations
* Caching hints (ETag, conditional GET)
* “Capabilities/tool catalog” endpoint for discoverability (per-caller permissions)

---

## Review workflow

### Step 1: Map the domain and intents

* List the top entities and relationships.
* Identify 5–10 common LLM intents (examples):

  * “Find urgent items, summarize blockers”
  * “Advance workflow step”
  * “Bulk close items matching criteria”
  * “Create item + attach to project/workflow”
  * “Explain what’s needed to complete item”

Deliverable: a small table of intents → current endpoints used (if known).

### Step 2: Inventory endpoints and classify

For each endpoint, record:

* Purpose (read vs write)
* Side effects (none / non-destructive / destructive)
* Idempotency (safe retries? supports Idempotency-Key?)
* Payload size risk (large nested response? unbounded lists?)
* Discoverability gaps (hidden enums? undocumented defaults? missing allowed transitions?)
* Error quality (actionable vs generic)

Deliverable: endpoint inventory (even if partial) plus “top offenders”.

### Step 3: Score against the checklist

Score each “must-have” area as:

* ✅ Present and consistent
* ⚠️ Present but inconsistent or incomplete
* ❌ Missing / harmful pattern present

Focus on “why an agent would fail”:

* context blow-ups from large payloads
* inability to narrow results (missing search/filters)
* non-actionable errors (agent can’t self-correct)
* unsafe writes (no idempotency, no preview)
* hidden workflow logic (agent guesses transitions)

Deliverable: top risks and root causes, in prioritized order.

### Step 4: Propose the LLM-optimized target surface

Prefer a distinct base path and versioning to avoid coupling:

* UI: `/api/v1/...`
* LLM: `/llm/v1/...` (or `/agent/v1/...`)

Define:

* summary list + detail get + typed search
* explicit action endpoints for workflow mutations
* meta endpoints for enums/schemas/capabilities
* preview/confirm patterns for risky ops
* job handles for long ops

Deliverable: a concise endpoint set (10–30 endpoints) that supports the top intents.

### Step 5: Produce a phased plan

Split into phases:

* **Phase 0 (1–2 weeks):** documentation and safety essentials

  * error schema standardization, request_id, deterministic sorts, pagination defaults, OpenAPI cleanup
* **Phase 1 (2–6 weeks):** LLM surface MVP

  * `/llm/v1` endpoints for key resources (summary/detail/search), idempotency, actionable errors
* **Phase 2:** workflow + safety hardening

  * allowed-actions/transitions, preview/confirm, bulk responses, rate limits
* **Phase 3:** operational maturity

  * job handles, concurrency controls, audit logs, simulation tests, deprecation strategy

Deliverable: plan with milestones, owners, and acceptance criteria.

---

## Recommendation writing rules

For each recommendation, include:

* **What** to change (endpoint/schema behavior)
* **Why** it matters for LLMs (failure mode prevented)
* **Priority** (Must-have / Recommended / Optional)
* **Effort** (S/M/L) and dependencies
* **Concrete example** (request/response or schema snippet)

Prefer “replace/augment X with Y” over abstract advice.

---

## Canonical patterns (use as building blocks)

### Pattern A: Token-efficient reads

* `GET /llm/v1/{resource}?view=summary&limit=50&cursor=...`
* `GET /llm/v1/{resource}/{id}?view=detail`
* `GET /llm/v1/{resource}/{id}?fields=id,title,status`

Rules:

* `include=` is off by default.
* Summary views avoid sensitive fields by default.

### Pattern B: Typed search (SQL-like power, safe)

```http
POST /llm/v1/tasks/search
Content-Type: application/json

{
  "filter": {
    "assignee": "me",
    "status": ["open","blocked"],
    "due_before": "2026-03-01T00:00:00Z"
  },
  "sort": ["due_at", "-priority"],
  "limit": 50,
  "cursor": null
}
```

Rules:

* Always support sorting + cursor pagination aligned to sort order.
* Document default sort.

### Pattern C: Explicit action endpoints for mutations

* `POST /llm/v1/tasks/{id}/actions/complete`
* `POST /llm/v1/tasks/{id}/actions/assign`
* `POST /llm/v1/tasks/{id}/actions/add_comment`

Rules:

* Keep input schemas small and intention-specific.
* Return a change summary (what changed) plus updated resource (summary by default).

### Pattern D: Actionable errors (self-correcting)

```json
{
  "error": {
    "code": "INVALID_ENUM",
    "message": "Invalid status 'donee'.",
    "field": "status",
    "allowed_values": ["open","in_progress","blocked","done"],
    "example_fix": { "status": "done" }
  },
  "request_id": "req_123"
}
```

Rules:

* Stable `code`
* Include `field` and `allowed_values` when applicable
* Include `example_fix` whenever feasible
* Always return `request_id` (and echo it in headers if possible)

### Pattern E: Safe writes with idempotency

* Require `Idempotency-Key` for creates and side-effecting actions.
* Ensure replay returns the same result (or reference to it).

### Pattern F: Preview/confirm for risky or bulk operations

* `POST /llm/v1/tasks/bulk_complete:preview`
* `POST /llm/v1/tasks/bulk_complete` (requires confirmation token)

Bulk response shape:

```json
{
  "summary": { "requested": 3, "succeeded": 2, "failed": 1 },
  "results": [
    { "id": "t_1", "status": "succeeded" },
    { "id": "t_2", "status": "failed", "error": { "code": "NOT_ALLOWED", "message": "Task is locked." } },
    { "id": "t_3", "status": "succeeded" }
  ],
  "request_id": "req_200"
}
```

### Pattern G: Workflow discoverability

* `GET /llm/v1/tasks/{id}/allowed-actions`
* `GET /llm/v1/workflows/{id}/transitions`

Example:

```json
{
  "id": "t_123",
  "state": "awaiting_review",
  "allowed_actions": [
    { "name": "request_changes", "destructive": false },
    { "name": "approve", "destructive": false },
    { "name": "cancel", "destructive": true }
  ]
}
```

### Pattern H: Long-running jobs

* `POST /llm/v1/imports` → `202 Accepted` with `{ "job_id": "...", "status_url": "/llm/v1/jobs/..." }`
* `GET /llm/v1/jobs/{id}` → `status`, `progress`, `result` or `error`

---

## Implementation plan guidance

### Quick wins (no new endpoints required)

* Standardize error schema + `request_id`
* Add deterministic default sorts on lists
* Ensure pagination everywhere (limit + cursor)
* Add `fields` and `include` controls
* Improve OpenAPI accuracy and examples

### Build the LLM surface MVP (/llm/v1)

* Pick 1–3 core resources and implement:

  * summary list
  * detail get
  * search
  * 2–5 action endpoints
* Add:

  * idempotency for writes
  * per-caller capabilities endpoint (optional but helpful)
  * enum/meta endpoints (or make OpenAPI easily accessible)

### Safety and workflow expansion

* Add preview/dry-run for bulk/destructive ops
* Add allowed-actions/transitions endpoints
* Implement rate limit and retry contract
* Add optimistic concurrency controls for updates

### Operational maturity

* Job handles for long ops
* Audit logging with correlation id and actor identity
* Automated contract tests to prevent drift
* Agent simulations (see below)

---

## Migration and compatibility strategy

* Keep UI API stable; add `/llm/v1` as an additive surface.
* Map existing operations to LLM equivalents; avoid “breaking” changes.
* Prefer shared internal service logic; only the boundary (shape/schemas) differs.
* Provide a deprecation plan for any legacy “screen endpoints”:

  * measure usage
  * add replacements
  * add warnings
  * sunset gradually

Deliverable: a mapping table of “current endpoint → new LLM endpoint”.

---

## Validation and testing (agent-oriented)

Add tests beyond normal API tests:

* **Contract tests**: OpenAPI matches real responses; enums/constraints accurate.
* **Golden path tool-chains**: scripted sequences that mimic agent usage:

  * search → get detail → allowed-actions → action → verify change
* **Retry tests**: replay idempotent requests, ensure no duplication.
* **Bulk partial failure tests**: confirm per-item results and actionable errors.
* **Token/payload tests**: enforce summary payload size thresholds.
* **Rate limit tests**: validate `429` + `Retry-After` and stable throttling error code.

---

## Appendix: report templates

### Prioritized recommendations template (copy/paste)

For each item:

* **Title:**
* **Priority:** Must-have / Recommended / Optional
* **Problem (current behavior):**
* **Why it breaks LLM clients:**
* **Proposed change:**
* **Example:** (request/response)
* **Effort:** S/M/L
* **Dependencies/risks:**
* **Acceptance criteria:**

### Target LLM endpoint set template (copy/paste)

* **Meta**

  * `GET /llm/v1/me`
  * `GET /llm/v1/meta/enums`
  * `GET /llm/v1/openapi.json` (or stable `/openapi.json`)
  * `GET /llm/v1/capabilities` (optional)
* **Resource: X**

  * `GET /llm/v1/x?view=summary...`
  * `GET /llm/v1/x/{id}?view=detail`
  * `POST /llm/v1/x/search`
  * `GET /llm/v1/x/{id}/allowed-actions` (if stateful)
  * `POST /llm/v1/x/{id}/actions/...`
  * `POST /llm/v1/x/bulk_...:preview` (if risky/bulk)
  * `POST /llm/v1/x/bulk_...`

