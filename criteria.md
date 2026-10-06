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
<!-- Why 4 of 5 and not 5 of 5? Something about your search, probably —
     "my search is a plain keyword match and some phrasings will miss" is a
     real answer. -->

     This search is a plain keyword match and involves model generated responses, so 4 out of 5 allows for the occasional miss while still requiring the full planning loop to work reliably for most matching queries. 

---

## 2. An impossible query stops before the second tool

Given a query that matches no listings, the agent stops before calling
`suggest_outfit` and returns a message naming what to change — 5 of 5 tries.

**Why this target:**
<!-- Why is 5 of 5 reasonable here when criterion 1 isn't? What's different
     about this path? -->

     This is controlled by whether the search returns an empty list. Since this doesn't involve any model generated output and should happen every time, 5 out of 5 is reasonable here. 

---

## 3. Something about state

<!-- YOU WRITE THIS ONE.

     How would you know that the item your search found is the same item the
     next tool received? Name something countable or observable.

     This is the criterion people find hardest, because state failure doesn't
     look like state failure — it looks like a tool problem. Something that
     compares session["selected_item"] against what actually reached
     suggest_outfit is the shape you're after. -->
     If a given query matches at least one listing, the item stored in session["selected_item"] is the same as the item passed to suggest_outfit in 5 out of 5 runs. 



**Why this target:**
This is a state management issue that is controlled by the code. This ensures that the item found is the item received, and it is deterministic, not determined by keyword matches or phrasings. Since this isn't dealing with model generated wording and is controlled by the code, it must pass 5 out of 5 times. 



---

## 4. Something about the fit card

<!-- YOU WRITE THIS ONE.

     The fit card calls a model, so the same input can produce different words
     each time. That's not a bug — it's the nature of the tool. So what would
     make it acceptable?

     Think about what you'd actually be unhappy to see. A caption that never
     mentions the price? Two different items producing the same opening
     sentence? A card longer than a caption anyone would post? Any of those can
     be turned into a number. -->


Given a successful outfit recommendation, create_fit_card returns a 2–4 sentence caption that is at least 100 characters long, includes the new item's name, mentions at least one outfit piece, and describes the overall style or vibe in at least 4 of 5 tries.

**Why this target:**

This checks that the fit card uses information from both the new item and the recommended outfit while keeping the result short enough to function as a caption. It stops anything too short. A 4 out of 5 target allows for any variations that could occur since it is model generated. 

**Revision note:** The original criterion required the fit card to be between 20 and 200 characters. After testing `create_fit_card` with multiple items and outfits, the generated captions were consistently longer than 200 characters while still meeting the intended purpose of a short fit-card caption. I revised the target to 2–4 sentences and at least 100 characters, while requiring the item name, an outfit piece, and the overall style or vibe. This makes the criterion more consistently measurable without imposing an arbitrary character limit.

---

## 5. Your choice

<!-- YOU WRITE THIS ONE TOO.

     Pick something you actually care about getting right. Speed, the empty
     wardrobe path, what happens when the model can't be reached, whether the
     search respects a price ceiling — anything, as long as it names a number
     or an observable outcome. -->
Given a matching listing and an empty wardrobe, suggest_outfit still returns outfit advice rather than failing in 5 of 5 tries.



**Why this target:**
The wardrobe is an input to suggest_outfit, but the agent should still be able to provide general styling advice when there are no wardrobe items to combine with the new item. This is a defined fallback behavior rather than model-generated success criteria, so it should work consistently in 5 of 5 tries.



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
