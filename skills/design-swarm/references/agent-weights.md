# Agent Weights — Design Swarm

Default weights for composite scoring. Override per-invocation if needed.

| Agent | Weight | Rationale |
|-------|--------|-----------|
| craft-philosophy | 1.5 | Meta-quality lens, tiebreaker for conflicts |
| critique | 1.2 | Holistic user-centric evaluation |
| accessibility | 1.2 | Non-negotiable compliance requirement |
| foundations | 1.0 | Core visual language |
| interaction | 1.0 | Core usability |
| composition | 1.0 | Core structure |
| baseline-ui | 1.0 | Core constraint enforcement |
| frontend-design | 1.0 | Core visual aesthetics |
| interface-craft | 0.8 | Polish layer, context-dependent |
| motion | 0.8 | Polish layer, context-dependent |

## Composite score formula

```
composite = sum(score_i * weight_i) / sum(weight_i)
```

Where `weight_i` only includes agents that participated in the audit (some may be skipped based on target type).

## When to adjust weights

- **Code-only target** (no visual): increase `baseline-ui` to 1.2, decrease `frontend-design` to 0.8
- **Screenshot-only** (no code): set `baseline-ui` to 0, increase `frontend-design` to 1.2
- **Animation-heavy target**: increase `motion` and `interface-craft` to 1.2
- **Form-heavy target**: increase `interaction` to 1.2, increase `accessibility` to 1.5
