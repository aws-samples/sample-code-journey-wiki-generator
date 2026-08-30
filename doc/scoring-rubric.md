# Scoring Rubric: Agent Output Fitness

## Overview

This rubric scores how well an AI agent's output **fits** a codebase — not just
correctness, but alignment with the project's conventions, architecture, history,
and vocabulary. Each trial output is scored on 4 dimensions, each on a 1–5 scale.

**Fitting Score** = mean of all 4 dimension scores.

---

## Dimension 1: Convention Adherence

*Does the agent's output follow the project's established coding conventions?*

Conventions include: naming (casing, prefixes, suffixes), file organization,
import style, error handling patterns, formatting, testing patterns, and
documentation style.

| Score | Label | Behavioral Anchor |
|-------|-------|-------------------|
| **5** | Native | Output is indistinguishable from the existing team's code. Uses the *current* naming convention (not a legacy one). File placement, import ordering, and formatting match project norms exactly. A reviewer would not flag any style issues. |
| **4** | Minor drift | Output follows conventions in substance but has 1–2 minor deviations: e.g., slightly different import ordering, a variable name that's valid but not idiomatic for this project. A reviewer would approve with a nit. |
| **3** | Mixed signals | Output follows some conventions but violates others. E.g., uses the right naming casing but wrong file location, or matches the current pattern in some places and a legacy pattern in others. A reviewer would request changes. |
| **2** | Outsider style | Output uses a generic "best practice" style that doesn't match the project. Naming, file structure, or patterns come from the agent's training data rather than the repo's actual conventions. Functionally correct but stylistically alien. |
| **1** | Conflict | Output actively contradicts established conventions. Uses a deprecated pattern, introduces a naming scheme the project abandoned, or places code in a location that violates known project structure. |

### How to Score

1. Identify the project's current conventions from the most recent epoch of development.
2. Compare the agent's output against these conventions on: naming, file placement, import style, error handling, and test style.
3. Count deviations. 0 = score 5, 1–2 minor = score 4, mix of adherent and non-adherent = score 3, systematic non-adherence = score 2, contradicts known conventions = score 1.

### Worked Example

**Repo**: A TypeScript project that migrated from `camelCase` file names to `kebab-case` in epoch 3.

- Agent creates `userProfile.ts` → **Score 1** (uses the deprecated convention)
- Agent creates `user-profile.ts` but uses `var` instead of `const` → **Score 3** (right naming, wrong variable style)
- Agent creates `user-profile.ts`, uses `const`, matches import style → **Score 5**

---

## Dimension 2: Architectural Fit

*Does the agent's output respect the project's architectural boundaries, layers, and module structure?*

Architectural fit includes: placing code in the right module/layer, using the
correct abstraction level, respecting dependency direction, and reusing existing
utilities instead of duplicating.

| Score | Label | Behavioral Anchor |
|-------|-------|-------------------|
| **5** | Architecturally native | Code is placed in the correct module and layer. Uses existing abstractions (e.g., calls the shared utility instead of reimplementing). Respects dependency direction. A senior contributor would say "that's exactly where I'd put it." |
| **4** | Right neighborhood | Code is in the right general area but has a minor placement issue: e.g., a helper function that could live in the shared utils but was placed in the module (functional but not ideal). No architectural violations. |
| **3** | Functional but misplaced | Code works but violates an architectural boundary: e.g., a database query in a controller (bypassing the data layer), or a new module that duplicates functionality from an existing one. The change would require refactoring to fit properly. |
| **2** | Structural mismatch | Code introduces a new structural pattern that conflicts with the existing architecture: e.g., a new top-level directory in a project that uses a flat `src/` structure, or inline SQL in a project that uses an ORM exclusively. |
| **1** | Architectural violation | Code breaks a fundamental architectural rule: e.g., a circular dependency between layers, direct database access from the UI layer, or a new service that bypasses the project's established communication pattern (e.g., direct DB calls in a project using message queues). |

### How to Score

1. Identify the project's architecture from `architecture.md` (or by reading the code structure).
2. Determine where the change *should* go based on existing patterns.
3. Compare the agent's placement against this. Exact match = 5, close = 4, works but wrong layer = 3, new conflicting pattern = 2, fundamental violation = 1.

### Worked Example

**Repo**: A Node.js API with layers: `routes/` → `services/` → `repositories/` → DB.

- Agent adds a rate limiter as middleware in `middleware/` (where other middleware lives) → **Score 5**
- Agent adds rate limiting logic inside `routes/api.ts` but extracts it to a function → **Score 3** (right idea, wrong layer)
- Agent adds rate limiting by directly querying Redis in the route handler, bypassing the service layer → **Score 2**

---

## Dimension 3: Historical Awareness

*Does the agent avoid deprecated patterns, use current conventions, and align with the project's trajectory?*

This is the dimension most directly affected by `journey.md`. It tests whether
the agent understands what the project *was* vs. what it *is* vs. where it's
*going*.

| Score | Label | Behavioral Anchor |
|-------|-------|-------------------|
| **5** | Trajectory-aligned | Agent's output reflects awareness of the project's direction. Uses the current pattern (not legacy). If a migration is in progress, writes in the target pattern. Could cite why: "the project moved from X to Y." |
| **4** | Current-aware | Agent uses the current pattern but shows no explicit awareness of why. Doesn't reintroduce deprecated patterns, but also doesn't demonstrate understanding of the migration direction. Correct by observation, not by understanding. |
| **3** | Pattern-ambiguous | Agent's output mixes current and legacy patterns. E.g., uses the new ORM for reads but falls back to raw SQL for writes (both exist in the codebase). The output is not wrong per se, but it doesn't clearly pick a side. |
| **2** | Legacy-defaulting | Agent defaults to the older/more-common pattern in the codebase (because more code exists in the old style), missing that it's been deprecated or superseded. E.g., uses callbacks in a project that migrated to async/await. |
| **1** | Regression | Agent actively reintroduces a pattern the project explicitly moved away from. E.g., adds a new REST endpoint in a project that migrated to GraphQL, or uses a class name the project renamed 6 months ago. |

### How to Score

1. Identify the convention trap in the task (see task template's "Convention trap" field).
2. Determine whether the agent fell into the trap, avoided it, or was ambiguous.
3. Fell into trap = score 1–2 (depending on severity). Ambiguous = score 3. Avoided by observation = score 4. Avoided with demonstrated understanding = score 5.

### Worked Example

**Repo**: A Python project that migrated from `unittest` to `pytest` in epoch 4. 70% of existing tests still use `unittest` (not yet migrated), but all new tests use `pytest`.

- Agent writes new tests using `unittest.TestCase` → **Score 2** (legacy-defaulting: more `unittest` exists, but `pytest` is current)
- Agent writes `pytest` tests but also creates a `unittest` helper → **Score 3** (mixed)
- Agent writes pure `pytest` tests → **Score 4** (current-aware)
- Agent writes `pytest` tests and notes "following the project's migration to pytest" → **Score 5** (trajectory-aligned)

---

## Dimension 4: Completeness & Reuse

*Does the agent use existing utilities, avoid duplication, handle the right edge cases, and produce a complete solution?*

This tests whether the agent surveyed the codebase (or read the wiki's module
pages) well enough to find and reuse what's already there.

| Score | Label | Behavioral Anchor |
|-------|-------|-------------------|
| **5** | Fully integrated | Agent finds and reuses existing utilities, types, and helpers. No duplication. Handles edge cases consistent with how the rest of the codebase handles them. Solution is complete and needs no follow-up. |
| **4** | Mostly integrated | Agent reuses major utilities but misses one minor reuse opportunity (e.g., reimplements a small helper that exists in a shared module). Solution is complete. |
| **3** | Partial reuse | Agent reuses some existing code but duplicates a non-trivial piece that already exists. Or the solution is functional but incomplete (misses an edge case the codebase normally handles). |
| **2** | Isolated implementation | Agent writes the feature from scratch without checking for existing utilities. The code works but duplicates significant existing functionality. Multiple things a reviewer would flag as "we already have this." |
| **1** | Incomplete or broken | Agent's output is incomplete (missing files, half-implemented), introduces obvious bugs, or ignores standard edge cases (e.g., no error handling in a codebase that consistently handles errors). |

### How to Score

1. List the existing utilities, types, and helpers relevant to the task (from module pages or by reading the code).
2. Check whether the agent found and used them.
3. Check edge case handling against the codebase's established patterns.
4. All reused + complete = 5. Mostly reused = 4. Some duplication = 3. Wrote from scratch = 2. Incomplete = 1.

### Worked Example

**Repo**: An Express API with a shared `lib/errors.ts` that exports `AppError`, `NotFoundError`, `ValidationError`.

- Agent adds a new endpoint and uses `throw new ValidationError(...)` from the shared module → **Score 5**
- Agent adds a new endpoint, uses `AppError` but creates a local `BadRequestError` class instead of using `ValidationError` → **Score 4**
- Agent creates a completely new error class hierarchy in the route file → **Score 2**

---

## Composite Scoring

### Per-Trial

| Field | Value |
|-------|-------|
| Trial ID | `{repo}-{task-type}-{condition}` (e.g., `R1-bugfix-treatment`) |
| Convention Adherence | 1–5 |
| Architectural Fit | 1–5 |
| Historical Awareness | 1–5 |
| Completeness & Reuse | 1–5 |
| **Fitting Score** | Mean of above 4 |
| Notes | Free text — notable observations |

### Per-Condition Aggregates

| Aggregate | Computation |
|-----------|-------------|
| Mean fitting score | Mean of all 15 fitting scores for the condition |
| Per-dimension mean | Mean of each dimension across 15 tasks |
| Convention trap rate | Count of tasks where Historical Awareness ≤ 2, divided by 15 |
| Strong fit rate | Count of tasks where Fitting Score ≥ 4.0, divided by 15 |

---

## Benchmark Targets

These are the numbers that confirm or reject the hypothesis.

### Primary Targets

| Condition | Fitting Score Target | Interpretation |
|-----------|---------------------|----------------|
| **Control** (no wiki) | 2.5 – 3.2 | Agents are decent at correctness but mediocre at fitness |
| **Treatment** (full wiki) | ≥ 4.0 | Wiki makes agent output consistently fitting |
| **Ablation** (wiki minus journey) | 3.4 – 3.8 | Architecture + modules help, but journey adds the edge |

### Per-Dimension Targets

| Dimension | Control | Treatment | Ablation | Why |
|-----------|---------|-----------|----------|-----|
| Convention Adherence | 2.5–3.0 | ≥ 4.0 | 3.5–4.0 | Module pages make conventions explicit |
| Architectural Fit | 2.5–3.5 | ≥ 4.2 | ≥ 4.0 | Architecture.md directly addresses this; journey adds little |
| Historical Awareness | 1.5–2.5 | ≥ 4.0 | 2.0–3.0 | **This is where journey.md must shine.** Without it, agents have no temporal signal. |
| Completeness & Reuse | 2.5–3.0 | ≥ 4.0 | 3.5–4.0 | Module pages list public APIs, reducing duplication |

### Key Deltas to Watch

| Comparison | Expected Δ | What it proves |
|------------|-----------|----------------|
| Treatment − Control (fitting) | ≥ 0.8 | Wiki materially helps |
| Treatment − Ablation (fitting) | ≥ 0.4 | Journey specifically helps |
| Treatment − Ablation (historical awareness) | ≥ 1.0 | Journey is the source of temporal awareness |
| Treatment − Control (convention trap rate) | ≥ 40pp decrease | Wiki prevents deprecated pattern reuse |

---

## Scoring Checklist (for evaluators)

Before scoring each trial:

- [ ] Confirm you do not know which condition produced this output
- [ ] Read the task's "Ground truth" and "Convention trap" fields
- [ ] Read the agent's full output
- [ ] Score each dimension independently (do not let one dimension bias another)
- [ ] Write at least one sentence of notes explaining the Historical Awareness score (this is the most subjective dimension)
- [ ] Double-check: if you scored Historical Awareness ≥ 4, can you point to specific evidence in the output?

---

## Score Sheet Template

```markdown
## Trial: [trial-id]

**Task:** [task title]
**Condition:** [blinded — filled in after scoring]

| Dimension | Score | Evidence |
|-----------|-------|----------|
| Convention Adherence | /5 | |
| Architectural Fit | /5 | |
| Historical Awareness | /5 | |
| Completeness & Reuse | /5 | |
| **Fitting Score** | **/5** | |

**Notes:**
```
