---
name: iterate
description: "Diverge-converge workflow: spin up N agents on isolated worktrees, each exploring a different approach to the same problem. Compare results side-by-side (with optional preview ports), pick a winner, merge it back. Use when the user says /iterate, 'try multiple approaches', 'explore different directions', or wants to compare implementations."
---

# /iterate — Diverge, Compare, Converge

Spin up N parallel agents on isolated git worktrees, each exploring a different direction for the same problem. Review all variants, pick the best, merge it back.

## When to Use

- User wants to explore multiple approaches to a problem
- Design/UI work where visual comparison matters
- Refactoring where there are multiple valid strategies
- Any task where "try a few things and pick the best" is the right move

## Prerequisites

- Must be inside a git repository with a clean working tree (or stashable changes)
- The repo should have committed state to branch from

## Usage

```
/iterate <task description>
/iterate 3 <task description>        # explicit count (default: 3)
/iterate 2 redesign the settings page using shadcn
```

## How It Works

### Phase 1: Parse Arguments

Parse `$ARGUMENTS` to extract:
- **N** (number of variants): First token if it's a number 1-5, otherwise default to **3**
- **Task**: Everything after the count (or all of $ARGUMENTS if no count)

If no arguments provided, ask the user:
1. What task/problem should the agents explore?
2. How many variants? (default 3)

### Phase 2: Generate Divergent Angles

Before spawning agents, generate N distinct approaches. These should be meaningfully different, not just cosmetic variations.

**Example for "redesign the settings page":**
1. Minimal — strip to essentials, single scrollable page, maximum whitespace
2. Tabbed — group settings into logical tabs with a sidebar nav
3. Search-first — spotlight-style search with categorized results

**Example for "implement caching":**
1. In-memory LRU cache with TTL
2. Redis-backed with cache invalidation events
3. Service worker + Cache API for edge caching

Present the angles to the user for approval before spawning. The user may adjust, add, or remove angles.

### Phase 3: Spawn Agents

For each variant, spawn an agent using:

```
Agent tool with:
  - isolation: "worktree"
  - subagent_type: "general-purpose"
  - run_in_background: true (to run all N in parallel)
```

Each agent gets this prompt structure:

```
You are variant {i} of {N} exploring: {task}

YOUR ANGLE: {angle_description}

INSTRUCTIONS:
1. Implement the solution following the angle described above
2. Make it complete and functional — this will be compared against other approaches
3. If this is a web project with a dev server, start it on port {base_port + i}
   (use the project's dev command with --port flag)
4. When done, write a brief VARIANT_NOTES.md at the repo root with:
   - Approach taken
   - Key decisions and tradeoffs
   - What you'd improve with more time
5. Commit all changes with message: "iterate: variant {i} — {angle_name}"
```

**Port assignment**: Base port = 3100, so variants get 3101, 3102, 3103, etc.

### Phase 4: Monitor & Report

Wait for all agents to complete. As each finishes, collect:
- Worktree path and branch name (from agent result)
- Whether a dev server is running and on which port
- Summary from VARIANT_NOTES.md if it exists

Present a comparison table:

```
┌─────────┬──────────────────────┬──────────┬─────────────────────────┐
│ Variant │ Angle                │ Port     │ Summary                 │
├─────────┼──────────────────────┼──────────┼─────────────────────────┤
│ 1       │ Minimal              │ :3101    │ Single page, 3 sections │
│ 2       │ Tabbed               │ :3102    │ 4 tabs, sidebar nav     │
│ 3       │ Search-first         │ :3103    │ Spotlight + categories  │
└─────────┴──────────────────────┴──────────┴─────────────────────────┘
```

Tell the user to review each variant (visit the ports, check the code).

### Phase 5: Converge

Ask the user: **"Which variant do you want to keep?"**

Options:
- Pick a single winner (1, 2, or 3)
- Cherry-pick pieces from multiple variants
- None — discard all and try again with different angles

**If single winner:**
1. Merge the winning branch into the current branch
2. Remove VARIANT_NOTES.md from the merge
3. Clean up all worktrees (`git worktree remove`)
4. Kill any running dev servers on the variant ports

**If cherry-pick:**
1. Ask which parts from which variants
2. Apply changes manually from the relevant worktrees
3. Clean up all worktrees

**If none:**
1. Clean up all worktrees
2. Offer to re-run with new angles

### Cleanup

Always clean up worktrees when done, even if the user abandons the process:

```bash
# List worktrees
git worktree list

# Remove each variant worktree
git worktree remove <path> --force

# Delete variant branches
git branch -D iterate-variant-1 iterate-variant-2 iterate-variant-3

# Kill dev servers on variant ports
lsof -ti:3101 -ti:3102 -ti:3103 | xargs kill 2>/dev/null
```

## Important Notes

- **Git safety**: Never force-push or modify the user's current branch until they explicitly pick a winner
- **Port conflicts**: Check if ports are in use before assigning; shift base port if needed
- **Large repos**: Worktrees share the git object store, so disk usage is minimal
- **Context isolation**: Each agent starts fresh — no context pollution between variants
- **Dev server detection**: Look for `package.json` scripts (dev, start, serve) or common framework commands to determine if/how to start a preview server
