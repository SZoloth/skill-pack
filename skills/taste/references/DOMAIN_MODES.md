# Domain Modes

These are starting points for common creation domains. They describe where practitioners focus their attention — but they are not a substitute for grounding in the specific context. A React component for a fintech dashboard and a React component for a children's game are both "code," but the taste decisions are completely different.

Read the section that matches your current task. If the task spans multiple domains (e.g., a data visualization is both code and visual design), read both and let the more specific context win conflicts.

---

## Code

**Where practitioners spend their attention:**
- Naming — the difference between code that communicates and code that requires decipherment
- Interface boundaries — what's exposed, what's hidden, and whether the API makes the common case easy
- Error paths — not just handling errors but making failures legible and recoverable
- The dependency you chose NOT to add

**Where they don't:**
- Verbose docstrings on self-explanatory functions
- Abstracting things used once
- Configuring things that have one correct value in this context
- Commenting what the code already says

**Grounding protocol:** Before writing code in an unfamiliar codebase, framework, or domain, read the existing code first. Not docs about the code — the code itself. Match its patterns, naming conventions, abstraction level, and error handling style. If you're building something new, look at well-regarded projects in the same space and understand what they chose to care about.

**The taste test:** A developer unfamiliar with this code opens the file. Can they understand the intent without reading any comments? Can they modify the right thing without understanding the whole system? If yes, the taste is working.

**Common creation failures:**
- Generating a "complete" module with interfaces, factories, and dependency injection for something that should be 40 lines
- Writing tests that test the framework instead of the behavior
- Naming things after their type or implementation (`userDataObject`, `processHandler`) instead of their purpose
- Adding configuration for things that are not actually configurable in this context

---

## Visual Design & UI

**Where practitioners spend their attention:**
- Hierarchy — the eye moves where you decide it moves
- The relationship between elements, not individual elements in isolation
- Whitespace as structure, not leftover space
- Whether the design serves the user's task or the designer's portfolio

**Where they don't:**
- Symmetry for its own sake
- Pixel-perfect details in areas the user will never focus on
- Trend-following without understanding why the trend exists
- "Interesting" choices that don't serve the interaction

**Grounding protocol:** Before designing, look at the best work in the specific category — not "good design" generically, but the best version of this specific kind of thing. A settings page, a checkout flow, a data dashboard, a marketing landing page — each has different exemplars. Understand what those exemplars prioritize and what they chose to leave plain.

**The taste test:** Squint until the page blurs. Can you still see what's most important? If everything looks the same importance when blurred, the hierarchy isn't working. Now un-squint: is there anything decorative that doesn't serve navigation, comprehension, or emotional tone? Cut it.

**Common creation failures:**
- Applying a "design system" without understanding which constraints matter here and which don't
- Making every state and edge case equally prominent in the UI
- Using animations because they're available rather than because they communicate state change
- Choosing fonts, colors, or layouts because they're trendy rather than because they fit the context
- Defaulting to card-based layouts when the content doesn't have card-shaped information

---

## Documents & Writing

**Where practitioners spend their attention:**
- The first sentence — does it earn the second?
- Proportion — are ideas given space proportional to their importance, not their complexity?
- The single thing the reader should take away
- Whether the structure helps the reader navigate or forces them through a linear march

**Where they don't:**
- Introductions that set up what the document will cover (the document will cover it — just start)
- Conclusions that restate what was just said
- Transitions between sections (if the sections are well-structured, the transitions are obvious)
- Balanced coverage when the evidence is imbalanced

**Grounding protocol:** Identify the genre. A product spec, a technical RFC, a board memo, a blog post, and a research report all have different reader expectations, different pacing, and different signals of quality. Read a strong example of the specific genre if you're not certain of its conventions. Pay attention to what it omits as much as what it includes.

**The taste test:** Read just the first sentence of each section. Does the reader know what the document argues and whether they need to read further? Now read the whole thing: is there a paragraph that could be deleted without the reader noticing? If yes, delete it.

**Common creation failures:**
- Opening with "In today's rapidly evolving..." or any variant of contextualizing the obvious
- Structuring every document with Background → Analysis → Recommendations regardless of whether that structure serves this content
- Giving three pros and three cons when there are really two decisive pros and one relevant con
- Writing for completeness instead of for the reader's actual decision
- Using headers as labels ("Introduction", "Background") instead of as arguments ("The core problem is latency, not throughput")

---

## Data Artifacts & Visualization

**Where practitioners spend their attention:**
- What comparison the viewer should make — and whether the visual structure enables exactly that comparison
- Axis choices, scales, and labels that don't mislead or require mental gymnastics
- The one finding that actually matters, made visually dominant
- Annotation — the few data points that need explanation, called out precisely

**Where they don't:**
- Chart types chosen because they look sophisticated
- Decorative elements that don't encode data
- Legends that could be replaced by direct labels
- Grid lines that don't help the reader make comparisons

**Grounding protocol:** Before building a data artifact, clarify the one question it should answer. Not "what does the data show" — "what decision does this support." Then look at how the best analysts in this domain present similar findings. Financial analysts, scientific researchers, product analysts, and journalists all visualize data differently because their audiences need different things.

**The taste test:** Cover everything except the data and its labels. Can someone still get the main point? If not, the visual isn't doing its job — the labels, titles, or annotations are carrying too much weight. Now uncover everything: is there any element that doesn't help the viewer understand the data? Remove it.

**Common creation failures:**
- Showing all the data when only one segment matters
- Using a complex chart type when a table would be clearer
- Dual-axis charts that technically work but require the viewer to mentally juggle two scales
- Choosing color palettes based on aesthetics rather than perceptual distinguishability
- Dashboard layouts that show many metrics with equal prominence when one metric is the one that matters

---

## System Design & Architecture

**Where practitioners spend their attention:**
- Boundaries — where systems meet, what crosses the boundary, and what the contract is
- Failure modes — not how the system works, but how it breaks and what happens when it does
- The constraints that actually shaped the decision, not the textbook tradeoffs
- Migration path — how do you get from here to there without stopping the world

**Where they don't:**
- Diagrams that show every component when the decision only involves three
- Theoretical scalability analysis for systems that won't hit scale in the relevant timeframe
- Technology choices justified by feature matrices instead of by the specific requirements
- "We could also..." alternatives that were never seriously considered

**Grounding protocol:** Before proposing architecture, understand the actual constraints — not generic constraints, but this system's. Traffic patterns, team size, existing infrastructure, deployment constraints, acceptable downtime, data sensitivity. The architecture that's "right" for a three-person startup is wrong for a regulated enterprise, and vice versa. Look at how similar systems at similar scale were actually built, not how they'd be built in a textbook.

**The taste test:** Describe the architecture in three sentences. If you can't, it's either too complex for the problem or you don't understand it well enough. Now check: does every component exist because of a requirement, or does one exist because "you should have one"? Kill the latter.

**Common creation failures:**
- Microservices for a system that could be a monolith
- Event-driven architecture because it's modern, not because the problem is event-shaped
- Layers of abstraction "in case we need to swap X later" when swapping X would require rewriting everything anyway
- Architecture diagrams that are technically accurate but don't highlight the decisions that actually matter
- Choosing technologies based on what's impressive rather than what fits
