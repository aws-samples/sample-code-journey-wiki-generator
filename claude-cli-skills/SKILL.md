---
name: wiki
description: >-
  Generate a comprehensive multi-page wiki for the current repository.
  Creates Wikipedia-style pages with architecture diagrams, module docs,
  git journey timeline, contributor profiles, and glossary.
  Use when the user says "generate wiki", "create wiki", "build wiki",
  "document this repo", "write documentation", or invokes /wiki.
argument-hint: "[--force] [--output <path>]"
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
  - Bash(git:*)
  - Bash(gh:*)
  - Bash(wc:*)
  - Bash(mkdir:*)
  - Bash(which:*)
  - Bash(ls:*)
  - Bash(sort:*)
  - Bash(head:*)
  - Bash(basename:*)
  - Agent
---

# Wiki Generator

Generate a multi-page, Wikipedia-style wiki for the current repository. The wiki includes architecture diagrams (Mermaid), per-module documentation, a narrative git journey, contributor profiles, and a glossary.

## Output

Wiki pages are written to: **`wiki/`** in the current repository root.
Override with `--output <path>`.

Pages generated:
- `index.md` — Project summary, quick stats, table of contents
- `architecture.md` — System design with Mermaid component + dependency diagrams
- `modules/<name>.md` — One page per major module (public API, key files, flow diagrams)
- `journey.md` — Narrative timeline with gitGraph, epoch summaries, PR highlights
- `contributors.md` — Who built what, contribution patterns
- `glossary.md` — Domain terms extracted from the codebase

---

## Phase 0: Preflight

Before anything else, run these checks:

1. **Verify git repo:**
   ```
   git rev-parse --is-inside-work-tree
   ```
   If this fails, tell the user this skill requires a git repository and stop.

2. **Determine repo name:**
   ```
   git remote get-url origin
   ```
   Extract the repo name from the URL (e.g., `org/repo-name` → `repo-name`).
   Fallback: use `basename` of the current directory.

3. **Set output path:**
   - If `--output <path>` was passed, use that path.
   - Otherwise: `wiki/` in the current repository root.

4. **Check existing output:**
   If the output directory already exists and `--force` was NOT passed, warn the user:
   > "Wiki output directory already exists at `<path>`. Use `--force` to overwrite, or specify a different path with `--output <path>`."
   
   Stop and wait for user response. If `--force` was passed, proceed.

5. **Check `gh` CLI:**
   ```
   which gh
   ```
   Note whether `gh` is available. If available, also check auth:
   ```
   gh auth status
   ```
   If authenticated, PR enrichment will be used. If not, fall back to merge commit messages.

6. **Create output directories:**
   ```
   mkdir -p <output>/modules
   ```

---

## Phase 1: Parallel Data Gathering

Launch **two sub-agents in parallel** while also reading the README yourself.

### Agent 1: Codebase Scanner

Launch with the Agent tool (subagent_type: "Explore", thoroughness: "very thorough"):

**Prompt for Agent 1:**
> You are analyzing the structure of a code repository to generate wiki documentation.
>
> Read the file `references/module-detection.md` for module detection heuristics.
>
> Perform these tasks:
> 1. **Languages:** Glob for source files by extension. Count files per extension. Report the top 5 languages by file count.
> 2. **LOC estimate:** Sample up to 20 representative source files with `wc -l` and extrapolate.
> 3. **Modules:** Follow the detection heuristics from the reference file. For each module found, report: name, path, file count, entry point file, and a one-sentence description (from reading the entry point or local README).
> 4. **Key configs:** Note the presence of: Dockerfile, docker-compose.yml, CI configs (.github/workflows/, .gitlab-ci.yml, Jenkinsfile), infrastructure files (terraform/, k8s/).
> 5. **README summary:** Read the root README and summarize it in 3-5 sentences.
> 6. **Dependencies:** For each module, grep for import/require statements to other modules. Report which modules depend on which.
>
> Return a structured report with all findings. Be concise but complete.

### Agent 2: Git Historian

Launch with the Agent tool (subagent_type: "general-purpose"):

**Prompt for Agent 2:**
> You are analyzing the git history of a repository to build a narrative timeline for wiki documentation.
>
> Read the file `references/git-analysis.md` for exact commands and parsing guidance.
>
> Perform these tasks:
> 1. Run the core git commands listed in the reference to extract: total commits, project dates, tags, contributor stats, merge commits, and branch info.
> 2. Extract PR numbers from merge commit messages (patterns: `Merge pull request #N`, `(#N)`).
> 3. {{If gh is available}}: Fetch full details for the top 20 PRs using `gh pr view <NUMBER> --json number,title,body,labels,mergedAt,author`.
> 4. Group commits into epochs following the epoch grouping logic in the reference (tag-based if tags exist, time-based otherwise).
> 5. Identify the 5-10 most significant events (tagged releases, major merges, first commit).
>
> Return a structured report following the output format in the reference file.

Replace `{{If gh is available}}` with the actual status from Phase 0 preflight.

### Self: Read Project Context

While agents run, read:
- Root `README.md` (or `README.rst`, `README.txt`)
- `CLAUDE.md` if it exists
- `LICENSE` or `LICENSE.md` (to note license type)
- `CONTRIBUTING.md` if it exists (for development workflow info)

---

## Phase 2: Synthesis

Once both agents return, build a unified **project model**:

1. **Merge data:** Combine codebase scanner's structural data with git historian's timeline data.
2. **Module selection:** Modules with fewer than 3 source files get mentioned in architecture.md but do NOT get their own page in `modules/`. If total modules > 15, keep only the top 15 by file count.
3. **Dependency graph:** From the scanner's import analysis, build the module dependency list for the Mermaid diagram.
4. **Timeline:** From the historian's epochs, select the narrative thread for journey.md.
5. **Glossary candidates:** Note any domain-specific terms that appeared frequently in the scanner's output (class names, service names, custom types, abbreviations in comments).
6. **Tiny repo check:** If the repo has fewer than 5 source files total, skip `modules/` directory entirely and fold module info into architecture.md.

---

## Phase 3: Write Wiki Pages

Write pages **sequentially** in this order. Use the templates from `references/page-templates.md` as the structural guide for each page.

### 3a: Write `index.md`

Use the `index.md` template. Fill in:
- Project name (from repo name)
- Summary paragraph (synthesized from README + codebase analysis)
- Quick stats table (languages, LOC, commits, contributors, age)
- Table of contents linking all other pages
- Getting started section (from README if available)

### 3b: Write `architecture.md`

Use the `architecture.md` template. Include:
- System overview paragraphs describing major layers/components
- **Mermaid flowchart TD** showing component relationships (use subgraphs for layers)
- **Mermaid graph LR** showing module dependency graph
- Key design decisions (inferred from code structure, framework choices, patterns)
- Data flow section with **Mermaid sequence diagram** if a clear request-response flow exists
- External dependencies table

### 3c: Write `modules/<name>.md` for each qualifying module

Use the `modules/module-name.md` template. For each module:
- Summary paragraph explaining its role
- Key files table (top 5-8 most important files)
- Public API listing (exported functions, classes, types)
- Internal flow diagram (**Mermaid flowchart** showing how the module processes work)
- Dependency links to other module pages

**Optimization:** If there are more than 5 modules, launch sub-agents (one per module, up to 3 in parallel) to write module pages. Each agent should read the page template from `references/page-templates.md` (at the repo root) and the specific module's entry point + key files.

### 3d: Write `journey.md`

Use the `journey.md` template. This is the **narrative heart** of the wiki:
- Opening paragraph with project creation date, total commits, contributor count
- **For each epoch:** Write a 2-4 sentence narrative paragraph in past tense, third person, describing what was built and why it mattered. Follow with a "Key events" bullet list.
- **Mermaid gitGraph** showing major branches and merges (limit to ~20 nodes for readability)
- Release history table (from tags)
- Notable PRs section with title, author, date, and summary for each enriched PR

**Writing style for journey.md:**
- Write like a Wikipedia "History" section — factual, neutral, informative
- Use past tense: "The project was initialized with...", "Version 2.0 introduced..."
- Connect epochs with narrative flow: "Following the v1.0 release, development shifted to..."
- Highlight turning points: major refactors, technology changes, team growth

### 3e: Write `contributors.md`

Use the `contributors.md` template:
- Top contributors table with commit counts and active date ranges
- Contribution patterns observations (solo vs team, activity frequency, primary areas)

### 3f: Write `glossary.md`

Use the `glossary.md` template:
- Extract domain terms from: module names, class/type names, README terminology, comment blocks
- Provide definitions (inferred from usage context, comments, or naming)
- Note where each term is primarily used

---

## Phase 4: Finalization

1. **Verify cross-links:** Grep the output directory for all relative links (`](`) and verify each target file exists.
2. **Register the wiki in `CLAUDE.md`:** So future agent sessions discover and use the generated wiki, ensure the project's root `CLAUDE.md` points at it.
   - Determine the wiki location to reference (the `<output>` path, default `wiki/`).
   - If `CLAUDE.md` does **not** exist at the repo root, create it with this content:
     ```markdown
     # CLAUDE.md

     ## Repository context

     Use the local `wiki/` directory for getting context about this repo. It is a
     generated, multi-page wiki (architecture, modules, git journey, contributors,
     glossary). Start at `wiki/index.md`. Read the relevant page before working:
     `wiki/architecture.md` for system design, `wiki/modules/<name>.md` for a
     specific module, and `wiki/journey.md` for the history behind a decision.

     Regenerate with `/wiki --force` after significant changes so the context stays current.
     ```
     (Replace `wiki/` with the actual `<output>` path if `--output` was used.)
   - If `CLAUDE.md` **does** exist, do not overwrite it. Read it first. If it already references the wiki, leave it unchanged. Otherwise append a `## Repository context` section (using the text above) to the end of the file, preserving all existing content.
3. **Report to user:** Summarize what was generated:
   - List all pages created with their paths
   - Total word count across all pages
   - Any pages that were skipped and why (e.g., "Skipped modules/ — repo has fewer than 5 source files")
   - Note the output location

---

## Edge Case Handling

| Scenario | What to do |
|----------|-----------|
| **No git history** (0 commits) | Skip journey.md and contributors.md. Note in index.md: "This project has no commit history yet." |
| **No tags** | Use time-based epochs in journey.md. Omit the Release History table. |
| **No PRs or merge commits** | Write journey.md using regular commit messages. Skip the Notable PRs section. |
| **Monorepo with workspaces** | Each workspace is a module. Show workspace relationships in architecture.md. |
| **Tiny repo (< 5 files)** | Skip modules/ directory. Combine all info into index.md + architecture.md. |
| **Large repo (> 1000 files)** | Cap at 15 modules. Sample LOC from 20 files. Limit commit scan to 500. |
| **No README** | Write index.md summary from code analysis alone. Note: "No README found." |
| **Binary/non-code repo** | Generate index.md + journey.md only. Skip architecture and modules. |

---

## Writing Style Guide

All wiki pages must follow these conventions:

- **First sentence:** Bold the project/module name. State what it is and does in one sentence.
- **Tone:** Neutral, encyclopedic, third-person. Never use "you", "we", "I".
- **Tense:** Present tense for descriptions ("The API module handles..."). Past tense for history ("Version 1.0 was released on...").
- **Cross-links:** Always use relative links between wiki pages. Format: `[Page Title](relative-path.md)`.
- **Mermaid diagrams:** Always wrap in triple-backtick blocks with `mermaid` language tag. Keep under 15 nodes per diagram. Use descriptive node labels.
- **Tables:** Use GitHub-flavored markdown tables. Align columns for readability.
- **Headings:** Use `#` for page title, `##` for major sections, `###` for subsections. Never skip levels.
- **No fluff:** Every sentence should convey information. Avoid filler phrases like "It is worth noting that" or "As mentioned above".
