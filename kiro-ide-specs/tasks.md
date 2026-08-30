# Wiki Generator — Tasks

## Phase 0: Preflight

- [ ] 1. Verify the current directory is a git repository (`git rev-parse --is-inside-work-tree`)
- [ ] 2. Determine the repository name from `git remote get-url origin` (fallback: directory basename)
- [ ] 3. Resolve output path: use `--output <path>` argument if provided, otherwise default to `wiki/`
- [ ] 4. If output directory exists and `--force` not passed, warn the user and stop
- [ ] 5. Check if `gh` CLI is installed and authenticated (`which gh && gh auth status`)
- [ ] 6. Create output directory structure: `mkdir -p <output>/modules`

## Phase 1: Data Gathering

These three tasks are independent and should run in parallel where possible.

### Codebase Scan
- [ ] 7. Glob for source files by extension and count files per extension; report top 5 languages
  - Source extensions: `.ts`, `.tsx`, `.js`, `.jsx`, `.py`, `.rs`, `.go`, `.java`, `.kt`, `.swift`, `.c`, `.cpp`, `.h`, `.hpp`, `.rb`, `.php`, `.cs`, `.scala`, `.clj`, `.ex`, `.exs`, `.ml`, `.hs`, `.lua`, `.r`, `.R`, `.jl`, `.dart`, `.vue`, `.svelte`
  - Exclude directories: `.git`, `node_modules`, `vendor`, `dist`, `build`, `target`, `out`, `bin`, `obj`, `.venv`, `venv`, `__pycache__`, `.next`, `.cache`, `coverage`
- [ ] 8. Sample up to 20 representative source files with `wc -l` and extrapolate total LOC
- [ ] 9. Detect modules using heuristics from `references/module-detection.md` (try strategies 1–4 in order, stop at first that yields results)
- [ ] 10. For each detected module, record: name, path, file count, entry point, one-sentence description
- [ ] 11. Note presence of: Dockerfile, docker-compose.yml, `.github/workflows/`, `.gitlab-ci.yml`, Jenkinsfile, `terraform/`, `k8s/`
- [ ] 12. Read the root README and summarize in 3–5 sentences
- [ ] 13. Grep for import/require statements between modules to map inter-module dependencies

### Git History Analysis
- [ ] 14. Extract project metadata: total commits (`git rev-list --count HEAD`), first and last commit dates
- [ ] 15. Extract version tags with dates (`git tag -l --sort=-version:refname --format='%(refname:short)|%(creatordate:iso)'`)
- [ ] 16. Extract contributor stats (`git shortlog -sn --all`) with first/last active dates per author
- [ ] 17. Extract merge commits and PR numbers from commit messages (patterns: `Merge pull request #N`, `(#N)`)
- [ ] 18. If `gh` is available: fetch details for top 20 PRs (`gh pr view <N> --json number,title,body,labels,mergedAt,author,additions,deletions`)
- [ ] 19. Group commits into epochs (tag-based if tags exist, time-based otherwise — see `references/git-analysis.md`)
- [ ] 20. Identify 5–10 most significant events (tagged releases, major merges, first commit)

### Project Context
- [ ] 21. Read root README.md (or .rst/.txt variant)
- [ ] 22. Read LICENSE/LICENSE.md and extract license type
- [ ] 23. Read CONTRIBUTING.md if it exists for development workflow info

## Phase 2: Synthesis

- [ ] 24. Merge codebase scan and git history data into a unified project model
- [ ] 25. Filter modules: exclude those with < 3 source files from getting their own page; cap at 15 modules
- [ ] 26. Build module dependency adjacency list from import analysis
- [ ] 27. Select epoch narrative thread for journey.md
- [ ] 28. Collect glossary candidates: domain terms from module names, class/type names, README, comments
- [ ] 29. Apply tiny repo rule: if < 5 source files total, plan to skip `modules/` directory

## Phase 3: Write Wiki Pages

Write pages sequentially using templates from `references/page-templates.md`.

- [ ] 30. Write `<output>/index.md`
  - Fill in: project name, summary paragraph, quick stats table (languages, LOC, commits, contributors, age, license), table of contents, getting started section
- [ ] 31. Write `<output>/architecture.md`
  - Include: system overview, Mermaid component diagram (flowchart TD with subgraphs), Mermaid dependency graph (graph LR), key design decisions, data flow sequence diagram (if applicable), external dependencies table
- [ ] 32. Write `<output>/modules/<name>.md` for each qualifying module
  - Include: summary, key files table (top 5–8 files), public API listing, internal flow diagram (Mermaid flowchart), dependency cross-links
  - Read each module's entry point and key files before writing its page
- [ ] 33. Write `<output>/journey.md`
  - Include: opening paragraph with stats, epoch narratives (past tense, third person, encyclopedic), key events per epoch, Mermaid gitGraph (max ~20 nodes), release history table, notable PRs section
- [ ] 34. Write `<output>/contributors.md`
  - Include: ranked contributors table (name, commits, first/last active, primary area), contribution pattern observations
- [ ] 35. Write `<output>/glossary.md`
  - Include: term/definition/location table with terms extracted from codebase

## Phase 4: Finalization

- [ ] 36. Verify all relative cross-links between wiki pages resolve to existing files
- [ ] 37. Register the wiki in the project's root `CLAUDE.md` (using the resolved `<output>` path):
  - If `CLAUDE.md` does not exist, create it with a `## Repository context` section telling readers to use the local `wiki/` for repo context (start at `wiki/index.md`; `architecture.md` for design, `modules/<name>.md` for a module, `journey.md` for history; regenerate after significant changes)
  - If `CLAUDE.md` exists, read it first; if it already references the wiki leave it unchanged, otherwise append the `## Repository context` section without overwriting existing content
- [ ] 38. Report to user: list all pages created, total word count, any skipped pages with reasons, output location
