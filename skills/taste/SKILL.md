---
name: taste
description: "Domain-grounded judgment for creation: code, visuals, documents, design, data artifacts, and other output where quality depends on choosing what not to include. Use this skill when the user wants something to feel authored rather than generated, when the brief says a draft feels off, when polish alone is insufficient, or when the deliverable is externally visible and the quality bar is shaped by practitioner norms. Triggers on requests like 'use taste', 'apply taste', 'make this good', 'tighten this', 'clean this up', 'less is more', 'this feels generated', or any creation task where judgment matters more than coverage. Do not use for exhaustive extraction, rote transformation, or work where comprehensiveness is the explicit goal."
metadata:
  author: Hunter Bown
  version: "0.1.0"
  style: instruction-only
  compatibility: Agent Skills compatible clients including Claude Code and OpenAI Codex-style agents
---

# Taste

Taste is domain-grounded judgment applied during creation. It is not a polish pass and not a slop-removal checklist. It is the difference between output that is merely competent and output that reflects an actual understanding of what practitioners in a field consider good.

The failure mode this skill corrects is straightforward: agents produce work that is technically complete but obviously generated. The information may be correct, but the judgment is absent. Equal weight goes to unequal ideas. Structure appears because templates exist, not because the content needs it. Details stay in because they were available, not because they matter.

This skill fixes that by requiring you to understand the domain before you touch the work.

## Grounding Requirement

Before creating anything, establish a domain model. The depth required depends on how far the domain is from your reliable knowledge.

If you know the domain cold:
- State the assumptions behind the quality bar and move.
- Do not perform research theater.

If you know the domain but not the current state:
- Research first.
- Look at current exemplars, docs, or conventions.
- A grounded first draft is better than an ungrounded third draft.

If the domain is unfamiliar or specialized:
- Stop and learn before creating.
- Read exemplars from practitioners, not tutorials about the domain.
- Understand what people in the field care about and what they treat as background.

The grounding step should produce three things:
1. The quality bar: what good looks like here, with specific reference points.
2. The assumed knowledge: what the audience already knows and will not tolerate having explained back to them.
3. The attention budget: where practitioners spend care and where they do not.

Skip grounding only when the domain is obvious and you are certain. When uncertain, ground.

## Core Principles

### 1. Exclusion is the primary act of taste

The first judgment is what to leave out. This is only possible if you understand the function of the material you're cutting. Taste without domain knowledge is just confidence.

### 2. Every element must earn its place

Nothing is included by default. Not a section header, not an abstraction, not a paragraph, not a color choice. Each element must answer: what does this do for the person encountering the work?

The exception is when the convention itself carries meaning. Standard README sections, established visual hierarchy, or a familiar API shape can earn their place because the audience relies on them.

### 3. Commit to the spine

Every created artifact has a controlling idea: the real job of a function, the actual argument of a document, the primary action of a design. Find it and orient everything around it.

If you cannot identify the spine, the work is not ready to be built.

### 4. Specificity over sophistication

A concrete detail grounded in the domain beats an elegant abstraction. Name the real thing. Use the actual number. Reference the specific constraint. Sophistication without anchoring is decoration.

### 5. Match the register, do not perform it

Each domain has a native way of communicating. API docs, pitch decks, commit messages, design critiques, and internal memos do not sound alike. The right register is about signal versus noise, not about formality.

### 6. Stop at the right level of finish

Overfinishing is a taste failure. Past the point where extra work improves the receiver's experience, you are serving your own anxiety rather than the work.

## Workflow

### 1. Ground

Determine what good means in this exact context. For common creation domains, load the relevant section from `references/DOMAIN_MODES.md`, but treat it as a starting point rather than a substitute for actual domain knowledge.

### 2. Frame

Before creating, establish:
- What is the single job of this work?
- Who encounters it, and what do they already know?
- What level of finish fits the stakes?
- What are the domain-specific signals of quality here?

If you cannot answer those questions, research or ask before creating.

### 3. Create with the filter on

Build from the spine outward. Apply the domain model at every decision point: inclusion, structure, level of detail, and register.

This is not create first and edit later. The filter is active during creation.

### 4. Subtract

After the first pass, remove:
- Anything included because it was available rather than useful.
- Structure imposed by habit rather than need.
- Detail the actual audience would consider obvious.
- Hedging that substitutes for judgment.
- Sections that exist only because they are conventional.

### 5. Verify the spine

Read the work back. Is the controlling idea clear? Does every element support it or get out of its way? If the spine is muddy, something is competing.

### 6. Calibrate finish

Check the result against the quality bar from grounding. Do not ask whether it is maximally polished. Ask whether it is finished at the right level for the context.

## What This Skill Does Not Do

- It does not apply a universal checklist for good writing, code, or design.
- It does not reduce taste to removing AI-isms.
- It does not default to minimalism.
- It does not replace research.

## Reference Files

Load only what the task needs.

- `references/DOMAIN_MODES.md`: Creation-specific guidance for code, visual design, documents, data artifacts, and system design.
- `references/ANTI_PATTERNS.md`: Common ways agents fake taste during creation.
- `references/EVALUATION.md`: Rubric for high-bar self-checks.
