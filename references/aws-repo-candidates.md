# AWS Repository Candidates -- Shape R2 (Solo / Small-Team)

Research date: 2026-04-15

---

## Tier 1: Strong R2 Matches (Solo Developer / Small Team, 100+ Commits)

### 1. awslabs/damo

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/damo |
| Language | Python (100%) |
| Commits | ~4,010 |
| Contributors | 13 total, but **95.9% from sjp38** (3,843 of 4,008 human commits) |
| Stars | 163 |
| Tags | 30 |
| License | GPL-2.0 |
| Created | 2021-03-31 |

**Why interesting:** DAMON (Data Access MONitor) user-space tool. This is a classic "internal Linux kernel tool that got open-sourced" profile. sjp38 (SeongJae Park) is the near-sole author. The commit messages are meaningful and descriptive (e.g., "treewide: Explicitly specify min_help param", "damo_record: Add --timeout option"). The repo has visible refactoring phases including API cleanups, argument parser restructuring, and deprecation notices (moved to another GitHub org). 4,000+ commits with disciplined, kernel-style commit conventions make this excellent for testing temporal understanding.

**Caveat:** GPL-2.0 license. Recently deprecated in favor of a non-AWS repo.

---

### 2. awslabs/fast-differential-privacy

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/fast-differential-privacy |
| Language | Python (100%) |
| Commits | ~212 |
| Contributors | 5 total, but **90.5% from woodyx218** (190 of 210 human commits) |
| Stars | 140 |
| Tags | 4 (minimal) |
| License | Apache-2.0 |
| Created | 2022-11-20 |

**Why interesting:** Research-to-production library for fast differential privacy training. Nearly a solo project by one researcher. Commit history shows evolution from initial research code toward production-ready patterns (adding ZeRO distributed training, FSDP support, extending to new model families). Visible feature addition phases with minimal tags suggest organic, research-driven development.

---

### 3. awslabs/keys_values

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/keys_values |
| Language | Python (90.8%), CUDA (5.1%), C++ (4.0%) |
| Commits | ~149 |
| Contributors | 3 (mseeger has **97.1%** of commits) |
| Stars | 10 |
| Tags | 1 (v0.1.0) |
| License | Apache-2.0 |
| Created | 2025-11-05 |

**Why interesting:** Efficient LLM inference with KV caching. Essentially a solo project by mseeger. Very new, actively developed (commits from April 2026). Shows fast-moving development with visible architectural evolution: adding CUDA/Triton kernel integration (FlashInfer), buffer strategy abstractions, and acknowledged technical debt (CPU offloading described as "suboptimal" with tracking issue). Commit messages show real working patterns ("ran flake and black", "Small fix", refactoring commits). Minimal tags = no formal versioning yet.

---

### 4. awslabs/aws-code-habits

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/aws-code-habits |
| Language | Makefile (95.1%), Python (3.2%), Shell (1.5%) |
| Commits | ~151 |
| Contributors | 4 total, but **94.0% from valter-silva-au** |
| Stars | 84 |
| Tags | 7 (v1.0.0 through v1.5.0) |
| License | MIT-0 |
| Created | 2022-10-25 |

**Why interesting:** A Make-based development governance library. Nearly all commits from one developer. Has an explicit `refactoring-plan.md` in the repo documenting planned architectural improvements. Commit history shows evolution: adding AWS CDK support, pnpm integration, nvm management, cross-platform compatibility. Well-structured version tags with meaningful releases. Uses conventional commits and pre-commit hooks. Good example of a solo dev building a productivity tool.

---

### 5. awslabs/llmeter

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/llmeter |
| Language | Python (100%) |
| Commits | ~237 |
| Contributors | 4 (acere ~63.6%, athewsey ~33%) |
| Stars | 33 |
| Tags | 10 (v0.1.0 through v0.1.8) |
| License | Apache-2.0 |
| Created | 2024-09-27 |

**Why interesting:** LLM latency/throughput benchmarking library. Effectively a 2-person project. Shows clear architectural evolution: movement from low-level Runner classes to high-level LoadTest experiments, adding OpenAI Responses API support, multi-modal payloads, and streaming. Commit messages use conventional prefixes (refactor, feat, fix, doc, test) making temporal analysis easier. Active development with refactoring visible in recent commits (April 2026). Good candidate for testing how an agent understands API evolution.

---

### 6. awslabs/rhubarb

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/rhubarb |
| Language | Python (68.4%), Jupyter Notebook (30.8%) |
| Commits | ~135 |
| Contributors | 6 (anjanvb ~47.8%, but top 3 make up ~80%) |
| Stars | 102 |
| Tags | 11 (v0.0.1 through v0.0.8) |
| License | Apache-2.0 |
| Created | 2024-04-17 |

**Why interesting:** Multi-modal document understanding framework for Amazon Bedrock. Small team, primarily one lead developer. Shows visible feature expansion: started with document Q&A, grew to add video analysis, native PDF support, MCP integration, cross-region inference, and streaming responses. Addition of `memory-bank/` and `pyrhubarb-mcp/` directories shows architectural expansion. Meaningful release notes ("Model Refresh, Native PDF Support, Global Cross-Region Inference").

---

### 7. awslabs/slapo

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/slapo |
| Language | Python (100%) |
| Commits | ~394 |
| Contributors | 7 (chhzh123 ~71.1%, comaniac ~23.6%) |
| Stars | 152 |
| Tags | 3 (minimal) |
| License | Apache-2.0 |
| Created | 2023-01-12 |

**Why interesting:** Schedule language for large model training. Primarily a 2-person project. Commit messages show clear development phases using categorical prefixes: [Feature], [Primitive], [Pipeline], [Tracer], [CI], [Bugfix], [Verification], [Autoshard]. Shows visible evolution including adding TorchDynamo support, DeepSpeed compatibility, resharding support, and HuggingFace tracer enhancements. Meaningful, well-categorized commits make this excellent for testing temporal understanding of feature development phases.

---

## Tier 2: Evolution-Rich Candidates (Not Strictly R2, But Interesting for Agent Testing)

### 8. awslabs/palace

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/palace |
| Language | C++ (100%) |
| Commits | ~2,217 |
| Contributors | 15 (sebastiangrimberg ~40%, hughcars ~23.5%) |
| Stars | 490 |
| Tags | 8 (v0.11.0 through v0.16.0) |
| License | Apache-2.0 |
| Created | 2022-11-28 |

**Why interesting:** 3D finite element solver for computational electromagnetics. Dominated by 2-3 developers. Rich commit history with detailed, meaningful messages about mesh partitioning, error estimation, multigrid operators. Shows deep technical evolution with library upgrades (libCEED patches, MFEM interface changes). Version tags with clear changelogs. C++ makes it more challenging for analysis but the evolution is very visible.

---

### 9. awslabs/backstage-plugins-for-aws

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/backstage-plugins-for-aws |
| Language | TypeScript (100%) |
| Commits | ~513 |
| Contributors | 22 (niallthomson 95 human commits, rest are bots or 1-commit contributors) |
| Stars | 149 |
| Tags | 30+ |
| License | Apache-2.0 |
| Created | 2023-12-26 |

**Why interesting:** AWS plugins for Backstage developer portal. Effectively a solo project by niallthomson with heavy bot automation (renovate, dependabot). TypeScript with many tagged releases. The Backstage plugin ecosystem involves visible convention changes and API evolution as Backstage itself evolves. Good for testing how an agent handles framework-driven convention changes.

---

### 10. awslabs/aws-ec2rescue-linux

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/aws-ec2rescue-linux |
| Language | Python (100%) |
| Commits | ~275 |
| Contributors | 6 (Drudenhaus ~63.3%, gregbdunn ~31.6%) |
| Stars | 187 |
| Tags | 10 |
| License | Apache-2.0 |
| Created | 2017-06-30 |

**Why interesting:** EC2 diagnostic tool. Essentially a 2-person project over 7+ years. Long development history means visible evolution of Python practices. Internal AWS tool that was open-sourced (matching the R2 profile exactly). Tagged releases suggest periodic stabilization phases.

---

### 11. awslabs/LISA

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/LISA |
| Language | Python |
| Commits | ~1,094 |
| Contributors | 18 (estohlmann ~48%, bedanley ~19.2%, dustins ~8.3%) |
| Stars | 81 |
| Tags | 30 |
| License | Apache-2.0 |
| Created | 2024-05-14 |

**Why interesting:** LLM inference solution for Amazon Dedicated Cloud. Dominated by 2-3 developers building a complete deployment platform. High commit velocity (1,094 commits in under 2 years) with many tags suggests rapid iteration. A complex system built by a small team -- good for testing whether agents can follow the evolution of a multi-component platform.

---

## Tier 3: Noteworthy But Not R2

### 12. awslabs/agent-squad

| Field | Value |
|-------|-------|
| URL | https://github.com/awslabs/agent-squad |
| Language | Python (63.8%), TypeScript (36.2%) |
| Commits | ~1,109 |
| Contributors | 25 |
| Stars | 7,500 |
| Tags | 17 |
| License | Apache-2.0 |

**Why interesting (for evolution testing):** Dual Python/TypeScript implementation of an agent orchestration framework. The dual-language nature means there are visible patterns of feature parity maintenance, and the framework has evolved through naming/branding changes. Too many contributors for R2, but interesting for testing cross-language evolution understanding.

---

## Summary Table

| Repo | Lang | Commits | Contributors | Top Dev % | Tags | Stars | License | R2 Match |
|------|------|---------|-------------|-----------|------|-------|---------|----------|
| damo | Python | 4,010 | 13 | 95.9% | 30 | 163 | GPL-2.0 | Strong |
| fast-differential-privacy | Python | 212 | 5 | 90.5% | 4 | 140 | Apache-2.0 | Strong |
| keys_values | Python/CUDA | 149 | 3 | 97.1% | 1 | 10 | Apache-2.0 | Strong |
| aws-code-habits | Makefile | 151 | 4 | 94.0% | 7 | 84 | MIT-0 | Strong |
| llmeter | Python | 237 | 4 | 63.6% | 10 | 33 | Apache-2.0 | Strong |
| rhubarb | Python | 135 | 6 | 47.8% | 11 | 102 | Apache-2.0 | Moderate |
| slapo | Python | 394 | 7 | 71.1% | 3 | 152 | Apache-2.0 | Moderate |
| palace | C++ | 2,217 | 15 | 40.0% | 8 | 490 | Apache-2.0 | Weak (but evolution-rich) |
| backstage-plugins-for-aws | TypeScript | 513 | 22 | solo+bots | 30+ | 149 | Apache-2.0 | Moderate |
| aws-ec2rescue-linux | Python | 275 | 6 | 63.3% | 10 | 187 | Apache-2.0 | Moderate |
| LISA | Python | 1,094 | 18 | 48.0% | 30 | 81 | Apache-2.0 | Weak (but evolution-rich) |

## Top Picks for Agent Testing (Codebase Evolution Understanding)

1. **awslabs/slapo** -- Best categorized commit messages ([Feature], [Pipeline], etc.), clear phases, manageable size
2. **awslabs/llmeter** -- Conventional commits, visible API evolution, active refactoring, small enough to comprehend
3. **awslabs/damo** -- Massive history with kernel-style commit discipline, but may be too large
4. **awslabs/keys_values** -- Very active, visible technical debt acknowledgment, real working patterns
5. **awslabs/aws-code-habits** -- Has an explicit refactoring plan document, clean version history
