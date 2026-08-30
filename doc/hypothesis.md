# Hypothesis: Code-Wiki as Agent Context

## The Claim

A generated code-wiki — especially one that includes a **git journey** — provides
structured context that makes AI agents materially better at working on a codebase.
Better in two measurable ways:

1. **Intent preservation** — the agent understands *why* the code is the way it is,
   not just *what* the code does. This reduces drift (changes that conflict with
   the project's direction).
2. **Pattern continuity** — the agent absorbs the established idioms, naming
   conventions, architectural boundaries, and contribution rhythm, producing
   changes that look like they came from the same team.

---

## Why This Matters

Agents today start every session cold. They can read files, grep for patterns,
and run tests — but they have no sense of the *story* behind the code. Consider
what a new human contributor does when they join a project:

| What a human learns | Where they learn it | Agent equivalent today |
|---------------------|---------------------|------------------------|
| What the project does | README | Reads README |
| How modules fit together | Architecture docs, asking a colleague | Infers from imports — often wrong |
| Why module X exists separately from Y | Team lore, PR discussions | No signal at all |
| What was tried and abandoned | Git history, PR comments | No signal at all |
| What the team values (speed? correctness? simplicity?) | Code review culture, commit patterns | No signal at all |
| What's coming next | Roadmap, open issues | No signal at all |

The wiki fills rows 2–5. The git journey specifically covers rows 3–4, which are
the hardest to reconstruct from code alone.

---

## The Git Journey Is the Key Differentiator

Architecture docs are useful but static — they describe the *current* state. The
git journey adds the **temporal dimension**:

### What the journey captures

- **Epoch structure** — how the project evolved in phases (prototyping → first
  release → scaling → refactoring). An agent that knows "we're in a stabilization
  phase" won't propose speculative features.
- **Decision archaeology** — which approaches were tried, merged, and sometimes
  reverted. An agent reading that "the team moved from REST to GraphQL in v2.0"
  won't suggest adding new REST endpoints.
- **Contribution patterns** — solo-developer vs. team, burst vs. steady cadence,
  areas of active development vs. stable/frozen code. An agent can prioritize
  where to tread carefully.
- **Naming evolution** — if the project renamed `User` to `Account` in epoch 3,
  the agent won't reintroduce `User` in epoch 4.

### What raw git log cannot do

Running `git log` gives an agent 500 lines of hashes and one-line messages. The
wiki's journey page synthesizes this into a narrative with:

- Named epochs with date ranges
- Narrative paragraphs explaining *why* things changed
- A Mermaid gitGraph showing the branching/merging shape
- Highlighted PRs that represent turning points

This is the difference between giving someone a phone book and giving them a
biography.

---

## Concrete Agent Scenarios

### Scenario 1: Bug fix in a mature module

**Without wiki:** Agent reads the module, finds the bug, patches it. Uses a
function name that conflicts with a deprecated name from 6 months ago. Adds a
helper that duplicates a utility the team already has in a shared module.

**With wiki:** Agent reads `modules/auth.md` and sees the module's public API.
Reads `journey.md` and sees that `validateToken` was renamed from `checkAuth` in
v2.1. Reads `architecture.md` and sees the shared utilities module. Produces a
patch that fits cleanly.

### Scenario 2: Adding a new feature

**Without wiki:** Agent scaffolds the feature using its generic best practices.
Creates a new directory structure that doesn't match the existing convention.
Uses camelCase in a project that uses snake_case after epoch 2.

**With wiki:** Agent reads the architecture page, sees the layered structure, and
places the feature in the right layer. Reads the glossary for domain terminology.
Reads the journey to understand that the team moved from camelCase to snake_case
and why. Output matches the project's evolved conventions.

### Scenario 3: Code review / PR assessment

**Without wiki:** Agent checks syntax, tests, and obvious bugs. Cannot assess
whether the change aligns with the project's direction.

**With wiki:** Agent cross-references the PR against the journey's current epoch
and the architecture's stated design decisions. Can flag: "This PR adds a new
REST endpoint, but the project migrated to GraphQL in v2.0 — is this intentional?"

---

## What Makes This More Than Just Documentation

Traditional documentation is written for humans, maintained (or not) by humans,
and goes stale. The code-wiki has three properties that make it specifically
valuable for agents:

### 1. It's generated, not maintained

The wiki is produced by analyzing the repo — git history, file structure, imports,
README. It can be regenerated at any time. This means it stays current without
human effort, which is the main failure mode of traditional docs.

### 2. It's structured for machine consumption

While readable by humans, the wiki uses consistent templates, Mermaid diagrams
(which are parseable syntax trees), and standardized sections. An agent can
reliably find "the module dependency graph is in architecture.md under
`## Module Dependencies`" — no ambiguity.

### 3. It encodes the narrative arc

This is the novel contribution. Static docs describe *what is*. The journey page
describes *what was, what changed, and why*. This temporal context is what lets
an agent reason about:

- Is this codebase in active development or maintenance mode?
- Is this module being built up or being deprecated?
- Is this pattern the current convention or a legacy holdover?

---

## How to Validate

### Test 1: Side-by-side agent task comparison

1. Pick 5 real repositories with meaningful git history (100+ commits, multiple
   contributors, at least one major refactor).
2. Generate the wiki for each.
3. Give an agent the same task on each repo — once with the wiki in context, once
   without.
4. Evaluate outputs on:
   - **Convention adherence** — does the output match naming, structure, and style?
   - **Architectural fit** — is the change in the right place, using the right
     abstractions?
   - **Historical awareness** — does the agent avoid repeating known mistakes or
     contradicting established decisions?
   - **Completeness** — does the agent find and use existing utilities instead of
     reinventing them?

### Test 2: Decision justification

1. Give an agent a wiki and ask it to explain *why* the code is structured a
   certain way.
2. Compare its explanation against what the actual team would say.
3. Score on accuracy: does the journey-informed explanation match reality?

### Test 3: Onboarding time proxy

1. Present a new developer with a repo — once with wiki, once without.
2. Ask them to answer 10 questions about the project (architecture, conventions,
   history, who to ask about X).
3. Measure time and accuracy. The wiki should reduce onboarding friction for
   humans and agents alike.

---

## Expected Outcomes

| Metric | Without wiki | With wiki (expected) |
|--------|-------------|---------------------|
| Convention match rate | ~60% (agent guesses from local context) | ~90% (explicit in wiki) |
| Architectural violations | Common (wrong layer, wrong module) | Rare (architecture.md is explicit) |
| Deprecated pattern reuse | Frequent (no signal) | Rare (journey.md flags transitions) |
| Utility duplication | Common (agent can't survey full repo) | Rare (module pages list public APIs) |
| Meaningful PR review comments | Surface-level (syntax, tests) | Directional (alignment with project goals) |

---

## Risks and Limitations

- **Stale wiki** — if the wiki isn't regenerated, it drifts from reality. Mitigation:
  the wiki is designed to be regenerated cheaply (single command, no manual input).
- **Wrong inferences** — the generator infers intent from commit messages and code
  structure. Commits with bad messages → bad narrative. Mitigation: the journey
  prioritizes PRs (which tend to have better descriptions) and tags (which are
  explicit milestones).
- **Context window cost** — a full wiki for a large repo might be 10-20K tokens.
  Mitigation: agents can read only the relevant pages (module page for a module
  task, journey for a design decision, etc.).
- **Circular dependency** — if agents use the wiki to write code, and the wiki is
  generated from that code, errors could compound. Mitigation: the wiki is
  regenerated from git history (ground truth), not from previous wiki output.

---

## Summary

The code-wiki hypothesis is: **structured, generated documentation that includes
a narrative git history gives agents the temporal and architectural context they
need to produce changes that are not just correct, but *fitting* — aligned with
the project's trajectory, conventions, and intent.**

The git journey is the centerpiece because it provides what no other artifact
does: the *why behind the what*, the *evolution behind the state*, and the
*decisions behind the code*.
