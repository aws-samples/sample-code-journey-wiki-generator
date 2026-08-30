# Module Detection Heuristics

Use these heuristics in priority order to identify major modules in any repository. Stop at the first strategy that yields results.

---

## Strategy 1: Package Manager Workspaces

Check for workspace definitions — these are the most reliable module boundaries.

| File | Field / Pattern | Module = |
|------|----------------|----------|
| `package.json` | `"workspaces": [...]` | Each workspace path |
| `pnpm-workspace.yaml` | `packages:` list | Each listed glob path |
| `lerna.json` | `"packages": [...]` | Each package path |
| `nx.json` + `workspace.json` | `"projects": {...}` | Each project entry |
| `Cargo.toml` | `[workspace] members = [...]` | Each member crate path |
| `go.work` | `use (...)` directives | Each listed module path |
| `settings.gradle` / `settings.gradle.kts` | `include(...)` statements | Each included project |

**How to check:**
```
Read package.json → look for "workspaces" key
Glob for pnpm-workspace.yaml, lerna.json, nx.json
Read Cargo.toml → look for [workspace] section
Glob for go.work
Glob for settings.gradle*
```

---

## Strategy 2: Subdirectory Manifests

Look for subdirectories that contain their own package manifest — each is an independent module.

| Manifest File | Language/Ecosystem |
|--------------|-------------------|
| `package.json` | Node.js / JavaScript / TypeScript |
| `Cargo.toml` | Rust |
| `go.mod` | Go |
| `pyproject.toml` | Python (modern) |
| `setup.py` / `setup.cfg` | Python (legacy) |
| `CMakeLists.txt` | C / C++ |
| `pom.xml` | Java (Maven) |
| `build.gradle` / `build.gradle.kts` | Java / Kotlin (Gradle) |
| `*.csproj` | C# / .NET |
| `mix.exs` | Elixir |
| `Gemfile` | Ruby |
| `Package.swift` | Swift |

**How to check:**
```
Glob for */package.json, */Cargo.toml, */go.mod, */pyproject.toml, etc.
Exclude: node_modules/, vendor/, .git/, dist/, build/, target/
Each directory containing a manifest = one module
```

---

## Strategy 3: Language-Specific Conventions

If no workspace or subdirectory manifests found, use language conventions.

### Python
- Top-level directories containing `__init__.py` are packages
- `Glob: */__init__.py` → parent directory = module

### Rust
- `src/` subdirectories with `mod.rs` or named after modules
- Crate root identified by `src/lib.rs` or `src/main.rs`

### Go
- Each directory with `.go` files is a package
- Group by top-level directories under `cmd/`, `pkg/`, `internal/`

### Java / Kotlin
- Directories under `src/main/java/` matching package structure
- Top-level Maven/Gradle modules

### JavaScript / TypeScript
- `src/` subdirectories that act as feature boundaries
- Directories with an `index.ts` / `index.js` entry point

---

## Strategy 4: Structural Fallback

When no language-specific signals are found, use directory structure.

**Identify top-level directories that contain source code:**

Source file extensions to look for:
`.ts`, `.tsx`, `.js`, `.jsx`, `.py`, `.rs`, `.go`, `.java`, `.kt`, `.swift`,
`.c`, `.cpp`, `.h`, `.hpp`, `.rb`, `.php`, `.cs`, `.scala`, `.clj`, `.ex`,
`.exs`, `.ml`, `.hs`, `.lua`, `.r`, `.R`, `.jl`, `.dart`, `.vue`, `.svelte`

**Directories to ALWAYS exclude:**
`.git`, `node_modules`, `vendor`, `dist`, `build`, `target`, `out`, `bin`,
`obj`, `.venv`, `venv`, `env`, `__pycache__`, `.tox`, `.mypy_cache`,
`.next`, `.nuxt`, `.cache`, `.idea`, `.vscode`, `coverage`, `.github`,
`.gitlab`, `.circleci`, `docs`, `wiki`, `assets`, `static`, `public` (unless it contains source)

**Module threshold:** A directory qualifies as a module if it contains **3 or more source files** (recursive).

**Cap:** If more than 15 modules detected, keep only the top 15 by file count.

---

## Output Format

For each detected module, gather:

| Field | How |
|-------|-----|
| **name** | Directory name |
| **path** | Relative path from repo root |
| **file_count** | Count of source files (recursive) |
| **entry_point** | `index.*`, `main.*`, `lib.*`, `mod.*`, `__init__.py`, or largest file |
| **description** | Read first comment block or docstring from entry point, or first line of local README |
