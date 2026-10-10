# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Three or four sentences: what a user asks for, and what they get back. -->

FitFindr helps users find secondhand clothing listings based on a description, size, and maximum price, then suggests outfits using the selected item and the user's wardrobe. It uses a planning loop to decide whether to continue based on the search results. When a matching listing is found, FitFindr generates outfit suggestions and a short fit-card caption for the selected item. If no listings match, the agent stops and tells the user what they can change instead of continuing with empty results.




---

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### `search_listings`

- **What it does:** Searches the clothing listings for items matching the user's description, requested size, and maximum price.
- **Inputs:** `description` (str), `size` (str, optional), `max_price` (float, optional, in whole dollars) size: requested size string; matching is case-insensitive and supports combined sizes such as S/M.
- **Returns:** A list of matching clothing listings, including each listing's title, description, category, style tags, size, price, colors, brand, and platform.
- **When it has nothing:** Returns an empty list when no listings match the description, size, and maximum price.

### `suggest_outfit`

- **What it does:** Uses the selected new clothing item and the user's wardrobe to generate outfit ideas that combine the new item with pieces from the wardrobe
- **Inputs:** `new_item` (dict), `wardrobe` (dict with items list)
- **Returns:** Outfit suggestions describing how to style the new item with pieces from the wardrobe as a string.
- **When it has nothing:** If the wardrobe is empty, returns general outfit advice for the new item instead of failing.

### `create_fit_card`

- **What it does:** Creates a short fit-card caption based on the selected outfit and new clothing item.
- **Inputs:** `outfit` (str), `new_item` (dict)
- **Returns:** A short text caption describing the completed outfit and new item.
- **When it has nothing:** If `outfit` is empty or whitespace, returns the message "No outfit suggestion was provided, so a fit card could not be created." instead of calling the model.

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:**
The agent first searches for listings. If `search_listings` returns an empty list, the agent stops and tells the user what they can change, such as the description, size, or maximum price. It does not call `suggest_outfit` or `create_fit_card` when there are no matching listings. Otherwise, if matching listings are found, the agent selects a listing and continues to `suggest_outfit`, then uses the outfit and selected item to call `create_fit_card`.

**Where it lives:** `agent.py::run_agent`


**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which -->
The query is parsed with regular expressions in agent.py::parse_query, which extracts the description, optional size, and optional maximum price before searching.

**What moves through the session:** <!-- which fields, in what order -->
The session stores parsed, search_results, selected_item, wardrobe, outfit_suggestion, and fit_card. The search results are stored first, the selected item is read from the session for suggest_outfit, and the resulting outfit is read from the session for create_fit_card.

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'vintage graphic tee under $30'
[1] parse_query
      in:  vintage graphic tee under $30
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 8 items: Graphic Tee — 2003 Tour Bootleg Style, Y2K Baby Tee — Butterfly Print, Vintage Graphic Hoodie — Faded Black … +5 more
      →    8 match(es)
[3] select_item
      out: Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
[4] suggest_outfit
      in:  Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
      out: Here are two outfit suggestions using your new graphic tee and pieces from your wardrobe:  ### Outfit 1: 90s G…
      →    10 wardrobe item(s)
[5] create_fit_card
      in:  Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
      out: Channeling major 90s grunge with this 2003 Tour Bootleg Style graphic tee, scored on Depop for just $24. It’s …

  Found:    Graphic Tee — 2003 Tour Bootleg Style — $24.0 on depop

  Outfit:   Here are two outfit suggestions using your new graphic tee and pieces from your wardrobe:

### Outfit 1: 90s Grunge Streetwear
* **Top:** Graphic Tee (New Item)
* **Bottoms:** Baggy straight-leg jeans (dark wash)
* **Outerwear:** Vintage black denim jacket
* **Shoes:** Black combat boots
* **Accessories:** Black crossbody bag

**Why it works:** This look leans heavily into the grunge and streetwear aesthetic of the tee. Pairing the faded black graphic tee with dark wash baggy jeans creates an effortless, relaxed silhouette. Throwing the vintage black denim jacket on top adds texture and cohesion, while the black combat boots anchor the outfit with a tough, classic edge. 

### Outfit 2: Elevated Casual Streetwear
* **Top:** Graphic Tee (New Item) layered over or under, paired with the Black cropped zip hoodie
* **Bottoms:** Wide-leg khaki trousers 
* **Shoes:** Chunky white sneakers
* **Accessories:** Brown leather belt, Black crossbody bag

**Why it works:** This outfit balances edgy streetwear with tailored minimal pieces. The boxy graphic tee tucked into the wide-leg khaki trousers creates a great proportion play, cinched together with the brown leather belt for a touch of contrast. Adding the black cropped zip hoodie (either worn open or carried) and finishing with chunky white sneakers keeps the overall vibe modern, comfortable, and effortlessly cool.

  Fit card: Channeling major 90s grunge with this 2003 Tour Bootleg Style graphic tee, scored on Depop for just $24. It’s got that perfect worn-in softness and boxy fit that looks unreal paired with baggy dark wash denim and combat boots. Total effortless streetwear energy.

0 model calls this session, 2 served from cache

```

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

[{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K era low-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]

```

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"

Here are two practical outfit suggestions using your new Vintage Levi's 501 Jeans and pieces from your existing wardrobe:

### Outfit 1: Effortless Streetwear
*   **Top:** White ribbed tank top
*   **Outerwear:** Vintage black denim jacket
*   **Shoes:** Chunky white sneakers
*   **Accessories:** Black crossbody bag

**Why it works:** 
This is a classic, effortless combination. The fitted silhouette of the white ribbed tank top balances the relaxed, straight-leg cut of the vintage Levi's. Layering the vintage black denim jacket on top plays into the retro aesthetic of the jeans while adding a cool, textured contrast (blue denim on black denim). Finished with chunky white sneakers and the black crossbody bag, the look leans into a comfortable, everyday streetwear vibe.

---

### Outfit 2: Cozy Casual
*   **Top:** Oversized grey crewneck sweatshirt
*   **Shoes:** Black combat boots
*   **Accessories:** Brown leather belt, Black crossbody bag

**Why it works:**
This outfit plays with proportions by pairing the boxy, oversized grey crewneck with the structured fit of the mid-rise 501s. Tucking the front of the sweatshirt in slightly and adding the brown leather belt pulls the look together while breaking up the grey-and-blue color palette with a warm earth tone. Grounding the outfit with black combat boots adds a subtle edge that complements the lived-in, vintage fading at the knees of the jeans.

```

```
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('black boots and leather jacket', load_listings()[1]))"

Channeling peak early 2000s energy in this Y2K Baby Tee — Butterfly Print, especially when I toughen it up with a leather jacket and black boots. The vibe is total sweet-meets-edgy nostalgia. Snagged this little crop on Depop for just $18.00!

```

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- **What I asked for:** I asked AI for help implementing the `search_listings` tool while following the instructor's requirements for keyword matching, price filtering, and case-insensitive size matching.
- **What came back:** AI suggested using keyword extraction and splitting combined sizes such as `S/M` into individual size options. It also explained why naive substring matching would cause incorrect matches, such as treating `L` as a match for `XL`.
- **What I changed:** I reviewed the approach against the provided requirements and implemented the size matching and keyword filtering in `tools.py`. I tested matching queries, size and price filters, and a query that returned no results.

**Moment 2**

- **What I asked for:** I asked AI to help evaluate the requirements for the fit-card acceptance criterion after testing `create_fit_card` with several different outfits and items.
- **What came back:** The generated fit cards were naturally longer than the original 20–200 character limit while still producing useful 2–4 sentence captions with the item, outfit details, and overall vibe.
- **What I changed:** I changed my acceptance criterion to focus on the qualities that made the fit card useful: a 2–4 sentence caption of at least 100 characters that includes the new item's name, at least one outfit piece, and the overall style or vibe.

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     `python run_eval.py --label before` runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Real output from one try**, pasted as text, naming the file and function
that produced it:

```

```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**



---

## Loop Trace

<!-- One full run, printed step by step, with the MCP call visible in it.

     `python app.py ask '...' --trace` once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path**

```
[1] parse_query
      in:  rust corduroy wide-leg pants under $35
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 5 items: Corduroy Wide-Leg Pants — Rust, Wide-Leg Linen Trousers — Natural, Low-Rise Cargo Pants — Khaki … +2 more
      →    5 match(es)
[3] select_item
      out: Corduroy Wide-Leg Pants — Rust ($32.0, depop)
[4] suggest_outfit
      in:  Corduroy Wide-Leg Pants — Rust ($32.0, depop)
      out: Here are two outfit suggestions using your new rust corduroy wide-leg pants and pieces from your wardrobe:  ##…
      →    10 wardrobe item(s)
[5] create_fit_card
      in:  Corduroy Wide-Leg Pants — Rust ($32.0, depop)
      out: Channeling major 70s autumn energy in these Rust Corduroy Wide-Leg Pants, which I just dropped on Depop for $3…

  Found:    Corduroy Wide-Leg Pants — Rust — $32.0 on depop

  Outfit:   Here are two outfit suggestions using your new rust corduroy wide-leg pants and pieces from your wardrobe:

### Outfit 1: 70s Earth-Toned Casual
* **Top:** White ribbed tank top
* **Outerwear:** Vintage black denim jacket
* **Accessories:** Brown leather belt, black crossbody bag
* **Shoes:** Chunky white sneakers

**Why it works:** 
The fitted white ribbed tank top balances the voluminous, high-waisted silhouette of the wide-leg cords. Tucking in the tank and adding the brown leather belt highlights your waist while tying into the warm, earthy 70s aesthetic. Throwing on the vintage black denim jacket adds a classic layer that complements the vintage vibeof the corduroy, and the chunky white sneakers finish the look with a fresh, casual touch.

### Outfit 2: Cozy & Contrasting Layers
* **Top:** Oversized grey crewneck sweatshirt
* **Accessories:** Brown leather belt, black crossbody bag
* **Shoes:** Black combat boots

**Why it works:**
This outfit plays with proportions by pairing the slouchy, oversized grey crewneck with the textured rust cords. By doing a "half-tuck" with the crewneck into the front of the pants (secured with the brown leather belt), you keep some shape to your waist while maintaining a relaxed, cozy feel. Pairing the warm rust tones withthe cool grey creates a great color contrast, and the black combat boots add a slightly edgy anchor to the outfit.

  Fit card: Channeling major 70s autumn energy in these Rust Corduroy Wide-Leg Pants, which I just dropped on Depop for $32. They are giving effortless earth-tone vintage, and I am so obsessed with how they look half-tucked into a cozy grey crewneck for that slouchy, textured contrast. Seriously the ultimate piece for building warm-toned transitional fits.

2 model calls this session, 843 prompt + 390 output tokens

```

**Empty search**

```
python app.py ask 'a solid gold spacesuit under $1'

[1] parse_query
      in:  a solid gold spacesuit under $1
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: [] (empty)
      →    0 match(es)
[3] branch
      →    search returned []: stopping before suggest_outfit

  Nothing in the listings matched description 'a solid gold spacesuit', under $1.
Things to change: try broader words — 'jacket' finds more than 'cropped corduroy jacket'; raise the price ceiling above $1.

0 model calls this session

```

**Empty Wardrobe**
```

(running with an empty wardrobe)
[1] parse_query
      in:  vintage graphic tee under $30
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 8 items: Graphic Tee — 2003 Tour Bootleg Style, Y2K Baby Tee — Butterfly Print, Vintage Graphic Hoodie — Faded Black … +5 more
      →    8 match(es)
[3] select_item
      out: Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
[4] suggest_outfit
      in:  Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
      out: Here are three practical ways to style the 2003 tour bootleg graphic tee:  **1. 90s Grunge (Textured Contrast)…
      →    0 wardrobe item(s)
[5] create_fit_card
      in:  Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
      out: Nothing beats the perfectly worn-in feel of this 2003 Tour Bootleg Graphic Tee ($24.00, live on Depop now). It…

  Found:    Graphic Tee — 2003 Tour Bootleg Style — $24.0 on depop

  Outfit:   Here are three practical ways to style the 2003 tour bootleg graphic tee:

**1. 90s Grunge (Textured Contrast)**
* **The Vibe:** Effortless, edgy, and relaxed.
* **How to style:** Pair the boxy black tee with relaxed-bottom pants in a contrasting texture or lighter wash (like distressed denim, corduroy, or plaid trousers). Layer with an open flannel shirt or a distressed knit cardigan. Finish with chunky boots or retro sneakers.

**2. Elevated Streetwear (Clean & Proportionate)**
* **The Vibe:** Modern, sharp, and intentional.
* **How to style:** Balance the boxy fit of the tee by tucking it into straight-leg or wide-leg bottoms in a solid neutral (like black, charcoal, or olive). Add a structured outer layer like a leather jacket, a denim jacket, or a minimalist overcoat. Accessorize with a silver chain necklace, a canvas belt, and court sneakers or loafers.

**3. Warm-Weather Casual (Simple & Easy)**
* **The Vibe:** Laid-back, everyday comfort.
* **How to style:** Wear the tee untucked with relaxed-fit shorts (denim, cargo, or nylon athletic styles). Keep it grounded with classic canvas low-top sneakers or slides. Add sunglasses or a baseball cap to complete the casual look.

  Fit card: Nothing beats the perfectly worn-in feel of this 2003 Tour Bootleg Graphic Tee ($24.00, live on Depop now). It’s got that boxy, effortless grunge vibe that makes throwing together an outfit way too easy. Pair it with distressed denim and an open flannel for the ultimate textured contrast, or keep it clean for the streets.

2 model calls this session, 568 prompt + 369 output tokens
```

**Model Unavailable**

```
[1] parse_query
      in:  rust corduroy wide-leg pants under $35
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 5 items: Corduroy Wide-Leg Pants — Rust, Wide-Leg Linen Trousers — Natural, Low-Rise Cargo Pants — Khaki … +2 more
      →    5 match(es)
[3] select_item
      out: Corduroy Wide-Leg Pants — Rust ($32.0, depop)
[4] model unavailable
      →    stopping, search results kept

  The model couldn't be reached, so the outfit and caption steps didn't run. The search worked — 5 listing(s) were found. Check GEMINI_API_KEY in your .env, then run the same query again.
What the service said: The model rejected your API key. Check GEMINI_API_KEY in your .env file, or create a fresh key at aistudio.google.com.

1 model calls this session
```


**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->

I registered `search_listings` in `mcp_server.py` and connected the agent's `_search()` function to call it through `mcp_client.call_tool()`. The MCP client confirmed that the server exposes `search_listings`. The agent's search trace showed the results, and the query continued through outfit suggestion and fit-card generation. Nothing behaved differently afterwards: the MCP call returned exactly the same list of listing dicts as the direct call (5 identical results for "rust corduroy wide-leg pants" under $35), and an impossible query still came back as an empty list.




---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:**

**Which failure it was meant to fix:**

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
