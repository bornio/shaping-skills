# Shaping Skills

Codex skills for shaping and breadboarding — the methodology from [Shape Up](https://basecamp.com/shapeup) adapted for working with an LLM.

This repository is a maintained fork of [rjs/shaping-skills](https://github.com/rjs/shaping-skills) with additional skills.

**Case study:** [Shaping 0-1 walkthrough](https://x.com/rjs/status/2020184079350563263) walks through the full process of building a project from scratch using these skills. The source for that project is at [rjs/tick](https://github.com/rjs/tick).

## Skills

**`/shaping`** — Iterate on both the problem (requirements) and solution (shapes) before committing to implementation. Separates what you need from how you might build it, with fit checks to see what's solved and what isn't.

**`/breadboarding`** — Map a system into UI affordances, code affordances, and wiring. Shows what users can do and how it works underneath — in one view. Good for slicing into vertical scopes.

**`/breadboard-reflection`** — Find design smells in an existing breadboard and fix wiring, naming, and causality issues.

**`/llm-api-review-and-redesign`** — Review a REST/OpenAPI surface and produce a prioritized redesign plan for LLM/agent reliability.

**`/skill-content-optimizer`** — Create or revise Skills and repo context files to be minimal, procedural, and verifier-focused.

## Install

```bash
# Clone your fork
git clone https://github.com/bornio/shaping-skills.git ~/.local/share/shaping-skills
cd ~/.local/share/shaping-skills

# Symlink every folder that contains SKILL.md into ~/.codex/skills/
mkdir -p ~/.codex/skills
for d in */; do
  [ -f "${d}SKILL.md" ] || continue
  ln -sfn "$PWD/${d%/}" "$HOME/.codex/skills/${d%/}"
done
```

Each skill must be a direct child of `~/.codex/skills/` so Codex can discover it. Symlinks keep them updatable with `git pull`.

## Sync with upstream

```bash
cd ~/.local/share/shaping-skills
git remote get-url upstream >/dev/null 2>&1 || git remote add upstream https://github.com/rjs/shaping-skills.git
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```
