# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Carpinteria Huayapam** - An integrated ERP+CRM+CMS platform for a carpentry workshop, built using the BMAD (Business Method and Design) framework v6.0.0-alpha.9.

**Current Status:**
- **Track:** BMad Method (Greenfield)
- **Phase:** 0 (Discovery) - Brainstorming completed Nov 14, 2025
- **Next Steps:** Research → Product Brief → PRD → Architecture → Implementation
- **User:** Carlo (Spanish communication, Spanish documents)

**Project Vision:**
Cloud-first platform with PWAs for internal users and public website for client acquisition. Core entity is the 16-field "Contrato de Entrada a Producción" (Work Order). System must be "invisible in adoption, simpler than WhatsApp."

**Top 3 Priorities:**
1. Work Order structure (16 mandatory fields)
2. Kanban production board
3. Unified Client → Quote → Work Order flow

## BMAD Framework Architecture

This repository contains a sophisticated AI-powered agile development framework with 4 modules, 16 specialized agents, and 48 workflows covering the full SDLC.

### Module Structure

```
bmad/
├── core/          # Foundation: workflow engine, config, BMad Master agent
├── bmm/           # BMad Method: 8 agents, 44 workflows, full SDLC
├── cis/           # Creative Intelligence Suite: 5 agents, creative workflows
└── bmb/           # BMad Builder: Meta-framework for building BMAD components
```

### The Workflow Engine

**Central concept:** All BMAD workflows execute through `bmad/core/tasks/workflow.xml` - the "operating system" for workflow execution.

**Execution flow:**
1. Load `workflow.yaml` configuration
2. Resolve variables from `config.yaml`
3. Execute `instructions.md` steps sequentially
4. Save outputs to `template.md` at checkpoints
5. Run validation via `checklist.md`

**Key patterns:**
- Variables resolve as `{project-root}`, `{bmad_folder}`, `{config_source}:variable_name`
- Templates use `{{variable}}` placeholders
- `<template-output>` tags create save checkpoints with user approval
- `#yolo` mode skips confirmations (auto-complete)
- Workflows can invoke other workflows via `<invoke-workflow>`

### Agent System

**Agent invocation:** Use slash commands like `/bmad:bmm:agents:analyst` or `/bmad:bmm:workflows:research`

**Agent structure (XML-based):**
```xml
<agent id="path" name="Name" title="Title" icon="emoji">
  <activation>
    <step n="2">ALWAYS load config.yaml FIRST to get user_name, language</step>
    <step n="4">Show menu in communication_language</step>
    <step n="5">WAIT for user input</step>
  </activation>
  <persona>Rich personality definition</persona>
  <menu>Available commands</menu>
</agent>
```

**16 Specialized Agents:**
- **Analyst (Mary)** - Research, requirements, product brief
- **PM (John)** - PRD creation, epic breakdown
- **Architect (Winston)** - Architecture decisions
- **SM (Bob)** - Sprint planning, story management
- **DEV (Amelia)** - Implementation
- **TEA (Murat)** - Test architecture
- **UX Designer (Sally)** - User experience design
- **Tech Writer (Paige)** - Documentation
- **+ 5 CIS agents** - Creative facilitation (brainstorming, design thinking, etc.)
- **+ BMB agents** - Framework building

### Configuration System

**Hierarchy:**
1. `bmad/bmm/config.yaml` - Module config (DO NOT MODIFY - links to core)
2. `bmad/core/config.yaml` - Project config (user name, language, folders)
3. `docs/bmm-workflow-status.yaml` - Workflow progress tracker

**Key config variables:**
```yaml
user_name: Carlo
communication_language: Español
document_output_language: Español
output_folder: '{project-root}/docs'
bmad_folder: bmad
```

**Usage in workflows:** Variables referenced as `{config_source}:user_name`

### Workflow Status System

**File:** `docs/bmm-workflow-status.yaml`

**Status values:**
- `required` - Must complete to progress
- `optional` - Can skip
- `recommended` - Strongly suggested
- `conditional` - Required if certain conditions met (e.g., `if_has_ui`)
- `{file-path}` - Completed (points to output file)
- `skipped` - Explicitly skipped

**How to use:**
- Run `/bmad:bmm:workflows:workflow-status` to see current position
- System recommends next workflow based on completion status
- Tracks: brainstorm → research → product-brief → prd → architecture → sprint-planning

## Development Workflow (BMad Method Track)

### Phase 0: Discovery (Optional but Recommended)

```bash
/bmad:bmm:workflows:brainstorm-project  # Interactive brainstorming session
/bmad:bmm:workflows:research            # Market, technical, competitive research
/bmad:bmm:workflows:product-brief       # Define product vision and strategy
```

**Current project:** ✅ Brainstorming complete, → Research next

### Phase 1: Planning

```bash
/bmad:bmm:workflows:prd                      # Product Requirements Document
/bmad:bmm:workflows:create-ux-design         # UX design (if UI-heavy)
/bmad:bmm:workflows:create-epics-and-stories # Break down into epics + stories
```

### Phase 2: Solutioning

```bash
/bmad:bmm:workflows:architecture          # Collaborative architecture decisions
/bmad:bmm:workflows:solutioning-gate-check # Validate readiness for implementation
```

### Phase 3: Implementation

```bash
/bmad:bmm:workflows:sprint-planning  # Generate sprint-status.yaml tracker
/bmad:bmm:workflows:create-story     # Draft next story from epic
/bmad:bmm:workflows:dev-story        # Implement story (DEV agent)
/bmad:bmm:workflows:code-review      # Review completed story
/bmad:bmm:workflows:story-done       # Mark story complete, advance queue
```

**Story lifecycle:** `backlog → drafted → ready → in-progress → review → done`

### Quick Flow Alternative (For Small Features/Bugs)

```bash
/bmad:bmm:workflows:tech-spec        # Lightweight spec (no PRD needed)
# Generates 1-5 stories automatically
/bmad:bmm:workflows:sprint-planning  # Then proceed to implementation
```

## Key Architectural Patterns

### 1. Story Context Assembly

**Problem:** LLMs hallucinate APIs, invent interfaces, create inconsistencies.

**Solution:** Dynamic context assembly via `story-context` workflow:
- Loads latest PRD/Architecture/Tech-spec
- Scans existing codebase for relevant patterns
- Assembles Story Context XML as single source of truth
- DEV agent references this during implementation

**Never guess at interfaces - always reference Story Context.**

### 2. Document Discovery (Smart Input Patterns)

Workflows use `input_file_patterns` in `workflow.yaml`:

```yaml
input_file_patterns:
  prd:
    whole: "{output_folder}/*prd*.md"
    sharded: "{output_folder}/*prd*/*.md"
    load_strategy: FULL_LOAD
```

**Three strategies:**
- **FULL_LOAD** - Load all matching files (PRD, Architecture, brownfield docs)
- **SELECTIVE_LOAD** - Load specific file using template variable (e.g., `epic-{{epic_num}}.md`)
- **INDEX_GUIDED** - Parse index.md, intelligently load relevant documents

**Priority:** Try whole document first, fall back to sharded version.

### 3. Living Documents Pattern

**Don't:** Generate entire document at end.

**Do:** Write incrementally at `<template-output>` checkpoints:
- First checkpoint saves initial content
- Subsequent checkpoints append/edit
- User approves each section before continuing
- Documents evolve through conversation

### 4. Intent-Driven Workflows

Workflows are **facilitative, not templated**:
- Guide discovery through questions
- Adapt based on responses
- Make decisions collaboratively
- Capture rationale, not just outputs

**Example:** Architecture workflow doesn't fill template - it facilitates architectural decisions through Socratic dialogue.

### 5. Multi-Agent Collaboration (Party Mode)

```bash
/bmad:core:workflows:party-mode
```

Orchestrates group discussions between all installed agents. Natural multi-agent conversations for complex decisions.

## Important Conventions

### File Paths and Variables

- **Always use absolute paths** - `{project-root}/docs/output.md`
- **Never hardcode "bmad"** - Use `{bmad_folder}`
- **Load config early** - Step 2 of agent activation MUST load config.yaml
- **Respect language settings** - Communicate in `{communication_language}`, write docs in `{document_output_language}`

### XML Tags in Instructions

```xml
<action>Single action to perform</action>
<action if="condition">Conditional single action</action>

<check if="condition">
  <action>First action in block</action>
  <action>Second action in block</action>
</check>

<ask>Question for user - WAIT for response</ask>
<template-output>Save checkpoint - show content, get approval</template-output>
<invoke-workflow path="...">Execute another workflow</invoke-workflow>
<goto step="3">Jump to step 3</goto>
```

### Workflow Checkpoints

At each `<template-output>` tag, offer:
- **[a] Advanced Elicitation** - Deep questioning via `advanced-elicitation.xml`
- **[c] Continue** - Proceed to next step
- **[p] Party Mode** - Consult other agents
- **[y] YOLO** - Auto-complete rest of document

### Story File Structure

Stories live in `docs/sprint-artifacts/stories/`:

```markdown
# Story: [Title]

**Epic:** epic-X.md
**Status:** drafted | ready | in-progress | review | done
**Points:** 3
**Assigned:** Amelia (DEV)

## User Story
As a [role], I want [goal], so that [benefit]

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Tasks
- [ ] Task 1
- [ ] Task 2

## Technical Notes
[Implementation guidance]

## Testing Strategy
[How to verify]
```

## Project-Specific Context

### Business Domain

**Carpinteria Huayapam** is a carpentry workshop transitioning to digital operations:

- **Pain points:** Projects "flying" without ownership, imprecise costs, material waste, unclear responsibilities
- **Root cause:** "Working ON the system" not perceived as productive work in artisan culture
- **Cultural requirement:** System must feel productive immediately, not add administrative burden

### System Architecture (from Brainstorming)

**Central Entity:** "Contrato de Entrada a Producción" (Work Order) with 16 mandatory fields:

**Client Data (4 fields):**
1. Client name
2. Phone/WhatsApp
3. Installation address
4. Origin channel

**Product Specs (6 fields):**
5. Furniture type
6. Agreed measurements
7. Basic layout
8. Main material
9. Finish
10. Special conditions

**Material Plan (2 fields):**
11. Estimated materials list
12. Availability verification

**Work Plan (4 fields):**
13. Project owner (carpenter)
14. Stages and order
15. Timeline and target date
16. Agreed price and payment terms

**System is NOT three modules** (ERP/CRM/CMS) - it's a **living knowledge graph** where Client ↔ Product ↔ Production data interconnect.

### Technical Direction (Updated Nov 14, 2025)

**Architecture:** Cloud-first with PWAs (not offline-first)
- PWAs specific per user type
- Public website for client acquisition and content marketing
- Shared components in monorepo
- Real-time updates for Kanban board

**Research priorities:**
1. Cloud-first stack (Next.js/SvelteKit + Supabase/Firebase)
2. Multi-tenant architecture (single DB vs separated)
3. PWA patterns for mobile-first UX
4. Auth/RBAC for multiple roles
5. SSR/SSG for public site vs SPA for internal PWAs

## Git Workflow

**Commit message format:**
```
Subject line (imperative, concise)

- Bullet points describing changes
- Focus on "why" not just "what"

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Branch:** Work on `main` (greenfield project, single developer)

**Remote:** https://github.com/carlo-spada/carpam.git

## Common Commands

### Agent Activation
```bash
/bmad:bmm:agents:analyst      # Business analysis, research
/bmad:bmm:agents:pm           # Product management
/bmad:bmm:agents:architect    # Architecture design
/bmad:bmm:agents:dev          # Implementation
```

### Workflow Execution
```bash
/bmad:bmm:workflows:workflow-status    # Check current position
/bmad:bmm:workflows:research           # Next step for this project
/bmad:bmm:workflows:product-brief      # After research
/bmad:bmm:workflows:prd                # Product requirements
/bmad:bmm:workflows:architecture       # Technical design
```

### Creative/Facilitation
```bash
/bmad:cis:workflows:brainstorming      # Interactive brainstorming
/bmad:core:workflows:party-mode        # Multi-agent discussion
```

### Meta-Building (Framework Development)
```bash
/bmad:bmb:workflows:create-agent       # Build new agent
/bmad:bmb:workflows:create-workflow    # Build new workflow
/bmad:bmb:workflows:audit-workflow     # Validate workflow quality
```

## Critical Files (Do Not Modify Manually)

- `bmad/_cfg/*.csv` - Manifests auto-generated during installation
- `bmad/bmm/config.yaml` - Links to core config, do not edit
- `docs/bmm-workflow-status.yaml` - Updated by workflows, not manually
- `docs/sprint-status.yaml` - Generated by sprint-planning workflow

## When Things Go Wrong

**Agent not loading?**
- Check config.yaml was loaded in step 2 of activation
- Verify `{user_name}` and language variables resolved

**Workflow fails?**
- Re-read `bmad/core/tasks/workflow.xml` for execution rules
- Check workflow.yaml for correct variable references
- Ensure config_source points to valid config file

**Context missing?**
- Use `discover_inputs` protocol to load PRD/Architecture/etc.
- Check `input_file_patterns` in workflow.yaml
- Try whole document pattern before sharded pattern

**Story Context hallucinations?**
- Run `story-context` workflow to assemble fresh context
- Reference Story Context XML, never guess interfaces
- Load existing code patterns before inventing new ones

## Documentation References

- **BMM Guide:** `bmad/bmm/docs/README.md`
- **Agents Guide:** `bmad/bmm/docs/agents-guide.md`
- **Scale System:** `bmad/bmm/docs/scale-adaptive-system.md`
- **Quick Start:** `bmad/bmm/docs/quick-start.md`
- **Brainstorming Output:** `docs/bmm-brainstorming-session-2025-11-14.md`

## Next Steps for This Project

1. ✅ Brainstorming (completed Nov 14)
2. → **Research workflow** (technical cloud-first stack investigation)
3. → Product Brief (define vision, KPIs, roadmap)
4. → PRD (detailed requirements for MVP)
5. → Architecture (collaborative technical decisions)
6. → Sprint Planning (break into implementable stories)
7. → Implementation (story-by-story development)
