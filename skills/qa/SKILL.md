---
name: qa
description: Full QA on all session changes using Codex as a second pair of eyes. Use when user says "QA", "full QA", "QA my changes", "QA all your changes", or "use codex to review". Runs git diff, sends changes to Codex for thorough review, and synthesizes findings.
---

# QA — Codex-Powered Change Review

Thorough QA on all changes in the current working tree using Codex as a second pair of eyes. Detects what changed, sends it to Codex for deep review, and presents actionable findings.

## When to Use

- After completing implementation work, before committing
- User says "QA", "full QA", "QA my changes", "review with codex"
- Any time you want a second opinion on everything you just built

## Process

### Step 1: Gather All Changes

```bash
# Get the full picture
git status --porcelain
git diff --stat
git diff --cached --stat

# Capture the actual diff (staged + unstaged)
git diff HEAD > /tmp/qa-full-diff.patch
```

If changes span many files, also get a summary:
```bash
git diff HEAD --name-only
```

### Step 2: Build Context

Before sending to Codex, gather context about what was being built:
- What task/feature was being implemented?
- What files were touched and why?
- Any known areas of concern?

Summarize this in 2-3 sentences to give Codex context.

### Step 3: Codex Review (diff-based)

Run Codex with the full diff and context. Use `-C` to point at the repo so Codex can read surrounding code.

```bash
cat <<'PROMPT' | codex exec --full-auto -C "$(git rev-parse --show-toplevel)" -o /tmp/qa-codex-review.md -
You are doing a thorough QA review of the following changes. Be critical — find real bugs, not style nits.

## Context
[2-3 sentence summary of what was built]

## Your Review Checklist
1. **Correctness**: Logic errors, off-by-ones, wrong conditions, missing edge cases
2. **Runtime errors**: Null/undefined access, type mismatches, missing imports
3. **Security**: Injection, XSS, secrets in code, unsafe operations
4. **Data integrity**: Race conditions, stale state, missing validation
5. **Integration**: Does this work with the rest of the codebase? Missing wiring?
6. **Regressions**: Could these changes break existing functionality?
7. **Missing pieces**: Anything obviously incomplete or TODO'd?

## Changes to Review
$(git diff HEAD)

For each issue found, report:
- **File:line** — exact location
- **Severity** — P1 (bug/security), P2 (likely problem), P3 (minor concern)
- **Issue** — what's wrong
- **Fix** — how to fix it

If the changes look solid, say so — don't manufacture issues.
PROMPT
```

**Important**: Set a timeout (3-5 minutes). Codex can run long on large diffs.

### Step 4: Codex Audit (file-based)

**This is a separate pass.** Instead of reviewing the diff, Codex reads the actual changed files in the repo and audits them holistically. This catches issues the diff-based review misses — things that look fine in isolation but break in context.

Run this in parallel with Step 3 when possible (use Bash tool's `run_in_background`).

```bash
# Build the file list from changes
CHANGED_FILES=$(git diff HEAD --name-only | tr '\n' ' ')

cat <<PROMPT | codex exec --full-auto -C "$(git rev-parse --show-toplevel)" -o /tmp/qa-codex-audit.md -
You are auditing recently modified files for quality and correctness. Read each file fully — don't just look at diffs.

## Context
[2-3 sentence summary of what was built]

## Files to Audit
${CHANGED_FILES}

## Your Audit Checklist
1. **Read each file end-to-end** — does it make sense as a whole?
2. **Imports & exports**: Are all imports used? Are exports consumed correctly elsewhere?
3. **Type safety**: Any implicit `any`, unsafe casts, or missing generics?
4. **Error handling**: Are errors caught and handled appropriately? Any swallowed errors?
5. **Edge cases**: Empty arrays, null inputs, boundary values, concurrent access
6. **Naming & contracts**: Do function signatures match their implementations? Misleading names?
7. **Test coverage**: If tests exist, do they actually test the changed behavior? Missing assertions?
8. **Build & run**: Would this build and run without errors? Missing deps, broken imports?

For each issue found, report:
- **File:line** — exact location
- **Severity** — P1 (bug/security), P2 (likely problem), P3 (minor concern)
- **Issue** — what's wrong
- **Fix** — how to fix it

If the files look solid, say so — don't manufacture issues.
PROMPT
```

### Step 5: Read and Synthesize Both Passes

```bash
cat /tmp/qa-codex-review.md
cat /tmp/qa-codex-audit.md
```

Merge findings from both passes. Deduplicate — if both passes flag the same issue, keep the more detailed one.

Present findings to the user in this format:

```
══════════════════════════════════════════════════════════════
QA REVIEW (via Codex)
══════════════════════════════════════════════════════════════

Files reviewed: N
Codex verdict: [PASS / ISSUES FOUND]

P1 — Bugs & Security
────────────────────────────────────────────────────────────────
[list or "None found"]

P2 — Likely Problems
────────────────────────────────────────────────────────────────
[list or "None found"]

P3 — Minor Concerns
────────────────────────────────────────────────────────────────
[list or "None found"]

══════════════════════════════════════════════════════════════
```

### Step 6: Offer to Fix

If P1 or P2 issues exist, offer to fix them:
- Fix P1s immediately (bugs and security)
- Offer to fix P2s with user confirmation
- Skip P3s unless user asks

After fixing, re-run the QA to verify the fixes.

## Rules

1. **Always use `--full-auto`** — non-interactive Codex execution
2. **Always use `-C`** — point Codex at the repo root for full context
3. **Always use `-o`** — capture output for structured parsing
4. **Set a timeout** — 300s max via Bash tool timeout parameter
5. **Don't auto-apply fixes** without presenting findings first
6. **Include the task context** — Codex reviews better with context about intent
7. **Be honest about clean results** — if Codex finds nothing, say so. Don't pad.

## Example

```
> /qa

Gathering changes...
  12 files changed, +342 lines, -89 lines

Sending to Codex for review...
  Context: "Implemented OAuth2 login flow with PKCE, added token refresh middleware"

[Codex reviewing... ~60s]

══════════════════════════════════════════════════════════════
QA REVIEW (via Codex)
══════════════════════════════════════════════════════════════

Files reviewed: 12
Codex verdict: ISSUES FOUND

P1 — Bugs & Security
────────────────────────────────────────────────────────────────
1. src/auth/token.ts:47 — Token stored in localStorage without encryption
   Fix: Use httpOnly cookie or encrypted session storage

P2 — Likely Problems
────────────────────────────────────────────────────────────────
1. src/middleware/refresh.ts:23 — Race condition if multiple requests
   trigger refresh simultaneously
   Fix: Add mutex/queue for token refresh

P3 — Minor Concerns
────────────────────────────────────────────────────────────────
None found

══════════════════════════════════════════════════════════════

Fix P1 issues now? [y/N]
```
