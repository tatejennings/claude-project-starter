# Claude Code Project Documentation Guide

A complete reference for setting up and maintaining project documentation that Claude Code can use effectively. Give this file to Claude Code at the start of a new project to establish the documentation foundation before any code is written.

---

## Overview

Good project documentation serves two audiences: **you** (the developer making decisions) and **Claude Code** (the agent implementing them). The structure below gives Claude Code the context it needs to build correctly — what to build, how to build it, why decisions were made, and what order to build it in — without stuffing everything into context at once.

The documentation is created **before implementation begins**. Claude Code reads the relevant files at the start of each task, not all at once. This keeps context focused and output quality high.

---

## Documentation Structure

### The `/docs` Folder

```
project-root/
├── AGENTS.md                    # Agent instructions — single source of truth for all AI tools
├── CLAUDE.md                    # Claude Code pointer → "See @AGENTS.md"
├── docs/
│   ├── PRODUCT.md               # Product requirements (what & why)
│   ├── ARCHITECTURE.md          # System overview, tech stack, data flow
│   ├── API.md                   # API contracts, endpoints, request/response shapes
│   ├── CONVENTIONS.md           # Code style, naming, file organization rules
│   ├── DECISIONS.md             # Architecture Decision Records (ADRs)
│   ├── ROADMAP.md               # Build-order milestones with dependencies
│   ├── 1 - Discovery/           # Business brief, market research, competitive analysis
│   └── 2 - Specs/               # Breakout specs for detailed topics
│       ├── database-schema.md
│       ├── authentication.md
│       └── ...
```

### What Each File Does

| File | What It Contains | When Claude Code Reads It |
|---|---|---|
| **AGENTS.md** | Project overview, tech stack, doc pointers, common commands, key code patterns, conventions quick-reference — the single source of truth for all AI coding agents | Every session (referenced by CLAUDE.md) |
| **CLAUDE.md** | Points to AGENTS.md. Can include Claude-specific overrides if needed, but generally just a pointer. | Every session (auto-loaded from repo root) |
| **PRODUCT.md** | User stories, features, data models, acceptance criteria, edge cases, UI flows | Before implementing any feature |
| **ARCHITECTURE.md** | Component relationships, tech stack rationale, repo structure, module boundaries, data flow diagrams, deployment topology | Starting new features, understanding system design, refactoring |
| **API.md** | Every endpoint with method, path, request body, response shape, auth requirements, rate limits | Building any endpoint or client that calls one |
| **CONVENTIONS.md** | Code patterns, naming rules, file organization, testing approach, anti-patterns to avoid | Any code generation task |
| **DECISIONS.md** | Past architectural choices — what was decided, what was rejected, why | When Claude suggests something you've already evaluated |
| **ROADMAP.md** | Sequenced milestones with dependencies, spec references, and "done when" criteria | Before starting any milestone; to understand what's next |
| **1 - Discovery/** | Business context, market research, competitive analysis | Rarely by Claude Code; primarily for human reference |
| **2 - Specs/** | Detailed breakout specs for topics that need more depth than the high-level docs provide | Before implementing the specific system that spec covers |

---

## File Specifications

### AGENTS.md (Repo Root)

**Location:** Repo root, alongside CLAUDE.md.

**Purpose:** The single source of truth for all AI coding agents (Claude Code, Codex, Cursor, Windsurf, Xcode Agent, etc.). Every agent-readable instruction lives here. Tool-specific files (CLAUDE.md, .cursorrules, etc.) simply point to this file.

**Structure:**

```markdown
# AGENTS.md — {Project Name}

## Project Overview
Brief description (2-3 sentences) of what the project does.

## Tech Stack
- Language/framework with versions
- Database
- Key dependencies and services
- Build tools

## Documentation
| Doc | When to Read |
|---|---|
| `docs/PRODUCT.md` | Before implementing any feature — contains requirements, user flows, acceptance criteria |
| `docs/ARCHITECTURE.md` | When starting a new feature, refactoring, or needing to understand system design |
| `docs/API.md` | When building any endpoint or client that calls one |
| `docs/CONVENTIONS.md` | Before writing any code — code style, naming, file organization, testing rules |
| `docs/DECISIONS.md` | Before suggesting alternatives to existing patterns — check if it's already been evaluated |
| `docs/ROADMAP.md` | Before starting any milestone — build order, dependencies, "done when" criteria |
| `docs/2 - Specs/*` | Before implementing the specific system that spec covers — detailed blueprints |

## Architecture Principles
The 3-5 non-negotiable rules for this project (e.g., "database enforces access control",
"server-side only for sensitive operations", "entity isolation is absolute").

## Common Commands
Build, test, lint, dev server, database reset, type generation — whatever a developer
runs daily.

## Code Conventions (Quick Reference)
The top 10 rules from CONVENTIONS.md that apply to every task. Keep it scannable.

## Key Patterns
2-3 annotated code snippets showing the standard patterns for common operations
(API route structure, database query pattern, etc.)

## Current Focus
Check `docs/ROADMAP.md` — see the Status section at the bottom for the current milestone.
```

**Rules:**
- Don't enumerate every file in the project — agents can explore
- Don't paste full specs — point to them
- Do include common commands (agents can't guess your build/test scripts)
- Do include the 2-3 most important code patterns (saves agents from inventing their own)
- Update the "Current Focus" section when you start a new milestone

> **iOS projects:** Include Swift version, minimum deployment target, SwiftUI vs UIKit, dependency manager, async patterns (`async/await` vs Combine), Xcode build commands, and any Run Script phases that affect codegen.

---

### CLAUDE.md (Repo Root)

**Location:** Must be at the project root. Claude Code auto-loads this file at the start of every session.

**Purpose:** Points Claude Code to AGENTS.md. Keeps all agent instructions in one place so you don't maintain duplicate content across tool-specific files.

**Contents:**

```markdown
See @AGENTS.md
```

That's it. If you ever need Claude-specific overrides (rare), add them below the reference:

```markdown
See @AGENTS.md

## Claude-Specific Notes
- {Any Claude Code-only behavior, if needed}
```

**Why this pattern:** Other AI tools use different config files (.cursorrules, Codex config, etc.) but they all need the same project context. AGENTS.md is the canonical source. Each tool's config file just points to it. One place to update, every tool stays in sync.

---

### PRODUCT.md

**Purpose:** Defines *what* gets built and *why*. This is the product requirements document. Named `PRODUCT.md` (not `PRD.md`) for consistency — no acronyms in file names.

**Should contain:**
- Product overview and target user
- Business model / monetization
- Feature descriptions with user flows
- Data model definitions (entities, relationships, field types)
- Acceptance criteria per feature
- Edge cases and error states
- Security requirements
- UI/UX descriptions (screen inventory, navigation structure)
- What's in scope vs. explicitly out of scope

**Should NOT contain:**
- Implementation details (that's ARCHITECTURE.md and specs)
- API endpoint definitions (that's API.md)
- Code patterns (that's CONVENTIONS.md)

**Tip:** Version this file (e.g., "Phase 1 v1.1"). When you iterate on requirements, bump the version and note what changed.

---

### ARCHITECTURE.md

**Purpose:** Defines *how the system fits together*. The bridge between product requirements and implementation.

**Should contain:**
- Architecture philosophy / guiding principles
- High-level data flow diagram (ASCII art works well for Claude Code)
- Tech stack table with rationale for each choice
- Repository structure (full directory tree with annotations)
- Module boundaries — what runs where and why
- Service communication patterns (what calls what)
- Database overview — table inventory, relationship summary, NOT full SQL (that goes in a spec)
- Key integration points (webhooks, pipelines, sync protocols)
- Deployment topology and environments

**Should NOT contain:**
- Full SQL migrations (that's `2 - Specs/database-schema.md`)
- Endpoint definitions (that's API.md)
- Business requirements (that's PRODUCT.md)

---

### API.md

**Purpose:** The contract between services. Every endpoint that exists or will exist.

**For each endpoint, document:**
- Method + path
- Auth requirements
- Request body (with TypeScript-style type annotations or JSON examples)
- Response body (success and error shapes)
- Query parameters for GET endpoints
- Side effects (e.g., "triggers Inngest event", "sends email")
- Rate limits

**Include a standard error response format** at the top so every endpoint follows it.

**Include a rate limiting table** summarizing limits by endpoint category.

---

### CONVENTIONS.md

**Purpose:** The coding standards. Claude Code follows these on every task without being asked.

**Should contain:**
- Language-specific rules (naming, typing, error handling, imports)
- File organization rules (one component per file, colocated tests, no barrel files, etc.)
- Framework-specific patterns (Server Components by default, validation at API boundary, etc.)
- Database conventions (naming, timestamps, soft deletes, FK behavior)
- Git conventions (branch naming, commit message format)
- Testing conventions (what to test, how to test, where tests live)
- Explicit anti-patterns ("never do X because Y")

**If the project spans multiple languages** (e.g., TypeScript + Swift), include a section for each language.

> **iOS projects:** Specify `async/await` vs Combine preference, `@Observable` vs `ObservableObject`, SwiftData vs Core Data, and whether you use `@MainActor` explicitly.

---

### DECISIONS.md

**Purpose:** Architecture Decision Records. Prevents Claude Code from suggesting things you've already evaluated and rejected.

**For each decision:**

```markdown
## ADR-{N}: {Decision Title}

**Date:** {Month Year}
**Status:** Accepted

**Context:** What problem needed solving.

**Options considered:**
1. Option A — brief description
2. Option B — brief description
3. Option C — brief description

**Decision:** Which option was chosen.

**Rationale:** Why this option won. Be specific about the technical reasons.

**Tradeoff:** What you gave up. Be honest — every decision has a cost.
```

Good ADRs save time. When Claude Code suggests "why not use X?", you can point to the ADR that already evaluated X.

---

### 2 - Specs/ (Breakout Specs)

**Purpose:** Detailed specifications for topics that need more depth than the high-level docs provide. These are NOT limited to user-facing features — they cover any area of the system that benefits from a dedicated, detailed document.

**Common spec topics:**
- Database schema (full SQL migrations, RLS policies, indexes)
- Encryption / security implementation
- Email ingestion pipeline
- File import/export pipelines
- Matching or scoring algorithms
- Offline sync protocols
- Background job definitions
- Admin system implementation
- Authentication flows (when complex)

**When to create a spec:** If a section of ARCHITECTURE.md or PRODUCT.md is getting too long or too detailed, break it out into a spec. The high-level doc should summarize and link to the spec: *"Full schema in `2 - Specs/database-schema.md`."*

**Spec format:** Each spec should be self-contained enough that Claude Code can read it and implement what it describes. Include:
- Overview of what the spec covers
- Reference back to which PRODUCT.md sections it implements
- Technical details (schemas, algorithms, prompt templates, data flows)
- Error handling table (what can go wrong, how to handle it)
- Monitoring / observability notes (what to track)

---

### ROADMAP.md

**Purpose:** Build-order milestone sequence. Tells Claude Code what to build next, what it depends on, and what "done" looks like.

**See the companion document** `03-generate-roadmap.md` for the complete process of creating this file from the other documentation.

**Key properties:**
- Milestones sequenced by **technical dependencies**, not by PRODUCT.md section order
- Each milestone scoped to **1-2 focused days** of work
- Each milestone includes: description, dependencies, doc references, definition of done
- No status indicators on individual milestones — all progress tracked in a single **Status section at the bottom** listing completed milestones and what's up next
- No task-level breakdown within milestones (Claude Code's Tasks system handles that)
- Includes dependency graph diagram, parallelization notes, and critical path

---

## Setting Up a New Project

### Step-by-Step

1. **Start with PRODUCT.md.** Define what you're building, for whom, and why. Include data models, features, flows, and acceptance criteria. This is the foundation everything else derives from.

2. **Write ARCHITECTURE.md.** Translate product requirements into system design. Choose the tech stack, define the repo structure, draw the data flow, establish module boundaries. This is where PRODUCT.md's "what" becomes "how."

3. **Write API.md.** Define every endpoint contract. Request shapes, response shapes, auth, errors. If the project has a mobile app, the API doc is the contract between server and client.

4. **Write CONVENTIONS.md.** Establish code style before any code exists. This prevents inconsistency from the first line.

5. **Write DECISIONS.md.** Document the architectural choices you made in steps 2-4. Every "we chose X over Y" gets an ADR.

6. **Write breakout specs** for anything that needs more detail. Database schema is almost always a spec. Pipelines, sync protocols, and complex algorithms usually need specs too.

7. **Create AGENTS.md** at the repo root. This is the single source of truth for all AI agents. Point to all the docs. Include commands and key patterns.

8. **Create CLAUDE.md** at the repo root with contents: `See @AGENTS.md`. If you use other AI tools, create their config files the same way (e.g., `.cursorrules` pointing to AGENTS.md).

9. **Generate ROADMAP.md.** Use Claude Code in plan mode to read all docs and produce the milestone sequence. (See `03-generate-roadmap.md` for the full process and prompt template.)

### Prompt Template

Give this entire file to Claude along with your completed business brief:

```
Here is my completed business brief:

{PASTE YOUR BUSINESS BRIEF HERE}

Follow the documentation creation process defined in this document. Work through
each document in the step-by-step order above — one at a time, getting my feedback
before moving to the next. Ask me questions whenever the brief is ambiguous rather
than guessing at requirements.
```

---

### What to Do First Session with Claude Code

Once all docs exist, your first Claude Code session should be:

```
Read CLAUDE.md, then read docs/ROADMAP.md. Start with the first milestone (M0).
Enter plan mode — read the specs listed in M0, then plan the implementation.
```

From there, the workflow for every subsequent milestone is:

1. Update ROADMAP.md — move the milestone to "Up next" in the Status section
2. Tell Claude Code: *"Start milestone M{N}. Read the listed specs and plan the implementation."*
3. Review the plan, adjust, say go
4. When done, move the milestone to "Completed" in the Status section and update "Up next"

---

## Context Management

### Keep Context Focused

- **Start fresh sessions** for unrelated tasks — context pollution degrades output quality
- **Point to specific docs** when starting a task: *"Read `docs/2 - Specs/email-ingestion.md` then implement the Postmark webhook handler"*
- **Don't load all docs at once.** CLAUDE.md tells Claude Code *when* to read each file. Let it load docs on demand.
- **Use `/compact` aggressively** during long sessions. The docs are on disk — Claude Code can re-read them after compaction.

### .claudeignore

Exclude files that add noise without value:

```
# Build artifacts
node_modules/
.next/
dist/
build/

# iOS
DerivedData/
*.xcodeproj/xcuserdata/
*.xcworkspace/xcuserdata/
Pods/
.build/
*.ipa
*.dSYM.zip

# Large assets
*.mp4
*.mov
*.zip

# Environment
.env
.env.local
```

### Git Hygiene

- **Commit frequently.** Claude Code works better with clean git state and can reference recent commits.
- **Use git worktrees** for parallel Claude Code sessions on different features.
- **Checkpoint after successful changes.** Treat Claude Code output like a PR from a teammate — review diffs before accepting.

---

## Working with Claude Code

### Prompting Patterns

**Be explicit about scope:**
- ✅ *"Edit only `lib/encryption/crypto.ts`"*
- ❌ Letting it touch multiple files without guidance

**Reference files directly:**
- *"Follow the pattern in `lib/supabase/server.ts`"*
- *"Match the style in `components/ui/Button.tsx`"*

**State constraints upfront:**
- *"Don't add new dependencies"*
- *"Use the existing error handling pattern from CONVENTIONS.md"*

**Ask for plans first on complex tasks:**
- *"Outline your approach before writing code"*
- Plan mode (Shift+Tab) is built for this

**For iOS projects:**
- *"Follow the ViewModel pattern in `Features/Profile/ProfileViewModel.swift`"*
- *"Use the same network request structure as `Services/APIClient.swift`"*

### Validation Loop

After every meaningful change:
1. *"Run the test suite and fix any failures"*
2. *"Run lint and fix violations"*
3. *"Review what you just wrote for edge cases"*

Include build, test, and lint commands in CLAUDE.md so Claude Code can run them without asking.

---

## Xcode 26.3+ Integration

> **Note:** This section covers anticipated Xcode integration with Claude via MCP. Verify against your current Xcode version — features described here may not yet be available.

With Xcode's native Claude Agent support via MCP, some practices shift:

### What Changes

| Practice | Why It Changes |
|---|---|
| `.claudeignore` configuration | Xcode controls context exposure via MCP |
| Manual `xcodebuild` commands | Claude builds natively through Xcode |
| Explicit file references | Claude explores the project autonomously |
| Describing expected UI | Claude can see Previews directly |

### What Still Matters

| Documentation | Why |
|---|---|
| ARCHITECTURE.md | High-level intent is faster than exploration |
| CONVENTIONS.md | Style preferences aren't inferrable from code alone |
| DECISIONS.md | *Why* you rejected alternatives — prevents re-litigating |
| PRODUCT.md / specs | Product requirements aren't in the codebase |
| API.md | External service contracts |
| ROADMAP.md | Build order and current focus |

### When to Use Terminal Claude Code Instead

| Scenario | Why Terminal Wins |
|---|---|
| Multi-repo / monorepo work | Xcode scopes to one project |
| Non-Apple platforms | Xcode is Apple-only |
| Backend + iOS in tandem | Terminal spans both codebases |
| Custom toolchains | More control over environment |
| Parallel sessions on same project | Xcode requires git worktree workaround |

---

## Quick Reference

### Do

- ✅ Create all documentation before writing any code
- ✅ Keep CLAUDE.md concise — it loads every session
- ✅ Be specific about scope and constraints in prompts
- ✅ Ask for plans before implementation on complex tasks
- ✅ Commit frequently for clean rollback points
- ✅ Run tests and lint after changes
- ✅ Start fresh sessions for unrelated work
- Update ROADMAP.md Status section as milestones complete
- ✅ Name files descriptively — PRODUCT not PRD, no acronyms

### Don't

- ❌ Stuff all docs into context at once
- ❌ Let Claude Code modify files without explicit guidance
- ❌ Skip reviewing diffs before accepting changes
- ❌ Continue polluted sessions across unrelated tasks
- ❌ Track granular tasks in ROADMAP.md (use Claude Code Tasks or GitHub Issues)
- ❌ Duplicate content across docs (summarize and link instead)
- ❌ Forget to checkpoint successful changes with commits
