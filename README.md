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
FitFindr helps users find thrifted clothing by searching listings with a description, size, and maximum price. It selects the highest ranked match and recommends outfits using items from the user’s wardrobe. It also creates a short caption for the find that user's could post. If nothing matches, it tells the user which search details to change and stops without generating an outfit or caption.

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

- **What it does:** Searches the listing data for items matching its description as well as price and size.
- **Inputs:** 'description'(str), 'size'(str), 'max_price'(float)
- **Returns:**  A list of matching listing dicts with the field and type of size and price 
- **When it has nothing:** Returns an empty list when nothing matches

### `suggest_outfit`

- **What it does:** Suggests outfits based on the user's wardrobe and thrifted items. 
- **Inputs:** 'new_item'(dict), 'wardrobe'(dict)
- **Returns:** A string with outfit suggestions. 
- **When it has nothing:** If the wardrobe is empty, the string returns general styling advice instead of an empty string.

### `create_fit_card`

- **What it does:** Writes a short caption that users can post about their outfit finds.
- **Inputs:** 'outfit'(str), 'new_item'(dict)
- **Returns:** A string with two to four sentences as the caption that reads like a real post
- **When it has nothing:** If empty, returns a descriptive message rather than raising.

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

**Branch rule:** "If search_listings returns an empty list, set session["error"] to a message suggesting the user change their description, size, or price, then return without calling the other tools. Otherwise, save the first result as session["selected_item"], pass it to suggest_outfit, then pass the outfit and item to create_fit_card."

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which --> I use regular expressions to extract the size and maximum price; the remaining query text becomes the listing description.

**What moves through the session:** <!-- which fields, in what order -->
query → parsed description, size, and max_price in parsed → results in search_results → first result in selected_item → suggestion in outfit_suggestion → caption in fit_card. If there are no results, error is set and the run returns before creating an outfit or fit card.
---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
python app.py ask "looking for a vintage graphic tee under $30, size L"

  Found:    Graphic Tee — 2003 Tour Bootleg Style — $24.0 on depop

  Outfit:   Here are two wearable, grunge-leaning outfits using your new graphic tee and pieces from your wardrobe:

### Outfit 1: 90s Streetwear Grunge
* **Top:** Graphic tee (tucked in slightly to define the waist)
* **Bottoms:** Baggy straight-leg jeans (dark wash)
* **Accessories:** Brown leather belt, Black crossbody bag
* **Shoes:** Chunky white sneakers
* **Why it works:** The dark wash denim anchors the vintage, worn-in feel of the tee, while the chunky sneakers and crossbody bag keep the streetwear proportions balanced.

### Outfit 2: Layered Edgy Casual
* **Outerwear:** Vintage black denim jacket (worn over the tee)
* **Bottoms:** Wide-leg khaki trousers
* **Shoes:** Black combat boots
* **Accessories:** Black crossbody bag
* **Why it works:** Pairing the faded graphic tee with khaki trousers creates a cool high-low contrast. Tossing on the cropped black denim jacket and finishing with combat boots leans into the grunge aesthetic.

  Fit card: Pulled together a couple of effortless, grunge-leaning looks by pairing the Graphic Tee — 2003 Tour Bootleg Style with some baggy denim and wide-leg trousers. I scored this piece on depop for just $24.0, and the worn-in vintage wash goes with everything in my closet. Ready to throw on some combat boots and call it a day.

```

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

```
[{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth','layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K era low-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"

```
Here are two wearable, everyday outfits using your vintage Levi's 501s:

**Outfit 1: Casual Streetwear**
*   **Top:** White ribbed tank top (tucked in)
*   **Outerwear:** Vintage black denim jacket (worn over the tank)
*   **Shoes:** Chunky white sneakers
*   **Accessories:** Black crossbody bag
*   *Why it works:* The fitted white tank balances the straight-leg fit of the 501s, while the black denim jacket andchunky sneakers lean into an effortless, classic streetwear vibe.

**Outfit 2: Cozy & Relaxed**
*   **Top:** Oversized grey crewneck sweatshirt 
*   **Shoes:** Black combat boots
*   **Accessories:** Brown leather belt
*   *Why it works:* Tucking the front of the oversized crewneck into the jeans (secured with the brown leather belt) adds shape to the look, and the combat boots add a tough, grounded edge to the faded medium wash.

```
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"

```
Nothing beats the effortless, everyday feel of well-worn denim paired with crisp white sneakers. These Vintage Levi's501 Jeans — Medium Wash have that perfect broken-in look for running weekend errands. I snagged them on depop for just $38.0.

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- *What I asked for:* I asked Copilot to help implement search_listings using the loader, filtering by price and size, and ranking matches by keyword overlap.
- *What came back:* It suggested tokenizing the query and listing descriptions, filtering by the optional constraints, then sorting and limiting the results.
- *What I changed:* I added the implementation and the missing re import. I tested it with graphic tee and a $30 price limit and saw it return listings instead of an empty list. 

**Moment 2**

- *What I asked for:* I asked Copilot to help fill in run_agent and handle the case where the search finds nothing.
- *What came back:* It suggested using regex to extract the size and price, storing each result in the session, and returning early with an error message when the search list is empty. 
- *What I changed:* I implemented that flow. My matching query test returned a listing, outfit suggestion, and fit card; I also added the empty-search branch before the outfit and fit-card calls. 

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
| 1. matching query completes | 4 of 5 | Pass | Pass  | Pass | Pass | Pass | Met |
| 2. impossible query stops early | 5 of 5 | Pass | Pass | Pass | Pass | Pass | Met |
| empty wardrobe _(diagnostic — not one of your five)_ |  |   |   |   |   |   |  |
| 3. selected item passed to outfit | 5 of 5 | Pass | Pass | Pass | Pass | Pass | Met |
| 4. fit card includes price and platform | 4 of 5 | Pass | Pass | Pass | Pass | Pass | Met |
| 5. search respects maximum price | 5 of 5 | Pass | Pass | Pass | Pass | Pass | Met |


**Real output from one try**, pasted as text, naming the file and function
that produced it:

```  Produced by run_eval.py::run_once and calls agent.py::run_agent

Criterion 1:
- stopped early: no
- selected_item: Graphic Tee — 2003 Tour Bootleg Style ($24.0, depop)
- search_results: 8
[1] search_listings (via MCP)
[2] suggest_outfit
[3] create_fit_card
Fit card: Stole this Graphic Tee — 2003 Tour Bootleg Style off depop for only $24.0, and it’s the ultimate grunge-streetwear find. I love dressing it down with dark baggy denim and combat boots for an effortless, heavy-metal vibe. It also looks super cool mixed with tailored khaki trousers and chunky white sneakers for a high-low look.

```  Produced by run_eval.py::run_once and calls agent.py::run_agent

Criterion 2:
- stopped early: yes — No matching listings. Try changing the description, size, or maximum price.
- selected_item: (none)
- search_results: 0
[1] search_listings (via MCP)
      out: [] (empty)
      → empty results; stopping

```  Produced by run_eval.py::run_once and calls agent.py::run_agent

Criterion 3:
- selected_item: Vintage Band Tee — Faded Grey ($19.0, depop)
[2] suggest_outfit
      in: new_item_id=lst_033, wardrobe_item_count=10

``` Produced by tools.py::create_fit_card and calls agent.py::run_agent

Criterion 4:
Channeling effortless 90s athletic energy with this vintage-inspired sporty look. I found the 90s Track Jacket — Navy/White Stripe listed on poshmark for just $45.0. It's the ultimate piece for mixing relaxed streetwear with tailored elements.

``` Produced by tools.py::search_listings and calls agent.py::run_agent

Criterion 5:
- Query: graphic tee under $30
- selected_item: Y2K Baby Tee — Butterfly Print ($18.0, depop)
- search_results: 6
[('lst_002', 18.0), ('lst_006', 24.0), ('lst_017', 15.0), ('lst_033', 19.0), ('lst_011', 27.0), ('lst_015', 26.0)]


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
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 8 items: Graphic Tee — 2003 Tour Bootleg Style, Y2K Baby Tee — Butterfly Print, Vintage Graphic Hoodie — Faded Black … +5 more
[2] suggest_outfit
      in:  dict with keys: new_item, wardrobe
      out: Here are two wearable, grunge-inspired outfits using your new graphic tee and pieces from your wardrobe:  **Ou…
[3] create_fit_card
      in:  dict with keys: outfit, new_item
      out: Scored this vintage-vibed Graphic Tee — 2003 Tour Bootleg Style on depop for just $24.0, and it’s the ultimate…

  Found:    Graphic Tee — 2003 Tour Bootleg Style — $24.0 on depop

  Outfit:   Here are two wearable, grunge-inspired outfits using your new graphic tee and pieces from your wardrobe:

**Outfit 1: 90s Streetwear Grunge**
*   **Top:** Graphic Tee (loose fit)
*   **Bottoms:** Baggy straight-leg jeans (dark blue)
*   **Outerwear:** Vintage black denim jacket (worn over the tee)
*   **Shoes:** Chunky white sneakers
*   **Accessories:** Black crossbody bag
*   *Why it works:* Double denim with a boxy band tee hits that authentic 90s streetwear aesthetic. The chunky sneakers balance out the baggy jeans. 

**Outfit 2: High-Contrast Edgy Casual**
*   **Top:** Graphic Tee (tucked in)
*   **Bottoms:** Wide-leg khaki trousers
*   **Accessories:** Brown leather belt (cinched at the waist) + Black crossbody bag
*   **Shoes:** Black combat boots
*   *Why it works:* Tucking the relaxed tee into sharp, wide-leg khakis creates an effortless high-low mix. Groundingit with combat boots and the brown belt adds a gritty, intentional finish.

  Fit card: Scored this vintage-vibed Graphic Tee — 2003 Tour Bootleg Style on depop for just $24.0, and it’s the ultimate piece for nailing 90s streetwear grunge. Paired it with baggy denim and a black jacket for that perfectly slouchy, double-denim look. It also transitions effortlessly into edgy casual when tucked into wide-leg khakis and grounded with combat boots.

```

**Empty search**

```
[1] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: [] (empty)
      →    empty results; stopping

  No matching listings. Try changing the description, size, or maximum price.

```

**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->



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
