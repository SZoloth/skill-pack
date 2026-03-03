# Orchestrator — Design Swarm

Instructions for the team lead (main session) on synthesizing agent reports, resolving conflicts, and producing the final output.

---

## Phase Gating

Which agents participate in which modes:

### Audit Phase
All 10 agents run in parallel, with these exceptions:
- **Screenshot-only target** (no code): Skip `baseline-ui` (can't evaluate code constraints)
- **Code-only target** (no visual): Skip `frontend-design` visual assessment; focus on code-level agents
- **Text brief target**: All agents provide theoretical assessment based on the described interface

### Ideate Phase
All 10 agents receive the synthesized audit findings and propose improvements through their lens.

### Implement Phase
A single `general-purpose` agent receives:
- The synthesized audit report
- The prioritized improvement roadmap from ideation
- References to all relevant source skill files for deep knowledge access

---

## Synthesis Algorithm

After collecting all agent reports via SendMessage:

### 1. Collect
Gather all reports. Verify each contains: assessment, score, issues list, top 3 opportunities.

### 2. Deduplicate
Same element flagged by multiple agents → merge into single finding:
- Keep the most specific description
- Combine severity ratings (take the highest)
- Note which lenses flagged it (validates importance)
- Example: "Low contrast text" flagged by both `foundations` and `accessibility` → single finding citing both

### 3. Aggregate by Severity
Group all unique findings:
1. **Critical** — Blocks users or violates compliance. Fix immediately.
2. **Serious** — Significant quality gap. Fix before shipping.
3. **Moderate** — Noticeable but not blocking. Fix in next iteration.
4. **Minor** — Polish items. Fix when time allows.

### 4. Compute Weighted Composite Score
```
composite = sum(score_i * weight_i) / sum(weight_i)
```

Using weights from `references/agent-weights.md`. Only include agents that participated (skip those excluded by phase gating).

### 5. Identify Conflicts
Scan for opposing recommendations on the same element:
- Agent A says "add animation" but Agent B says "reduce motion"
- Agent A says "increase density" but Agent B says "add whitespace"

### 6. Resolve Conflicts
Resolution priority:
1. **Check craft-philosophy agent's position** on the contested element
2. **If craft-philosophy has a direct position**: follow it (weight 1.5 = tiebreaker)
3. **If no direct position**: apply Six Principles fallback:
   - "Less but better" → simpler option wins
   - "Know the bar" → option closer to industry standard wins
   - "Uncommon care" → option that serves overlooked users wins
4. **Document** the conflict + resolution in final report for user override

---

## Final Report Template

```markdown
# Design Swarm Report

**Target**: [description/path/URL]
**Modes**: [audit | ideate | implement]
**Date**: [timestamp]

---

## Composite Score: [X.X]/10

| Agent | Score | Weight | Weighted |
|-------|-------|--------|----------|
| craft-philosophy | X | 1.5 | X.X |
| critique | X | 1.2 | X.X |
| accessibility | X | 1.2 | X.X |
| foundations | X | 1.0 | X.X |
| interaction | X | 1.0 | X.X |
| composition | X | 1.0 | X.X |
| baseline-ui | X | 1.0 | X.X |
| frontend-design | X | 1.0 | X.X |
| interface-craft | X | 0.8 | X.X |
| motion | X | 0.8 | X.X |
| **Composite** | | | **X.X** |

---

## Critical Findings
[Findings requiring immediate attention — blocks users or violates compliance]

## Serious Findings
[Significant quality gaps — fix before shipping]

## Moderate Findings
[Noticeable gaps — fix in next iteration]

## Minor Findings
[Polish items — fix when time allows]

---

## Cross-Agent Insights
[Patterns that emerged from multiple agents flagging the same area]

## Conflicts Resolved
| Element | Agent A | Agent B | Resolution | Rationale |
|---------|---------|---------|------------|-----------|
| ...     | ...     | ...     | ...        | ...       |

---

## Improvement Roadmap (Ideate mode)

### High Impact / Low Effort
[Quick wins from ideation]

### High Impact / High Effort
[Strategic investments]

### Low Impact / Low Effort
[Nice-to-haves]

---

## Implementation Plan (Implement mode)

### Phase 1: Critical fixes
[Ordered list with specific code changes]

### Phase 2: Quality improvements
[Ordered list]

### Phase 3: Polish
[Ordered list]
```

---

## Agent Naming Convention

When spawning agents via Task tool, use these names for `team_name: "design-swarm"`:
- `craft-philosophy`
- `foundations`
- `interaction`
- `composition`
- `critique`
- `interface-craft`
- `baseline-ui`
- `frontend-design`
- `accessibility`
- `motion`
