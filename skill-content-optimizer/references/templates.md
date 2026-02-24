# Templates and checklists

## Minimal SKILL.md skeleton (copy + edit)

---
name: <skill-name>
description: <one sentence: what it enables + when to use it>
---

# <Skill title>

## Workflow
1. <step 1: setup / inputs>
2. <step 2: core procedure>
3. <step 3: produce outputs>
4. <step 4: handle edge case (only if common)>
5. <step 5: finalize>

## Verification
- Fast check: <quick command or sanity check>
- Full check: <verifier/test command>
- Common failures:
  - <symptom> → <cause> → <fix>

## Example
<short end-to-end example with placeholders>

---

## Minimal AGENTS.md / CODEX.md skeleton (copy + edit)

# Agent instructions (minimal)

## Quick start
- Install: `<one canonical install command>`
- Test: `<the acceptance test command>`
- Lint/format (only if enforced): `<command>`
- Run: `<command>`

## Hard requirements (only what is required to pass)
- Tooling: `<required package manager/tool>`
- Versions: `<only if strict>`
- Environment variables: `<only if required>`

## Do / Don’t
- Do: run `<targeted tests>` before finalizing changes.
- Do: keep changes scoped to the issue requirements.
- Don’t: run the full test matrix unless the acceptance gate requires it.
- Don’t: refactor unrelated code unless needed for the fix.

---

## Compression checklist (mandatory)

- Remove background that does not change actions.
- Replace paragraphs with:
  - numbered steps
  - short checklists
- Delete duplicated content; link instead.
- Convert “always do X” into “do X **only if** <acceptance requires it>”.
- Keep one canonical command per action (avoid multiple alternatives).

---

## A/B evaluation checklist (keep lightweight)

For each content change (Skill or context file):
- Evaluate baseline (no added content): pass rate / failure modes / timeouts / step count.
- Evaluate with content: same metrics.
- If:
  - pass rate improves and overhead is acceptable → keep
  - pass rate flat and overhead rises → prune
  - pass rate drops → remove or rewrite
