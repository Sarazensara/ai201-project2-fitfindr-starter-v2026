# Acceptance criteria — FitFindr

Five criteria that say what "working" means for this agent, written in unit 3
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"The agent handles errors"* is an opinion.
*"When search returns nothing, the agent stops before calling the second tool,
in 5 of 5 tries"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter one. A reason that says something about your tools, your loop, or the
data earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

**Two are written for you. You write three.**

---

## 1. A matching query completes all three tools

Given a query that matches at least one listing, the agent completes all three
tool calls and returns a fit card — in at least 4 of 5 tries.

**Why this target:**
The search step is a plain keyword match against the listings data, so some
reasonable queries will still miss even when a valid item is in the catalog.
That makes 5 of 5 too strict: the loop is simple, but the dataset and matching
logic are not guaranteed to catch every phrasing. A 4 of 5 target still
requires the agent to succeed on the normal path most of the time without
punishing normal search variation.

---

## 2. An impossible query stops before the second tool

Given a query that matches no listings, the agent stops before calling
`suggest_outfit` and returns a message naming what to change — 5 of 5 tries.

**Why this target:**
This path is the main branch in the loop and it should be deterministic: if the
search returns an empty list, the agent must exit early instead of continuing to
` suggest_outfit ` with no item. Because the branch is driven by a concrete
condition in the loop, not by model creativity, 5 of 5 is reasonable here. The
real requirement is that the user gets a helpful message about how to change the
query rather than a broken or misleading follow-up.

---

## 3. The selected item is the one passed to the next tool

Given a successful search, the item stored in `session["selected_item"]` is the same listing that reaches `suggest_outfit` — in 5 of 5 tries.

**Why this target:**
The loop is only correct if its state is consistent: the item the user searched for is the same item the next tool receives. A tool failure can look like a model problem, but this check isolates state drift by comparing the selected listing ID against what the trace records as the `new_item` passed into `suggest_outfit`. A stricter rule like "the exact text matches perfectly" would be too brittle because the same item can be represented by the same fields without a word-for-word string match.

---

## 4. The fit card keeps the key facts and stays distinct

For 5 different successful item queries, each fit card is 2 to 4 sentences long, mentions the item and its price, and does not reuse the same opening sentence across different items — 4 of 5 tries.

**Why this target:**
The model is allowed to vary its wording, so exact wording is not a useful target. What I need is evidence that the card is usable and not generic: it includes the actual item and price, stays short enough to read like a real caption, and doesn't collapse into the same stock intro for multiple finds. That makes the criterion measurable without punishing normal model creativity.

---

## 5. An empty wardrobe still gets a helpful suggestion

Given an empty wardrobe, the agent still returns a general outfit suggestion and does not crash or call `create_fit_card` — 5 of 5 tries.

**Why this target:**
This is a common real-world path and a branch point in the loop: users often have no saved clothes yet. The failure mode is not subtle — it either crashes, returns an empty string, or tries to build a fit card with no wardrobe context. A target of 5 of 5 makes sense here because the logic is straightforward and the empty case is the exact scenario the agent should handle cleanly.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 4 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 4. Something about the fit card

         The fit card is different every time.

         **Why this target:** ...

         > **Revised in unit 4:** For 5 different items, the 5 fit cards share
         > no opening sentence.
         >
         > **Why revised:** "different" wasn't checkable — two cards that
         > differed by one word still counted. The new version is something I
         > can actually score.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said the empty search stops it 5 of 5 times, but I got 3 of 5,
            so 3 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
