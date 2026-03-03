---
name: design-swarm
description: |
  Orchestrate a team of 10 design agents for holistic UI audit, ideation, and implementation.
  Use when: "design swarm", "holistic audit", "design review with all lenses", "full design analysis".
  Modes: audit, ideate, implement (any combination).
  Targets: screenshots, URLs, file paths, or text briefs.
argument-hint: "[audit|ideate|implement] [target path, URL, or brief]"
---

# Design Swarm

Orchestrate 10 specialized design agents — each embodying a different skill/lens — to run holistic audits, ideation sessions, and implementations in parallel.

---

## Orchestration Flow

When invoked, follow these steps exactly:

### 1. Parse Arguments

Extract from the invocation:
- **Modes**: One or more of `audit`, `ideate`, `implement` (default: `audit` if none specified)
- **Target**: The thing to evaluate — one of:
  - Screenshot path (`.png`, `.jpg`, `.webp`)
  - URL (`http://` or `https://`)
  - File path (code file)
  - Text brief (quoted description of an interface)

### 2. Detect Target Type

| Signal | Type | Notes |
|--------|------|-------|
| `.png`, `.jpg`, `.webp`, `.gif` | screenshot | Read the image directly |
| `http://`, `https://` | url | Use browser tools to snapshot the page |
| Existing file path | code | Read the file(s) |
| Everything else | brief | Treat as text description |

### 3. Load Orchestration Context

Read these files:
- `modules/orchestrator.md` — synthesis logic, conflict resolution, final report template
- `references/scoring-rubric.md` — universal 1-10 scale
- `references/agent-weights.md` — default weights for composite scoring

### 4. Create Team

```
TeamCreate(team_name: "design-swarm", description: "Holistic design audit/ideation/implementation")
```

### 5. Execute Phases

For each requested mode, in order (`audit` → `ideate` → `implement`):

#### Phase: Audit

1. **Determine participating agents** (see orchestrator.md Phase Gating):
   - Screenshot-only: skip `baseline-ui`
   - Code-only: all 10
   - URL: all 10 (snapshot first, then inspect code if accessible)
   - Brief: all 10 (theoretical assessment)

2. **Create tasks** via TaskCreate — one per participating agent

3. **Spawn agents in parallel** via Task tool. For each agent:

   a. Read the agent template: `modules/agents/<agent-name>.md`
   b. Read the source knowledge file(s) listed in the template's "Source Knowledge" section
   c. Construct the agent prompt by combining:
      - The full agent template (structure + evaluation criteria)
      - The full source knowledge content
      - The target context (screenshot/URL snapshot/code/brief)
      - Mode: "audit"
      - The scoring rubric content
      - Instructions to mark task complete and send report via SendMessage to "team-lead"

   ```
   Task(
     name: "<agent-name>",
     team_name: "design-swarm",
     subagent_type: "general-purpose",
     run_in_background: true,
     prompt: [constructed prompt above]
   )
   ```

4. **Wait** for all agents to complete (they'll send reports via SendMessage)

5. **Synthesize** per orchestrator.md:
   - Collect all reports
   - Deduplicate findings
   - Aggregate by severity
   - Compute weighted composite score
   - Identify and resolve conflicts
   - Generate the final audit report

6. **Present** the synthesized audit report to the user

#### Phase: Ideate

1. **Create tasks** for each agent (blocked by audit synthesis if audit was run)

2. **Spawn agents** with the same process as audit, but:
   - Mode: "ideate"
   - Include the synthesized audit findings in each agent's prompt
   - Each agent proposes improvements through their specific lens

3. **Synthesize** ideation proposals:
   - Merge overlapping proposals
   - Categorize by impact/effort matrix
   - Produce prioritized improvement roadmap

4. **Present** the improvement roadmap to the user

#### Phase: Implement

1. **Spawn a single `general-purpose` agent** with:
   - The full synthesized audit report
   - The prioritized improvement roadmap
   - References to all relevant source skill files
   - Instructions to implement changes in priority order (critical → serious → moderate → minor)

2. **Present** implementation results to the user

### 6. Shutdown

After all phases complete:
1. Send `shutdown_request` to all active agents
2. Call `TeamDelete` to clean up

---

## Agent Reference

| Agent | Lens | Weight | Template | Source Knowledge |
|-------|------|--------|----------|-----------------|
| craft-philosophy | Principled reasoning, tiebreaker | 1.5 | `modules/agents/craft-philosophy.md` | `~/.claude/skills/design-craft/modules/craft-philosophy.md`, `~/.claude/skills/craft-philosophy/SKILL.md` |
| foundations | Color, type, spacing, tokens | 1.0 | `modules/agents/foundations.md` | `~/.claude/skills/design-craft/modules/foundations.md` |
| interaction | Forms, gestures, physics, a11y | 1.0 | `modules/agents/interaction.md` | `~/.claude/skills/design-craft/modules/interaction.md` |
| composition | Layout, IA, content strategy | 1.0 | `modules/agents/composition.md` | `~/.claude/skills/design-craft/modules/composition.md` |
| critique | 5-step systematic evaluation | 1.2 | `modules/agents/critique.md` | `~/.claude/skills/design-craft/modules/critique.md` |
| interface-craft | Animation DSL, polish | 0.8 | `modules/agents/interface-craft.md` | `~/.agents/skills/interface-craft/SKILL.md` |
| baseline-ui | Constraint enforcement | 1.0 | `modules/agents/baseline-ui.md` | `~/.agents/skills/baseline-ui/SKILL.md` |
| frontend-design | Visual aesthetics | 1.0 | `modules/agents/frontend-design.md` | `~/Documents/samos/.claude/skills/frontend-design/SKILL.md` |
| accessibility | WCAG, ARIA, focus | 1.2 | `modules/agents/accessibility.md` | `~/.agents/skills/fixing-accessibility/SKILL.md`, `~/.claude/skills/design-craft/modules/interaction.md` |
| motion | Motion principles + performance | 0.8 | `modules/agents/motion.md` | `~/.local/share/claude-tools/design-motion-principles/skills/design-motion-principles/SKILL.md`, `~/.agents/skills/fixing-motion-performance/SKILL.md` |

---

## Usage Examples

```
/design-swarm audit ~/Desktop/screenshot.png
/design-swarm audit ideate https://example.com
/design-swarm audit ideate implement ./src/components/Settings.tsx
/design-swarm ideate "dark mode settings panel with toggle, theme preview, and schedule"
/design-swarm audit implement ./src/pages/Dashboard.tsx
```

---

## Important Notes

- **Runtime knowledge loading**: Agent templates are compact. Source knowledge is read fresh from canonical skill files at spawn time. This prevents knowledge duplication and ensures agents always use the latest skill content.
- **Parallel execution**: All agents within a phase run simultaneously. Phases run sequentially when chained.
- **Conflict resolution**: The craft-philosophy agent (weight 1.5) serves as tiebreaker. See orchestrator.md for the full protocol.
- **Partial modes**: Any combination of modes is valid. `ideate` without prior `audit` uses the target directly (no findings context). `implement` without `ideate` applies audit findings directly.
