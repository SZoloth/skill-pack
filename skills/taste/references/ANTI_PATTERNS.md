# Anti-Patterns

Ways agents fake taste during creation. These are failure modes, not a checklist — recognizing the pattern doesn't mean you've avoided it. The fix is always domain grounding, not rule following.

---

## Savant Mode

Creating output that looks sophisticated to someone outside the domain but is immediately recognizable as ungrounded by someone inside it. The agent pattern-matched on surface features (vocabulary, structure, aesthetic) without understanding what those features are for.

**How to spot it:** The output uses domain vocabulary correctly but makes choices no practitioner would make. Like a function with perfect typing and JSDoc but an interface that makes the common case hard. Or a design with beautiful typography but a hierarchy that fights the user's task.

**The fix:** If you can't explain why a practitioner would make this specific choice, you don't understand the domain well enough. Ground first.

---

## Slop Removal as Taste

Treating taste as a cleanup pass — removing "Great question!", "It's worth noting", and other AI-isms — and calling the result tasteful. This is necessary but it's the floor, not the ceiling. Removing slop is not the same as having judgment.

**How to spot it:** The output is clean and professional but generic. It could be about anything in the domain. There's no evidence of specific understanding of *this* context, *this* audience, *this* purpose.

**The fix:** After removing slop, ask: "What would a practitioner do differently here based on the specific context?" If you can't answer that, you've cleaned up but you haven't exercised taste.

---

## Minimalism Cargo Cult

Equating taste with reduction — always shorter, always simpler, always fewer. This confuses a common symptom (AI output is verbose) with a universal rule (less is better). Sometimes the tasteful choice is rich, dense, detailed, or long — when the domain and context require it.

**How to spot it:** The output has been compressed past the point of usefulness. Important context is missing because "taste means cutting." A technical document lacks the detail a developer needs. A design is so minimal it doesn't communicate.

**The fix:** The question isn't "can I remove this?" but "does the audience need this to do their job?" Sometimes the answer is yes, and including it is the tasteful choice.

---

## Template Thinking

Imposing familiar structures because they've worked before, not because this content needs them. Every document gets an intro/body/conclusion. Every function gets a docstring. Every design gets a hero section. Every analysis gets background before findings.

**How to spot it:** The structure was decided before the content. You could swap the content for different content and the structure wouldn't change. The template is driving the work instead of the work driving the structure.

**The fix:** Let the content determine its own shape. A three-sentence email doesn't need headers. A single-purpose function doesn't need a docstring. A design for an expert tool doesn't need onboarding affordances.

---

## Comprehensiveness Theater

Including everything available because it looks thorough. This is the most common agent failure mode and the one taste is most specifically designed to correct. Being comprehensive is not the same as being good — it's often the opposite.

**How to spot it:** The output has no hierarchy. Everything is presented at the same level of emphasis. The reader can't tell what matters without reading all of it. The agent included information because it existed, not because it served the work.

**The fix:** Rank the elements. Give the top two or three most of the space. Compress or cut the rest. If you can't rank them, you don't understand the domain well enough.

---

## Defensive Hedging

Qualifying every statement, offering alternatives for every choice, and refusing to commit — not because the uncertainty is real, but because commitment feels risky. The output is technically not wrong about anything but also not useful about anything.

**How to spot it:** Count the number of "however", "on the other hand", "it depends", "there are tradeoffs" constructions. If you remove all of them, does the work improve? If yes, they were defensive, not informative.

**The fix:** Commit. State the choice, state the reason, state the one condition under which you'd choose differently. That's it. Uncertainty is fine — communicate it once, specifically, then move on.

---

## Aesthetic Frosting

Making something look polished without it being good underneath. Applied to code: clean formatting on a bad architecture. Applied to writing: smooth prose on a muddled argument. Applied to design: beautiful surfaces on a confusing interaction model.

**How to spot it:** The output looks great at first glance but falls apart under scrutiny. The structure doesn't hold. The logic doesn't track. The interaction doesn't flow. But it's pretty.

**The fix:** Get the spine right first. Structure, logic, hierarchy, interaction model — these are the load-bearing elements. Polish is the last step, not the first, and it only works if what's underneath is sound.

---

## Over-Refinement

Continuing to improve past the point where the improvement matters. The code works and is clear, but you're still refactoring. The document makes its point, but you're still tightening sentences. The design serves the task, but you're still adjusting spacing.

**How to spot it:** The last three changes you made would be invisible to the audience. You're optimizing for your own satisfaction, not for the receiver's experience.

**The fix:** Match the level of finish to the context. A Slack message needs less finish than a product launch. An internal tool needs less than a public API. Know when to stop.
