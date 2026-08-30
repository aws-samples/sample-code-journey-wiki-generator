# Wiki Generator — Requirements

## Overview

Generate a comprehensive, multi-page, Wikipedia-style wiki for any git repository. The wiki documents architecture, modules, git history, contributors, and domain terminology.

---

## User Stories

### US-1: Generate a full repository wiki
**As a** developer exploring an unfamiliar codebase,
**I want to** run a single command that generates a complete wiki for the repository,
**So that** I can quickly understand the project's architecture, modules, history, and conventions.

**Acceptance Criteria:**
- [ ] Wiki is generated in a `wiki/` directory at the repository root (or a user-specified path)
- [ ] Wiki contains: `index.md`, `architecture.md`, `journey.md`, `contributors.md`, `glossary.md`
- [ ] Wiki contains a `modules/` subdirectory with one page per qualifying module (3+ source files)
- [ ] All pages use GitHub-flavored Markdown with working relative cross-links
- [ ] All diagrams use Mermaid syntax (flowchart, sequence, gitGraph)
- [ ] Generation works on any git repository regardless of language or framework

### US-2: Project overview at a glance
**As a** new team member,
**I want to** open `index.md` and see a summary with key stats,
**So that** I can orient myself without reading the entire codebase.

**Acceptance Criteria:**
- [ ] `index.md` includes: project name, one-paragraph summary, quick stats table (languages, LOC, commits, contributors, age, license)
- [ ] `index.md` includes a table of contents linking every other wiki page
- [ ] Getting started section is included if the README contains setup instructions

### US-3: Understand system architecture
**As a** developer planning a feature,
**I want to** see architecture diagrams and module dependency graphs,
**So that** I can understand how components relate before making changes.

**Acceptance Criteria:**
- [ ] `architecture.md` includes a Mermaid component diagram (flowchart TD with subgraphs for layers)
- [ ] `architecture.md` includes a Mermaid module dependency graph (graph LR)
- [ ] `architecture.md` includes a data flow sequence diagram if a clear request-response pattern exists
- [ ] Key design decisions are listed with rationale
- [ ] External dependencies are listed in a table with purpose and version

### US-4: Explore individual modules
**As a** developer working on a specific module,
**I want to** read a dedicated page documenting that module's purpose, API, and internal flow,
**So that** I can understand its boundaries and behavior.

**Acceptance Criteria:**
- [ ] Each module with 3+ source files gets its own page in `modules/`
- [ ] Module page includes: summary, key files table, public API listing, internal flow diagram (Mermaid)
- [ ] Module page lists dependencies on other modules with cross-links
- [ ] Maximum 15 module pages (top 15 by file count if more exist)

### US-5: Understand project history
**As a** developer or manager,
**I want to** read a narrative timeline of the project's evolution,
**So that** I can understand how the codebase reached its current state.

**Acceptance Criteria:**
- [ ] `journey.md` opens with project creation date, total commits, and contributor count
- [ ] History is divided into epochs (tag-based if tags exist, time-based otherwise)
- [ ] Each epoch has a narrative paragraph (past tense, third person, encyclopedic tone) and key events list
- [ ] A Mermaid gitGraph shows major branches and merges (max ~20 nodes)
- [ ] Release history table is included if version tags exist
- [ ] Notable PRs section is included with title, author, date, and summary (enriched via GitHub CLI if available)

### US-6: Know who built what
**As a** team lead,
**I want to** see contributor profiles and patterns,
**So that** I can understand team dynamics and knowledge distribution.

**Acceptance Criteria:**
- [ ] `contributors.md` includes a ranked table: name, commit count, first/last active date, primary area
- [ ] Contribution pattern observations are included (solo vs team, frequency, primary areas)

### US-7: Understand domain terminology
**As a** new contributor,
**I want to** look up domain-specific terms used in the codebase,
**So that** I can understand naming conventions and business logic.

**Acceptance Criteria:**
- [ ] `glossary.md` contains domain terms extracted from module names, class/type names, README, and comments
- [ ] Each term has a definition and location where it is primarily used

### US-8: Handle edge cases gracefully
**As a** user running the generator on various repositories,
**I want** the tool to handle unusual repos without crashing or producing broken output,
**So that** it works reliably across different project shapes.

**Acceptance Criteria:**
- [ ] No git history (0 commits): skip journey.md and contributors.md, note in index.md
- [ ] No tags: use time-based epochs, omit release history table
- [ ] No PRs or merge commits: use regular commit messages, skip notable PRs section
- [ ] Monorepo with workspaces: each workspace treated as a module
- [ ] Tiny repo (< 5 source files): skip modules/ directory, fold info into architecture.md
- [ ] Large repo (> 1000 files): cap at 15 modules, sample LOC from 20 files, limit commit scan to 500
- [ ] No README: write summary from code analysis alone, note "No README found"
- [ ] Existing output directory without --force: warn and stop, do not overwrite

### US-9: Customizable output location
**As a** user,
**I want to** specify a custom output path,
**So that** I can place the wiki where my project conventions require.

**Acceptance Criteria:**
- [ ] Default output path is `wiki/` in the repo root
- [ ] `--output <path>` overrides the default
- [ ] `--force` allows overwriting an existing output directory
