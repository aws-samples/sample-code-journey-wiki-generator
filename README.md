# Code Journey

Generate a comprehensive, multi-page, Wikipedia-style wiki for any git repository. Produces architecture diagrams, per-module documentation, a narrative git history timeline, contributor profiles, and a domain glossary.

## Repository Structure

```
code-journey/
├── references/              # Shared reference docs (templates, heuristics, git commands)
│   ├── git-analysis.md      # Git history extraction commands and parsing guidance
│   ├── module-detection.md  # Heuristics for identifying modules in any repo
│   └── page-templates.md    # Markdown + Mermaid templates for each wiki page
├── claude-cli-skills/       # Claude Code skill implementation
│   └── SKILL.md             # Skill definition for /wiki slash command
├── kiro-ide-specs/          # Kiro IDE spec implementation
│   ├── requirements.md      # User stories and acceptance criteria
│   ├── design.md            # Technical design document
│   └── tasks.md             # Implementation task breakdown
└── kiro-cli-agents/         # Kiro CLI agent implementation
    ├── wiki-agent.json      # Custom agent (loads SKILL.md, agentSpawn hook)
    └── README.md            # Install and invocation guide
```

## Usage

### Claude Code (Skill)

1. Copy the skill into your Claude Code skills directory:

   ```bash
   mkdir -p ~/.claude/skills/wiki
   cp claude-cli-skills/SKILL.md ~/.claude/skills/wiki/SKILL.md
   cp -r references ~/.claude/skills/wiki/references
   ```

2. In any git repository, invoke the skill:

   ```
   /wiki
   ```

   Optional flags:
   - `--output <path>` — write wiki to a custom directory (default: `wiki/`)
   - `--force` — overwrite an existing wiki directory

3. The generated wiki appears in the `wiki/` directory with pages for architecture, modules, git journey, contributors, and glossary.

### Kiro (Spec)

1. Copy the spec into your Kiro specs directory:

   ```bash
   cp -r kiro-ide-specs /path/to/your-repo/.kiro/specs/wiki
   cp -r references /path/to/your-repo/references
   ```

2. Kiro uses the `requirements.md`, `design.md`, and `tasks.md` to guide implementation. The tasks file contains a checklist that tracks progress through all four phases: preflight, data gathering, synthesis, and page writing.

### Kiro CLI (Agent + Skill)

The [Kiro CLI](https://kiro.dev/docs/cli/) (`kiro-cli`) runs the generator through a custom agent that loads the same `SKILL.md` as a `/wiki` slash command.

1. Copy the skill, references, and agent into the target repository's `.kiro/` directory:

   ```bash
   mkdir -p .kiro/skills/wiki .kiro/agents
   cp claude-cli-skills/SKILL.md .kiro/skills/wiki/SKILL.md
   cp -r references references
   cp kiro-cli-agents/wiki-agent.json .kiro/agents/wiki.json
   ```

2. Generate the wiki — interactively or headless:

   ```bash
   kiro-cli --agent wiki          # then run /wiki in the session
   # or, in CI:
   KIRO_API_KEY=... kiro-cli chat --no-interactive --agent wiki \
     --trust-tools=read,shell,write "Generate the wiki for this repository"
   ```

   The agent's `agentSpawn` hook reports whether a `wiki/` already exists; see [`kiro-cli-agents/`](kiro-cli-agents/) for the agent definition and an optional `stop` hook that auto-regenerates the wiki after source changes.

## What Gets Generated

| Page | Description |
|------|-------------|
| `index.md` | Project summary, quick stats, table of contents |
| `architecture.md` | System design with Mermaid component and dependency diagrams |
| `modules/<name>.md` | One page per major module (API, key files, flow diagrams) |
| `journey.md` | Narrative timeline with gitGraph, epoch summaries, PR highlights |
| `contributors.md` | Who built what, contribution patterns |
| `glossary.md` | Domain terms extracted from the codebase |

## Requirements

- A git repository (the tool analyzes git history)
- [GitHub CLI (`gh`)](https://cli.github.com/) — optional, enables PR enrichment with full titles, descriptions, and labels

## License

[MIT](LICENSE)
