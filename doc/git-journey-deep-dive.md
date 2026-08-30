# The Git Journey: Why It's the Centerpiece

## The Gap in Current Tooling

Every agent framework today gives agents the ability to read code. Some give
access to git commands. A few provide structured project descriptions (CLAUDE.md,
.cursorrules, etc.). But none synthesize the **temporal narrative** of how a
codebase evolved.

This is the gap the git journey fills.

---

## What "Intent" Actually Means in Code

When we say the wiki "tells the intent of the project," we mean something specific.
Intent in a codebase has three layers:

### Layer 1: Local intent (what this function does)

Agents handle this well today. They read the function, understand its behavior,
and can modify or extend it correctly.

### Layer 2: Structural intent (why this module exists)

Agents partially handle this. They can infer from directory names, imports, and
README mentions. But they often miss the *boundaries* — why auth is separate from
user management, why there's a `legacy/` directory that shouldn't be touched.

The wiki's architecture and module pages address this layer.

### Layer 3: Temporal intent (how and why the code became what it is)

Agents have **zero** access to this today. This is the layer where questions like
these live:

- "We moved to TypeScript in month 4 — new code should be .ts, not .js"
- "The `utils/` directory was a dumping ground we're trying to decompose"
- "We tried microservices in v1.5 and pulled back to a modular monolith in v2.0"
- "The `permissions` module looks over-engineered because it was built to
  support a multi-tenant feature that was later descoped"

These are the kinds of things a senior contributor knows intuitively and a new
contributor learns through months of osmosis. The git journey makes them explicit.

---

## How the Journey Preserves Patterns

### Convention tracking across time

The journey's epoch structure shows when conventions changed:

```
Epoch 1 (Jan–Mar 2024): Foundation
  - Project initialized with JavaScript, Express, MongoDB
  - Naming: camelCase throughout

Epoch 2 (Apr–Jun 2024): TypeScript Migration
  - Codebase migrated from JS to TS
  - New naming convention: PascalCase for types, camelCase for functions

Epoch 3 (Jul–Sep 2024): API Redesign
  - REST endpoints replaced with GraphQL
  - New pattern: resolvers in dedicated files, one per entity
```

An agent reading this in October 2024 knows:
- Write TypeScript, not JavaScript
- New API code should use GraphQL, not REST
- Resolvers go in their own files

Without the journey, the agent sees a codebase with JS files, TS files, REST
endpoints, and GraphQL resolvers — and has no way to know which pattern is
current.

### Architectural decision records (implicit)

The journey functions as an implicit ADR (Architectural Decision Record) system.
Each epoch captures:

- **Context** — what the project looked like at that point
- **Decision** — what changed (migration, refactor, new feature)
- **Consequences** — what the codebase looks like after

This is extracted automatically from git history, not maintained manually.

### The "don't go back" signal

Perhaps the most valuable signal: when the journey shows a migration *away* from
something, it tells the agent **not to reintroduce that pattern**. This is
information that doesn't exist anywhere in the current codebase — the absence of
a pattern is invisible, but the journey records when and why it was removed.

---

## Keeping the Same Mindset

"Keeping the same mindset" means an agent's modifications should be
indistinguishable from what the existing team would produce. The journey enables
this through:

### 1. Coding style evolution

The journey tracks when the team adopted linters, formatters, or style changes.
An agent knows to follow the *latest* style, not the average of all historical
styles.

### 2. Abstraction philosophy

Some teams prefer thin abstractions and explicit code. Others build deep
abstraction layers. The journey reveals this through the pattern of refactoring
epochs — frequent abstraction-building phases suggest the team values DRY, while
a flat history suggests they prefer simplicity.

### 3. Testing philosophy

The journey shows when tests were introduced, what kind of tests dominate (unit
vs integration vs e2e), and whether the team is increasing or decreasing test
coverage. An agent adding a feature matches the current testing approach.

### 4. Velocity and scope

The journey's epoch density reveals whether the team ships small increments or
large batches. An agent adapts: in a high-velocity repo, it proposes focused
single-concern changes; in a batch-oriented repo, it can propose broader
refactors.

---

## The Journey as Update/Modify Guide

When an agent needs to update or modify existing code, the journey answers three
critical questions:

### "Is this code active or frozen?"

Code that was last touched 18 months ago in a "Foundation" epoch and hasn't been
modified since is likely stable/frozen. The agent should be conservative —
minimize changes, don't refactor, match the existing style exactly even if it's
outdated.

Code that was modified in the latest epoch is active. The agent has more latitude
to improve patterns, align with newer conventions, and propose structural changes.

### "What's the direction of change?"

If the journey shows that the team has been incrementally migrating from pattern A
to pattern B, the agent should:
- Write new code using pattern B
- When modifying code using pattern A, convert it to pattern B if the scope is
  reasonable
- Never introduce new pattern A code

### "What's the acceptable scope of a change?"

The journey's PR history reveals how large changes typically are. If most PRs
touch 2-5 files, the agent shouldn't propose a 50-file refactor. If the team
regularly does large migrations, the agent can be more ambitious.

---

## What This Means for code-journey

The `code-journey` project itself is a bet on this thesis: that the narrative
layer between raw git history and agent action is the missing piece in
agent-assisted development.

The project's value proposition, broken down:

1. **For agents**: structured context that makes their output fit the project
2. **For teams**: documentation that maintains itself and captures institutional
   knowledge
3. **For the ecosystem**: a standard format for project narratives that any agent
   framework can consume

The git journey is the centerpiece because it's the only artifact that captures
what no other tool does — the story of the code, told by the code itself.
