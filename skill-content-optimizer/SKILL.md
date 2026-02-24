---
name: skill-content-optimizer
description: Create performance-optimized Skill packages (SKILL.md + resources) and minimal repository context files (AGENTS.md/CODEX.md) that improve agent success while avoiding context bloat, conflicting guidance, and unnecessary requirements. Use when drafting or revising procedural instructions, workflows, checklists, templates, or agent-specific guidance meant to be consumed at inference time.
---

# Skill Content Optimizer

## Non-negotiables

- Encode **procedures** (workflows/SOPs), not broad background.
- Optimize for **verifier-facing success**: concrete steps, constraints, sanity checks, and how to confirm correctness.
- Keep content **focused and modular**:
  - Target **2–3 modules** for a task class (or 2–3 sections in one SKILL.md).
  - Avoid “comprehensive documentation” dumps.
- Include **≥1 working example** that a reader can adapt immediately.
- Prefer **human-curated procedures**; avoid “generate skills first” loops as a default strategy.
- Add **only acceptance-critical requirements**; treat all extra requirements as suspect.

## Workflow

1. **Define the output contract**
   - Specify deliverables, output formats, and what “pass” means.
   - List constraints that are easy to violate (paths, schemas, tolerances, formatting rules).

2. **Choose the right packaging**
   - Create a **Skill** when the goal is: reusable procedure + optional templates/scripts/examples.
   - Create a **repo context file** (AGENTS.md/CODEX.md) when the goal is: minimal repo-specific commands + required tooling + strict constraints enforced by CI.
   - Do not mix: keep repo-wide requirements in context files; keep reusable procedures in Skills.

3. **Draft the minimal structure**

   **Skill structure (recommended 2–3 modules/sections):**
   - **Workflow**: numbered steps with concrete commands/APIs.
   - **Verification**: how to check correctness; what failure looks like; quick sanity checks.
   - **Example**: minimal working example (or a pointer to `references/` / `scripts/`).

   **Context file structure (keep short):**
   - **Quick start commands**: install, test, lint/format, run.
   - **Hard requirements**: only what is mandatory to pass CI or match repo conventions.
   - **Do/Don’t guardrails**: prevent expensive, unnecessary exploration.

4. **Write “procedural, not descriptive”**
   - Use imperative steps: “Run…”, “Edit…”, “Verify…”.
   - Prefer exact commands, file patterns, and API calls over general advice.
   - Add only the smallest set of details that prevents common failure modes.

5. **Run a compression pass (mandatory)**
   - Delete background paragraphs that do not change actions.
   - Remove duplicated information that already exists elsewhere; link instead.
   - Convert prose to checklists and short step lists.
   - Mark anything non-mandatory as **Optional** (and make it skippable).

6. **Validate and iterate with A/B checks**
   - Compare outcomes **with vs. without** the content.
   - Track: pass/fail, timeouts, step count, and major failure reasons.
   - If performance drops or timeouts rise without a win in pass rate, **prune or delete** the added content.

## Skill authoring recipe

### Step 1 — Pick the boundary
- Define the **task class** (what repeats across tasks).
- Define the **inputs/outputs** and the **verifier** (tests, schemas, numeric tolerances, formatting constraints).
- Exclude anything that is task-instance specific (filenames, constants, IDs, expected outputs).

### Step 2 — Write the Workflow section
- Start with a 3–7 step “happy path” procedure.
- Include exact tool/library calls when the workflow is brittle.
- Add small “if X, then Y” branches only when necessary.

### Step 3 — Write the Verification section
- Provide:
  - One fast check (seconds/minutes).
  - One full check (the real verifier/test command).
  - A short list of common failure signatures and how to diagnose them.

### Step 4 — Add a minimal Example
- Provide one example that:
  - Demonstrates the full workflow end-to-end.
  - Uses placeholders clearly (e.g., `<INPUT_PATH>`, `<OUTPUT_PATH>`).
- If the example is longer than ~40 lines, move it to `references/templates.md` or `scripts/`.

### Step 5 — Keep module count low
- Prefer 2–3 sections total.
- Split into multiple Skills only when they are truly independent and reused separately.

## Repo context file recipe (AGENTS.md / CODEX.md)

### Include only what changes agent behavior toward success
- **Exact commands** that matter:
  - environment setup (the one true way for this repo)
  - unit tests / targeted tests (the acceptance gate)
  - lint/format/typecheck if enforced
- **Required tooling** (only if required):
  - package manager (e.g., `uv`, `pdm`, `poetry`, `npm`, `pnpm`)
  - test runner (e.g., `pytest -q`)
- **Hard constraints**:
  - required Python/node versions if strict
  - required env vars if tests depend on them
  - required formatting if CI will fail otherwise

### Avoid
- Repo “overviews” that list directories/files.
- Broad mandates like “run the entire test suite” unless acceptance requires it.
- “Explore the repo thoroughly”, “add more tests”, or “refactor” requirements unless required for acceptance.
- Duplicating README/docs content unless the repo lacks documentation entirely.

## Negative-delta triage (when added content makes performance worse)

1. **Check for conflict**
   - Remove competing instructions (two ways to do the same thing, conflicting style rules, contradictory commands).

2. **Check for overhead**
   - Delete sections that cause extra exploration/testing without improving correctness.

3. **Reduce scope**
   - Replace “must” with “optional” unless truly required.

4. **Re-run A/B**
   - Keep only what measurably improves outcomes.

## Harness-awareness checklist

- Ensure the **description** is specific enough for discovery (mention tools, task classes, and outputs).
- Assume some harnesses may acknowledge content but not apply it:
  - Add concise, action-first steps that are hard to ignore.
  - Put the critical workflow in the first screenful of the Skill.
- Do not rely on long “reminders”; rely on short steps + verification commands.

## Use the bundled templates

Read `references/templates.md` and adapt:
- Minimal SKILL.md skeletons
- Minimal AGENTS.md skeleton
- Compression and A/B evaluation checklists
