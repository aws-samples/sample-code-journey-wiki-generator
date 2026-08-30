# Value Proposition: Code-Wiki for Agent-Assisted Development

## The Problem Agents Face Today

When an AI agent is dropped into a codebase, it sees a **snapshot** — files,
directories, imports, tests. It has no access to:

- The decisions that shaped the current structure
- The patterns the team chose (and the ones they abandoned)
- The trajectory of the project (growing, stabilizing, migrating)
- The conventions that evolved over time (not just the ones visible now)

This is like hiring a contractor who can read blueprints but has never talked to
the architect. They can build *something*, but it won't necessarily fit the vision.

---

## What the Code-Wiki Provides

### For **intent** (journey.md)

The git journey translates raw commit history into a narrative that answers:

- "What phase is this project in?" → The agent knows whether to be conservative
  or exploratory.
- "What did the team try before?" → The agent avoids re-proposing rejected
  approaches.
- "What's the migration direction?" → The agent writes new code in the target
  pattern, not the legacy one.

**Example:** A project migrated from callbacks to async/await in Q3 2024. Without
the wiki, an agent might see both patterns coexisting and pick callbacks (more
examples in the codebase). With the journey, it knows async/await is the current
direction.

### For **patterns** (architecture.md + modules/)

Architecture and module pages make implicit patterns explicit:

- Layer boundaries — what belongs where
- Module APIs — what's already available (prevents reinvention)
- Dependency direction — which modules can import from which
- Naming conventions — derived from actual codebase usage

**Example:** The architecture page shows that all database access goes through a
`data/` layer. An agent adding a new feature puts its queries there instead of
inlining SQL in the controller.

### For **vocabulary** (glossary.md)

Domain terms prevent agents from inventing new names for existing concepts:

- If the codebase calls it a "workspace", the agent won't introduce "project"
- If the codebase calls it "ingest", the agent won't call it "import"
- Custom abbreviations are defined once: `txn = transaction`, `svc = service`

### For **team dynamics** (contributors.md)

Contribution patterns tell the agent about the project's social context:

- Solo project → the agent has more latitude to propose structural changes
- Active team → the agent should be more conservative, flag breaking changes
- Module ownership patterns → the agent can suggest who should review a change

---

## The Feedback Loop

The wiki creates a positive feedback loop for agent-assisted development:

```
  Generate wiki from repo
         │
         ▼
  Agent reads wiki before acting
         │
         ▼
  Agent produces fitting changes
         │
         ▼
  Changes merge into repo
         │
         ▼
  Regenerate wiki (captures new history)
         │
         └──────► back to top
```

Each cycle, the wiki gets richer (more history, more patterns), and the agent
gets better context. The wiki is never manually maintained — it's always derived
from the source of truth (the repo itself).

---

## Comparison: Wiki vs. Alternatives

| Approach | Pros | Cons |
|----------|------|------|
| **Raw file reading** | Always current | No structure, no narrative, huge token cost |
| **README only** | Standard, usually exists | Static, often outdated, no history |
| **CLAUDE.md / rules files** | Agent-specific, precise | Manually maintained, no temporal context |
| **git log / git blame** | Ground truth | Unstructured, noisy, expensive to parse |
| **Generated wiki** | Structured, temporal, regenerable, cheap to consume | Must be regenerated, inferences can be wrong |

The wiki is not a replacement for CLAUDE.md (which captures explicit team
preferences). It's complementary — CLAUDE.md says "do X", the wiki explains
"here's why X is the convention, here's when it was adopted, and here's what
came before it."

---

## The "Same Mindset" Argument

The strongest case for the wiki is this: when an agent reads the journey and
architecture of a project, it develops something analogous to the **mental model**
a senior contributor has. Not just "what files exist" but:

- "This project values simplicity over flexibility"
- "This module is being phased out in favor of that one"
- "The team prefers small, focused PRs"
- "This naming convention was a deliberate choice in v2"

This mental model is what lets a human contributor make judgment calls — not just
"does this code work?" but "does this code *belong*?" The wiki is an attempt to
give agents that same judgment.

---

## Practical Integration Points

### 1. Pre-task context loading

Before an agent starts any task, it reads:
- `wiki/index.md` — project overview
- `wiki/architecture.md` — where things go
- The relevant `wiki/modules/<name>.md` — what's already there
- `wiki/journey.md` (latest epoch) — current phase and conventions

### 2. PR review augmentation

An agent reviewing a PR reads:
- `wiki/architecture.md` — does the PR respect layer boundaries?
- `wiki/journey.md` — does the PR align with the project's direction?
- `wiki/glossary.md` — does the PR use the right domain terms?

### 3. Onboarding acceleration

A new contributor (human or agent) reads the wiki and gets:
- 5-minute project overview (index.md)
- Architectural mental model (architecture.md)
- Historical context (journey.md)
- Vocabulary alignment (glossary.md)

This replaces hours of "reading the code" with minutes of "reading the story."
