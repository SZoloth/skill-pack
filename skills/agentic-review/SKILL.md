---
name: agentic-review
description: Deep multi-agent code review for local changes. Inspired by AmpCode's agentic review. Use when you want comprehensive analysis of staged changes, unstaged changes, specific commits, or branch differences. Spawns parallel specialized agents (security, performance, patterns, architecture) and synthesizes actionable findings with priority levels. Can implement fixes directly.
---

# Agentic Code Review

Deep, multi-agent code review for personal repos. Analyzes changes, prioritizes files, spawns specialized review agents in parallel, and synthesizes actionable findings.

## When to Use

- After writing significant code, before committing
- Before pushing a branch
- Reviewing specific commits
- Comparing branches (e.g., feature vs main)

## Usage

```bash
# Review all local changes (staged + unstaged)
skill agentic-review

# Review only staged changes
skill agentic-review --staged

# Review specific commit(s)
skill agentic-review abc123
skill agentic-review abc123..def456

# Review branch diff against main
skill agentic-review --branch feature-branch
skill agentic-review --branch HEAD  # current branch vs main
```

## How It Works

### Phase 1: Detect & Gather Changes

First, determine what to review based on arguments:

```bash
# Check if we're in a git repo
git rev-parse --is-inside-work-tree 2>/dev/null || echo "NOT_GIT_REPO"

# Get current branch
git branch --show-current

# Check for changes
git status --porcelain        # Any local changes?
git diff --stat               # Unstaged changes stats
git diff --cached --stat      # Staged changes stats
```

**Determine review scope from $ARGUMENTS:**

| Argument | What to Review |
|----------|----------------|
| (empty) | All local changes (staged + unstaged) |
| `--staged` | Only staged changes |
| `abc123` | Specific commit |
| `abc123..def456` | Commit range |
| `--branch NAME` | Branch diff vs main/master |
| `--branch HEAD` | Current branch vs main/master |

### Phase 2: Pre-Scan & Prioritize Files

Analyze changed files to recommend review order:

```bash
# Get changed files with stats
git diff --numstat HEAD~1 2>/dev/null || git diff --numstat

# For each file, assess:
# - Lines changed (more = higher priority)
# - File type (src > test > config > docs)
# - Risk areas (auth, security, data, API)
```

**Prioritization Heuristics:**
1. **HIGH**: Security-related files (auth, crypto, permissions, secrets)
2. **HIGH**: Core business logic (models, services, controllers)
3. **MEDIUM**: API endpoints, data access
4. **MEDIUM**: Configuration files that affect runtime
5. **LOW**: Tests, documentation, styling

Output file review order before proceeding.

### Phase 3: Language Detection & Agent Selection

Detect languages from file extensions:

```bash
# Get unique file extensions from changes
git diff --name-only | sed 's/.*\.//' | sort -u
```

**Agent Selection Matrix:**

| Language | Agents to Spawn |
|----------|-----------------|
| TypeScript/JavaScript | kieran-typescript-reviewer, security-sentinel, performance-oracle |
| Python | kieran-python-reviewer, security-sentinel, performance-oracle |
| Ruby/Rails | kieran-rails-reviewer, dhh-rails-reviewer, security-sentinel |
| Any | pattern-recognition-specialist, architecture-strategist, code-simplicity-reviewer |

Always include:
- **security-sentinel**: Security vulnerabilities, secrets, OWASP
- **performance-oracle**: Performance issues, N+1, memory
- **pattern-recognition-specialist**: Anti-patterns, duplication
- **code-simplicity-reviewer**: Over-engineering, unnecessary complexity

### Phase 4: Parallel Agent Analysis

**CRITICAL: Launch agents in parallel using Task tool**

Spawn 4-6 agents simultaneously, each with:
1. The full diff (or relevant portions for large changes)
2. Context about file purposes
3. Instruction to return structured findings

```
Task security-sentinel("Review this diff for security issues: [diff]")
Task performance-oracle("Review for performance problems: [diff]")
Task pattern-recognition-specialist("Check for anti-patterns: [diff]")
Task code-simplicity-reviewer("Check for over-engineering: [diff]")
Task [language-specific]("Review for [language] best practices: [diff]")
```

Each agent returns findings in this format:
```
## Findings

### [SEVERITY] Issue Title
- **File**: path/to/file.ts:42
- **Issue**: Description of the problem
- **Impact**: Why this matters
- **Fix**: Suggested remediation
```

### Phase 5: Synthesize & Prioritize

Combine all agent findings and deduplicate:

**Priority Levels:**
- **P1 - CRITICAL** (blocks commit): Security vulnerabilities, data corruption risks, obvious bugs
- **P2 - IMPORTANT** (should fix): Performance issues, architectural concerns, significant code smells
- **P3 - SUGGESTED** (nice to have): Minor improvements, style issues, refactoring opportunities

**Deduplication Rules:**
- Same file + same line = keep most severe
- Overlapping concerns = merge into single finding
- Subjective "might be" issues = demote to P3 or drop

### Phase 6: Present Findings

Output structured summary:

```
══════════════════════════════════════════════════════════════
AGENTIC CODE REVIEW
══════════════════════════════════════════════════════════════

📊 SUMMARY
────────────────────────────────────────────────────────────────
Files reviewed: 8
Total findings: 12
  🔴 P1 Critical: 2 (MUST FIX)
  🟡 P2 Important: 5
  🔵 P3 Suggested: 5

Agents used: security-sentinel, kieran-typescript-reviewer,
             performance-oracle, pattern-recognition-specialist

📁 FILES BY PRIORITY
────────────────────────────────────────────────────────────────
1. src/auth/login.ts        [🔴 P1] Security issue
2. src/api/users.ts         [🟡 P2] Performance concern
3. src/utils/helpers.ts     [🔵 P3] Code smell
...

🔴 P1 - CRITICAL (Must fix before commit)
────────────────────────────────────────────────────────────────
1. SQL Injection Risk
   📍 src/api/users.ts:47
   ⚠️  Raw SQL with string interpolation
   💡 Use parameterized query instead

2. Hardcoded Secret
   📍 src/config/auth.ts:12
   ⚠️  API key directly in source
   💡 Move to environment variable

🟡 P2 - IMPORTANT (Should fix)
────────────────────────────────────────────────────────────────
[Similar format...]

🔵 P3 - SUGGESTED (Nice to have)
────────────────────────────────────────────────────────────────
[Similar format...]

══════════════════════════════════════════════════════════════
```

### Phase 7: Offer to Fix

After presenting findings, offer options:

```
NEXT STEPS
────────────────────────────────────────────────────────────────
1. Fix all P1 issues automatically
2. Fix all P1 + P2 issues automatically
3. Fix specific issue by number
4. Show detailed analysis for an issue
5. Exit (I'll fix manually)

Choice [1-5]:
```

If user chooses to fix:
1. Work through issues one at a time
2. Show the fix being applied
3. Ask for confirmation before moving to next
4. Re-run review on fixed files to verify

## Configuration

**Default Agents** (always run):
- security-sentinel
- performance-oracle
- pattern-recognition-specialist
- code-simplicity-reviewer

**Language-Specific Agents** (auto-detected):
- TypeScript/JS: kieran-typescript-reviewer
- Python: kieran-python-reviewer
- Ruby: kieran-rails-reviewer, dhh-rails-reviewer

**Optional Agents** (if relevant files detected):
- architecture-strategist (for structural changes)
- data-integrity-guardian (for DB/migration changes)

## Examples

### Example 1: Quick pre-commit review

```
> skill agentic-review --staged

Analyzing staged changes...
Files: 3 changed (src/api/users.ts, src/models/User.ts, tests/users.test.ts)
Lines: +45, -12

Spawning review agents in parallel...
✓ security-sentinel
✓ kieran-typescript-reviewer
✓ performance-oracle
✓ pattern-recognition-specialist

Results:
🔴 P1: 0
🟡 P2: 1 (missing input validation)
🔵 P3: 2 (minor suggestions)

Ready to commit! One P2 to consider:
→ src/api/users.ts:34 - Add validation for email parameter

Fix now? [y/N]
```

### Example 2: Branch review before PR

```
> skill agentic-review --branch feature/auth-refactor

Comparing feature/auth-refactor to main...
Files: 12 changed
Lines: +342, -156

[Full analysis with all agents...]

Review complete. See findings above.
Would you like to fix any issues before creating PR?
```

## Important Notes

- This skill is for **personal repos** - no GitHub/PR integration required
- Focus is on **actionable feedback** - subjective concerns are deprioritized
- Agents run in **parallel** for speed
- Findings are **deduplicated** to avoid noise
- User has **control over fixes** - nothing changes without confirmation
