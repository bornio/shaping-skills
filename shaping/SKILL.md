---
name: shaping
description: Use this methodology to collaboratively shape a solution with a human — iterating on problem definition (Requirements) and solution options (Shapes) while maintaining multi-document consistency and producing buildable slices.
---


# Shaping Methodology (Agent ↔ Human)

A structured, artifact-driven approach for defining problems, exploring solution options, selecting a buildable shape, and slicing it into demo-able increments.

This skill is optimized for agentic collaboration:

* The agent drives the artifacts (tables/docs stay current).
* The human drives the decisions (what matters, what’s acceptable, what to build).
* Progress happens via small edits to shared ground-truth documents, not long narrative.
---

## Roles and Interaction Contract

### Human (Decision Maker)

* Owns priorities, constraints, success criteria, and tradeoffs
* Approves requirements states (Must-have vs Nice-to-have vs Out)
* Selects the shape

### Agent (Shaping Driver)

* Maintains the artifacts (R table, shapes, fit checks, breadboards, slices)
* Proposes options and concrete mechanisms
* Keeps all doc levels in sync (see Multi-Level Consistency)
* Surfaces unknowns as ⚠️ and converts them into spikes (not guesswork)
* Avoids stalling: when blocked, produces a “best-next” artifact and marks open questions explicitly
---

## The Core Loop (How Each Iteration Works)

Every shaping iteration should follow this sequence:

1. Show current ground truth artifact(s) (full tables, no abbreviations).
2. Propose a change (add/edit requirements, add/edit shape parts, add spike, etc.).
3. Apply the change in the artifact (so the doc becomes the new truth).
4. Call out what’s still unsolved:

   * any Requirements with Undecided
   * any Fit Check failures ❌ for the candidate/selected shape
   * any Parts flagged ⚠️ (must become spikes or be resolved)
5. Offer the next smallest action (e.g., “Add R3”, “Split C3 into alternatives”, “Run fit check”, “Breadboard selected shape”).

⠀
This keeps the conversation concrete and prevents drift.

---

## Multi-Level Consistency (Critical)

Shaping produces documents at different levels of abstraction. Truth must stay consistent across all levels.

### Document Hierarchy (high → low)

1. Big Picture — fastest context acquisition (summary view)
2. Shaping doc — ground truth for R, shapes, parts, fit checks, breadboard
3. Slices doc — ground truth for slice definitions and sliced breadboard
4. Individual slice plans (V1-plan, etc.) — ground truth for implementation steps

⠀
### Principle

Each level summarizes the level(s) below it.

Changes ripple in both directions:

* High → low: If Big Picture flags a part ⚠️, the shaping doc must show the same ⚠️.
* Low → high: If a slice plan changes scope/mechanism, update Slices doc and Big Picture in the same operation.

### Practice (Agent Checklist)

Whenever changing anything:

1. Identify which doc level you edited
2. Ask: “Does this change affect docs above or below?”
3. Update all affected docs immediately
4. Never let documents drift

⠀
---

## Starting a Session

Offer both entry points:

### Start from R (Requirements)

Use when the problem is unclear or contested.

* Capture pain points, constraints, and success
* Convert them into negotiated requirements

### Start from S (Shapes)

Use when someone already has a solution in mind.

* Capture it as a shape
* Extract requirements implied by the shape and negotiate them

There is no required order — shaping is iterative.

---

## Artifacts (Source of Truth)

Shaping is artifact-driven. These are the canonical artifacts:

* Requirements table (R)
* Shapes (S) tables
* Fit Check table (decision matrix)
* Parts table (mechanisms; includes Flag column)
* Breadboard (UI + Non-UI affordances + wiring)
* Slices doc (slice definitions + sliced breadboard + slices grid)
* Slice plans (implementation details per slice)
* Spikes (investigations for unknown mechanics)
---

## R: Requirements

Requirements define the problem space as negotiated constraints/outcomes.

### Rules

* Use IDs: R0, R1, R2…
* Requirements are negotiated — the agent may propose candidates, but must not “finalize” them without human alignment.
* Requirements state what is needed, not how it’s achieved.
* Satisfaction is shown only in Fit Checks (R × S).

### Requirement Status Values

* Core goal
* Must-have
* Nice-to-have
* Undecided
* Leaning yes / Leaning no
* Out

### Format (Always Show Full Table)

## Requirements (R)

| ID | Requirement | Status |
|----|-------------|--------|
| R0 | ... | Core goal |
| R1 | ... | Undecided |
```

---

## S: Shapes (Solution Options)

Shapes are mutually exclusive top-level approaches.

### Notation Hierarchy

|  Level  |  Notation  |  Meaning  |  Relationship  |
|---|---|---|---|
|  Requirements  |  R0, R1…  |  Constraints/outcomes  |  Members of set R  |
|  Shapes  |  A, B, C…  |  Competing approaches  |  Pick one  |
|  Components  |  C1, C2…  |  Parts inside a shape  |  Combine within shape  |
|  Alternatives  |  C3-A, C3-B…  |  Options for a component  |  Pick one  |
### Shape Titles

Use short titles that describe the approach:

```
## E: Modify CUR in place to follow S-CUR
```

### Shapes Are Mechanisms

Shape parts must describe what you build/change (mechanism), not intentions.

* ✅ “Route childType === 'letter' to typesenseService.rawSearch()”
* ❌ “Types unchanged” (constraint → belongs in R)

### Prefer Vertical Slice Parts

Avoid horizontal “layers.” Co-locate data + logic with the feature behavior it supports.

---

## Parts and Flagged Unknowns (⚠️)

### Parts Table Format

```
### Parts

| Part | Mechanism | Flag |
|------|-----------|:----:|
| **F1** | Create widget (component, def, register) | |
| **F2** | Magic authentication handler | ⚠️ |
```

### Meaning of Flag

* Empty = mechanism is understood concretely
* ⚠️ = we know what we want, not how to implement it yet

### Why ⚠️ Fails Fit Check

A Fit Check ✅ is a claim of knowledge. If a part is ⚠️, then the shape cannot claim it satisfies the requirement.

Therefore:

* A requirement that depends on any ⚠️ part must be ❌ until resolved (or spiked and replaced with a known mechanism).
---

## Fit Check (Decision Matrix)

The fit check is the decision table comparing all shapes against all requirements.

### Rules (Strict)

* Requirements are rows, shapes are columns
* Binary only: ✅ pass, ❌ fail
* Shape columns contain only ✅ or ❌ (no inline commentary)
* Full requirement text must be shown (no abbreviations)
* Notes explain failures briefly

### Format

```
## Fit Check

| Req | Requirement | Status | A | B | C |
|-----|-------------|--------|---|---|---|
| R0 | Make items searchable from index page | Core goal | ✅ | ✅ | ✅ |
| R1 | State survives page refresh | Must-have | ✅ | ❌ | ✅ |

**Notes:**
- B fails R1: state is in-memory only (lost on refresh)
```

### When a Shape “Feels Wrong” but Passes

That indicates a missing requirement. Add a new R, then re-run the fit check.

---

## Comparing Alternatives Within a Component

When deciding between alternatives inside a component (C3-A vs C3-B), create a scoped fit check:

```
## C3: Component Name

| Req | Requirement | Status | C3-A | C3-B |
|-----|-------------|--------|------|------|
| R1 | State survives page refresh | Must-have | ✅ | ❌ |
```

---

## Working With an Existing Shaping Doc

If a shape is already selected:

1. Display the fit check for the selected shape only (R × [Selected shape])
2. Summarize what is unsolved

   * any requirements still Undecided
   * any ❌ in the selected shape column
   * any ⚠️ parts (must become spikes or be resolved)

⠀
This prevents re-litigating the whole space.

---

## Spikes (Investigations for Unknowns)

A spike is how you convert ⚠️ into concrete understanding.

### File Management

Always create spikes as standalone files:

* spike.md or spike-[topic].md

### Purpose

* Learn how the current system works in the relevant area
* Identify the concrete steps needed to implement a part
* Enable informed decisions after learning (spike ≠ decision)

### Structure

```
## [Component] Spike: [Title]

### Context
Why this investigation is needed.

### Goal
What we want to learn.

### Questions

| # | Question |
|---|----------|
| **X1-Q1** | Where is the [X] logic today? |
| **X1-Q2** | What changes are needed to achieve [Y]? |

### Acceptance
Spike is complete when we can describe [the understanding we’ll have].
```

---

## Breadboarding (Detailing a Selected Shape)

Breadboarding turns a selected shape into concrete affordances and wiring.

### When to Breadboard

Use breadboarding when you need to:

* map existing code to understand change points
* translate a high-level shape into concrete UI/Non-UI affordances
* reveal orthogonal concerns (independent parts)

### Breadboard Outputs

* UI Affordances table
* Non-UI Affordances table
* Wiring diagram grouped by Place

### Tables Are the Source of Truth

The affordance tables define the breadboard. The diagram is a rendering.

When updating:

1. Update the tables first
2. Then update the diagram to match

⠀
### CURRENT Is Reserved

Use CURRENT to describe the existing behavior (baseline).

---

## Detailing a Shape

When expanding a selected shape, use Detail X (not a new shape letter):

```
## A: First approach
## B: Second approach

## Detail B: Concrete affordances
```

Shape letters are mutually exclusive alternatives; detailing is not.

---

## Documents

Shaping produces up to four documents.

|  Document  |  Contains  |  Purpose  |
|---|---|---|
|  Frame  |  Source, Problem, Outcome  |  The “why” and success criteria  |
|  Shaping doc  |  Requirements, Shapes, Fit Check, Parts, Breadboard  |  Ground truth during shaping  |
|  Slices doc  |  Slice definitions, sliced breadboard, slices grid  |  Ground truth for incremental delivery  |
|  Slice plans  |  V1-plan.md, V2-plan.md…  |  Implementation steps per slice  |
---

## Frame Document

Frame captures the why before solution details dominate.

### Capturing Source Material (Verbatim)

When the human provides raw source material (emails, quotes, scenarios), capture it verbatim:

```
## Source

> [verbatim text]
```

Then interpret it into:

```
## Problem
- ...

## Outcome
- ...
```

---

## Big Picture Document

Big Picture is a one-page summary that stays in sync with lower-level docs.

### Structure — Exactly Three Sections

```
# [Feature Name] — Big Picture

**Selected shape:** [Letter] ([Short description])

---

## Frame

### Problem
- ...

### Outcome
- ...

---

## Shape

### Fit Check (R × [Selected Shape])
[table]

### Parts
[table with Flag column]

### Breadboard
[full Mermaid breadboard + legend]

---

## Slices

[sliced breadboard]

[slices grid]
```

### Fit Check in Big Picture

Show only the selected shape column (R × [Selected]).

### Parts in Big Picture

Always include Flag column and show ⚠️ where applicable.

### Breadboard Legend (Required)

After the breadboard, include:

```
**Legend:**
- **Pink nodes (U)** = UI affordances (things users see/interact with)
- **Grey nodes (N)** = Code affordances (data stores, handlers, services)
- **Solid lines** = Wires Out (calls, triggers, writes)
- **Dashed lines** = Returns To (return values, reads)
```

---

## Slicing

After a shape is breadboarded, slice it into vertical increments.

### Rule

Every slice must end in demo-able UI.

If a slice produces no visible output, it’s a horizontal layer and should be re-cut.

### Slices Section Must Contain

1. Sliced breadboard (slice boundaries)
2. Slices grid (status + demo per slice)

⠀
### Slices Grid Format

* Empty header row
* Typically 2×3 (6 slices) or 3×3 (9 slices)
* Each cell includes:

  * linked title when plan exists
  * status ✅ COMPLETE / ⏳ PENDING
  * 3–4 bullets
  * italicized demo line
  * use <br> and spacer bullets • &nbsp;

Example:

```
|  |  |  |
|:--|:--|:--|
| **[V1: WIDGET WITH REAL DATA](./feature-v1-plan.md)**<br>✅ COMPLETE<br><br>• ...<br><br>*Demo: ...* | **V2: ...**<br>⏳ PENDING<br><br>• ...<br><br>*Demo: ...* | ... |
```

---

## Agent Operating Rules (Behavioral Defaults)

1. Never abbreviate artifacts. When showing R / shapes / fit checks / parts, show full tables.
2. Prefer editing artifacts over narration. The doc is the output.
3. Don’t “solve” undecided requirements. Propose candidates, keep status Undecided until aligned.
4. Convert unknowns to spikes. Mark ⚠️ and create spike questions; do not hand-wave.
5. Keep docs synchronized. Apply Multi-Level Consistency every time.
6. Always end with the next concrete shaping action. (Add R, add shape, run fit check, breadboard, slice, create spike.)