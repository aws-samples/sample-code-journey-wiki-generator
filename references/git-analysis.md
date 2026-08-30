# Git Analysis Commands & Parsing

Reference for extracting repository history, milestones, PRs, and contributor data.

---

## Core Commands

### Full Commit History
```bash
git log --format='%H|%h|%d|%s|%ai|%an|%ae|%P' --all | head -500
```
Fields: full hash | short hash | decorations | subject | author date (ISO) | author name | author email | parent hashes

- Commits with 2+ parent hashes in `%P` are **merge commits**
- Decorations (`%d`) contain branch/tag names

### PR Merge Commits
```bash
git log --merges --format='%h|%s|%ai|%an' --grep="Merge pull request" | head -100
```
Also try: `--grep="(#[0-9]"` for squash-merge PRs that include `(#123)` in the subject.

**Extracting PR numbers from commit messages:**
- Pattern 1: `Merge pull request #(\d+) from (.+)` → PR number + source branch
- Pattern 2: `(.+) \(#(\d+)\)` → squash-merge title + PR number
- Pattern 3: `Merge branch '(.+)' into (.+)` → branch merge (no PR)

### Version Tags
```bash
git tag -l --sort=-version:refname | head -50
```
For tag dates:
```bash
git tag -l --sort=-version:refname --format='%(refname:short)|%(creatordate:iso)' | head -50
```

### Contributor Statistics
```bash
git shortlog -sn --all | head -30
```
Format: `<count>\t<name>`

For contributor date ranges:
```bash
# First and last commit per author
git log --format='%an|%ai' --all | sort | head -1   # earliest by author
git log --format='%an|%ai' --all | sort -r | head -1 # latest by author
```

### Project Age
```bash
git log --format='%ai' --reverse | head -1    # First commit date
git log --format='%ai' -1                      # Most recent commit date
git rev-list --count HEAD                      # Total commit count
```

### Active Branches
```bash
git branch -a --sort=-committerdate --format='%(refname:short)|%(committerdate:iso)|%(subject)' | head -20
```

---

## PR Enrichment (requires `gh` CLI)

Check availability first:
```bash
which gh && gh auth status 2>&1 | head -3
```

For each extracted PR number:
```bash
gh pr view <NUMBER> --json number,title,body,labels,mergedAt,author,additions,deletions
```

**Enrichment strategy:**
1. Extract all PR numbers from merge commit messages
2. Sort by date (most recent first)
3. Fetch full details for the **top 20** PRs via `gh pr view`
4. For remaining PRs, use only the merge commit message

**PR data to extract:**
- `title` — PR title (use as section heading)
- `body` — First 500 chars of PR description (use as summary)
- `labels` — Categorization tags
- `mergedAt` — Merge timestamp
- `additions` / `deletions` — Change magnitude

---

## Epoch Grouping Logic

### Strategy A: Tag-Based Epochs (preferred)

If the repo has version tags:

1. Sort tags chronologically
2. Each tag range (v0.1 → v0.2) becomes an epoch
3. Name: the tag name (e.g., "v1.0.0 — Initial Release")
4. Commits before the first tag = "Pre-release" epoch
5. Commits after the last tag = "Development (unreleased)" epoch

```bash
# Get commits between two tags
git log --format='%h|%s|%ai|%an' v0.1.0..v0.2.0
```

### Strategy B: Time-Based Epochs (fallback)

If no tags exist:

1. Determine project timespan (first commit → last commit)
2. If < 6 months: group by month
3. If 6 months – 2 years: group by quarter (Q1 2024, Q2 2024)
4. If > 2 years: group by half-year or year
5. Name each epoch by its most impactful commit/merge

### Epoch Naming

For each epoch, pick the best name from:
1. The tag name (if tag-based)
2. The most common prefix in commit messages (e.g., "feat:", "refactor:")
3. The PR title of the biggest merge in the period
4. A descriptive summary like "Authentication & Authorization"

---

## Identifying Significant Commits

Rank commits by significance using these signals:
1. **Tagged commits** — highest significance (releases)
2. **Merge commits with PR references** — high (feature completions)
3. **Commits touching many files** — medium (refactors, migrations)
4. **Commits with keywords** — medium ("breaking", "major", "initial", "migrate", "rewrite")
5. **First commit** — always significant (project inception)
6. **Regular commits** — low (use for epoch texture, not headliners)

Select the **top 5-10** most significant events for the journey narrative.

---

## Output Structure

Return a structured summary with:

```
Project:
  name: <repo name>
  created: <first commit date>
  latest: <last commit date>
  total_commits: <count>
  total_contributors: <count>

Epochs:
  - name: <epoch name>
    start: <date>
    end: <date>
    commit_count: <N>
    highlights:
      - <significant commit/PR summary>
      - <significant commit/PR summary>

Tags:
  - name: v1.0.0
    date: <date>
    message: <tag message or associated commit message>

Contributors:
  - name: <author>
    commits: <count>
    first_active: <date>
    last_active: <date>

Notable PRs:
  - number: 42
    title: <PR title>
    summary: <first 200 chars of body>
    merged: <date>
    author: <name>
    labels: [bug, feature]

Branches:
  - name: <branch>
    last_activity: <date>
```
