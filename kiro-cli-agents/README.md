# Kiro CLI Integration

Run the Code Journey wiki generator from the [Kiro CLI](https://kiro.dev/docs/cli/) (`kiro-cli`). This delivery form reuses the existing skill in [`claude-cli-skills/SKILL.md`](../claude-cli-skills/SKILL.md) and the shared [`references/`](../references/) — nothing is re-authored.

Kiro CLI exposes the generator through three of its extension points:

| Kiro concept | What it does here | Source / location |
|--------------|-------------------|-------------------|
| **Skill** | The four-phase procedure; Kiro surfaces it as the `/wiki` slash command | `.kiro/skills/wiki/SKILL.md` (copy of `claude-cli-skills/SKILL.md`) |
| **Custom agent** | Names the agent, scopes its tools, and loads the skill + references as context | `.kiro/agents/wiki.json` (template: [`wiki-agent.json`](wiki-agent.json)) |
| **Hook** | An `agentSpawn` hook that tells each session whether a `wiki/` already exists | `hooks` block inside the agent JSON |

## Install

From the repository you want to document, copy the skill, references, and agent into its `.kiro/` directory:

```bash
# 1. The skill (becomes the /wiki slash command)
mkdir -p .kiro/skills/wiki
cp /path/to/code-journey/claude-cli-skills/SKILL.md .kiro/skills/wiki/SKILL.md

# 2. Shared references the skill reads at runtime
cp -r /path/to/code-journey/references references

# 3. The custom agent (with its agentSpawn hook)
mkdir -p .kiro/agents
cp /path/to/code-journey/kiro-cli-agents/wiki-agent.json .kiro/agents/wiki.json
```

## Use

**Interactive** — start a session with the agent, then run the slash command:

```bash
kiro-cli --agent wiki
# then, in the session:
/wiki
```

**Headless / CI** — pass the prompt as an argument and pre-trust the tools the skill needs:

```bash
export KIRO_API_KEY=...   # required for non-interactive runs
kiro-cli chat --no-interactive --agent wiki \
  --trust-tools=read,shell,write \
  "Generate the wiki for this repository"
```

## How the agent is configured

See [`wiki-agent.json`](wiki-agent.json). Highlights:

- **`resources`** load the README, the `references/` heuristics and templates, and the skill via `skill://` (which is what makes `/wiki` available).
- **`tools` / `allowedTools` / `toolsSettings`** grant `read`, `shell` (scoped to `git`, `gh`, `wc`, `mkdir`, …), and `write` (scoped to `wiki/**` and `CLAUDE.md`). Kiro controls tool permissions in the agent JSON, not in the skill frontmatter — the skill body ports over unchanged.
- **`hooks.agentSpawn`** runs a shell check on session start and feeds the result into context, so the agent knows up front whether to read an existing `wiki/` or offer to generate one.

### Hooks reference

Kiro hooks fire at five lifecycle events — `agentSpawn`, `userPromptSubmit`, `preToolUse`, `postToolUse`, and `stop` — and run a shell `command` whose STDOUT is added to the conversation. Each entry accepts `matcher` (which tool to match; omitted for `stop` and `agentSpawn`), `command`, `timeout_ms` (default 30000), and `cache_ttl_seconds` (`0` disables caching; `agentSpawn` hooks are never cached). See the [Kiro hooks docs](https://kiro.dev/docs/cli/hooks/) and the [Agent Configuration Reference](https://kiro.dev/docs/cli/custom-agents/configuration-reference#hooks-field).

**Optional: keep the wiki fresh automatically.** Add a `stop` hook that nudges the agent to regenerate the wiki after it edits source files, realizing the "generated, not maintained" property from [`doc/hypothesis.md`](../doc/hypothesis.md):

```json
"stop": [
  {
    "command": "git diff --quiet HEAD -- ':!wiki' || echo '{\"decision\":\"block\",\"reason\":\"Source files changed. Run /wiki --force to regenerate the wiki so its context stays current.\"}'",
    "timeout_ms": 10000
  }
]
```

When a `stop` hook returns `{"decision":"block","reason":"..."}`, Kiro sends the reason back as a new user message, continuing the session — an automatic feedback loop that keeps the wiki in sync.

---

*See also: [`kiro-ide-specs/`](../kiro-ide-specs/) for the Kiro IDE spec form, and the root [README](../README.md) for the Claude Code skill form.*
