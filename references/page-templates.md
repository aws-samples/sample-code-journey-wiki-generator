# Wiki Page Templates

Templates and Mermaid patterns for each wiki page. Follow these structures when writing pages. Replace placeholders `{{...}}` with actual content.

All pages use **Wikipedia conventions**: bold first-sentence summary, neutral third-person tone, clear section headings, relative cross-links between pages.

---

## index.md

```markdown
# {{Project Name}}

**{{Project Name}}** is a {{language/framework}} {{type: application/library/service/tool}} that {{one-sentence purpose}}.
{{2-3 sentence expanded summary covering what it does, who it's for, and its key differentiator.}}

## Quick Stats

| Metric | Value |
|--------|-------|
| Primary Language(s) | {{languages}} |
| Lines of Code | {{LOC estimate}} |
| Total Commits | {{count}} |
| Contributors | {{count}} |
| Project Age | {{first commit date}} — present |
| Latest Activity | {{last commit date}} |
| License | {{license or "Not specified"}} |

## Contents

- [Architecture](architecture.md) — System design, components, and data flow
- [Modules](modules/) — Detailed documentation for each major module
{{for each module:}}
  - [{{Module Name}}](modules/{{module-slug}}.md) — {{one-line description}}
{{end}}
- [Journey](journey.md) — The project's evolution from first commit to today
- [Contributors](contributors.md) — Who built what
- [Glossary](glossary.md) — Domain terms and definitions

## Getting Started

{{If a README with setup instructions exists, summarize the key steps here. Otherwise:}}
See the project's README for setup and installation instructions.
```

---

## architecture.md

```markdown
# Architecture

**{{Project Name}}** follows a {{architectural style: monolithic/microservice/modular/layered}} architecture {{brief qualifier}}.

## System Overview

{{2-3 paragraphs describing the high-level architecture: what the major layers/components are, how they interact, what external dependencies exist.}}

### Component Diagram

\`\`\`mermaid
flowchart TD
    subgraph {{Layer1 Name}}["{{Layer1 Label}}"]
        {{ID1}}[{{Component1}}]
        {{ID2}}[{{Component2}}]
    end
    subgraph {{Layer2 Name}}["{{Layer2 Label}}"]
        {{ID3}}[{{Component3}}]
        {{ID4}}[({{Database}})]
    end
    {{ID1}} --> {{ID3}}
    {{ID2}} --> {{ID3}}
    {{ID3}} --> {{ID4}}
\`\`\`

## Module Dependencies

\`\`\`mermaid
graph LR
    {{moduleA}} --> {{moduleB}}
    {{moduleA}} --> {{moduleC}}
    {{moduleB}} --> {{shared}}
    {{moduleC}} --> {{shared}}
\`\`\`

## Key Design Decisions

{{Bulleted list of notable architectural choices found in the code:}}
- **{{Decision}}**: {{Rationale or observation}}
- **{{Decision}}**: {{Rationale or observation}}

## Data Flow

{{Describe how data moves through the system. If a clear request-response or pipeline flow exists:}}

\`\`\`mermaid
sequenceDiagram
    participant {{Actor1}}
    participant {{Component1}}
    participant {{Component2}}
    participant {{Store}}
    {{Actor1}}->>{{Component1}}: {{action}}
    {{Component1}}->>{{Component2}}: {{action}}
    {{Component2}}->>{{Store}}: {{action}}
    {{Store}}-->>{{Component2}}: {{response}}
    {{Component2}}-->>{{Actor1}}: {{response}}
\`\`\`

## External Dependencies

| Dependency | Purpose | Version |
|-----------|---------|---------|
| {{name}} | {{what it's used for}} | {{version if known}} |

---

*See also: [Modules](modules/) for detailed component documentation, [Journey](journey.md) for how the architecture evolved.*
```

---

## modules/{{module-name}}.md

```markdown
# {{Module Name}}

**{{Module Name}}** is the {{role: core/utility/API/UI/data}} module responsible for {{one-sentence purpose}}.

## Overview

{{2-3 paragraphs: what this module does, why it exists, how it relates to the rest of the system.}}

## Key Files

| File | Purpose |
|------|---------|
| `{{path/to/file}}` | {{what it does}} |
| `{{path/to/file}}` | {{what it does}} |
| `{{path/to/file}}` | {{what it does}} |

## Public API

{{List exported functions, classes, types, or endpoints:}}

### `{{functionName}}({{params}})`
{{One-line description of what it does.}}

### `{{ClassName}}`
{{One-line description.}}

## Internal Flow

\`\`\`mermaid
flowchart TD
    A[{{Entry point}}] --> B{{{Decision?}}}
    B -->|Yes| C[{{Action 1}}]
    B -->|No| D[{{Action 2}}]
    C --> E[{{Result}}]
    D --> E
\`\`\`

## Dependencies

- **Uses**: [{{Other Module}}]({{other-module}}.md), {{external lib}}
- **Used by**: [{{Parent Module}}]({{parent-module}}.md)

---

*Back to [Architecture](../architecture.md) | [All Modules](./)*
```

---

## journey.md

```markdown
# Journey

**{{Project Name}}** was created on **{{first commit date}}** and has grown through **{{total commits}} commits** by **{{contributor count}} contributors** over **{{project age}}**.

## Timeline

{{For each epoch, write a narrative paragraph:}}

### {{Epoch Name}} ({{date range}})

{{Narrative paragraph describing what happened in this period: what was built, what problems were solved, key milestones. Write like a Wikipedia history section — factual, third-person, past tense.}}

**Key events:**
- {{date}} — {{event description}} {{#PR if applicable}}
- {{date}} — {{event description}}

{{Repeat for each epoch}}

## Git Flow

\`\`\`mermaid
gitGraph
    commit id: "{{first commit msg}}"
    {{for significant branches/merges:}}
    branch {{branch-name}}
    commit id: "{{key commit}}"
    checkout main
    merge {{branch-name}} id: "{{merge description}}" {{tag: "vX.Y.Z" if tagged}}
    {{end}}
\`\`\`

## Release History

| Version | Date | Highlights |
|---------|------|-----------|
| {{tag}} | {{date}} | {{key changes}} |

## Notable Pull Requests

{{For each enriched PR:}}

### #{{number}} — {{title}}
**Merged:** {{date}} | **Author:** {{author}} | **Labels:** {{labels}}

{{First 200 chars of PR body, or commit message summary.}}

---

*See also: [Contributors](contributors.md) for who drove these changes, [Architecture](architecture.md) for the current state.*
```

---

## contributors.md

```markdown
# Contributors

**{{Project Name}}** has been built by **{{count}} contributors** since {{first commit date}}.

## Top Contributors

| # | Contributor | Commits | First Active | Last Active | Primary Area |
|---|------------|---------|-------------|------------|-------------|
| 1 | {{name}} | {{count}} | {{date}} | {{date}} | {{area/module if detectable}} |
| 2 | {{name}} | {{count}} | {{date}} | {{date}} | {{area}} |

## Contribution Patterns

{{Observations about how the team works:}}
- {{e.g., "The project was primarily developed by X, with contributions from Y and Z starting in Q3 2024."}}
- {{e.g., "Most merge activity occurs on weekdays, suggesting a professional development team."}}

---

*See also: [Journey](journey.md) for the timeline of their work.*
```

---

## glossary.md

```markdown
# Glossary

Domain-specific terms, abbreviations, and concepts used in the **{{Project Name}}** codebase.

| Term | Definition | Where Used |
|------|-----------|-----------|
| **{{term}}** | {{definition extracted from comments, README, or inferred from usage}} | {{file or module where it appears}} |
| **{{term}}** | {{definition}} | {{location}} |

---

*Back to [Index](index.md)*
```

---

## Mermaid Quick Reference

### Architecture (flowchart TD — top-down)
```
flowchart TD
    subgraph GroupName["Display Label"]
        A[Rectangle]
        B([Rounded])
        C[(Database)]
        D{Decision}
    end
    A --> B
    B --> C
    B -.->|optional| D
```

### Dependencies (graph LR — left-to-right)
```
graph LR
    A --> B
    A --> C
    B --> D
    C --> D
```

### Git Graph
```
gitGraph
    commit id: "label"
    branch name
    checkout name
    commit id: "label"
    checkout main
    merge name id: "label" tag: "v1.0"
```

### Sequence Diagram
```
sequenceDiagram
    participant A as Label
    participant B as Label
    A->>B: Solid arrow with label
    B-->>A: Dashed return arrow
    A->>+B: Activate B
    B-->>-A: Deactivate B
```

### Formatting Notes
- Keep node labels short (< 30 chars)
- Use subgraphs to group related components
- Max ~15 nodes per diagram for readability
- If a diagram would exceed 15 nodes, split into multiple diagrams
- Use `---` for solid lines, `-.-` for dashed lines
- Escape special characters in labels with quotes: `A["Label with (parens)"]`
