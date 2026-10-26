# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

My community is r/volleyball (https://www.reddit.com/r/volleyball/), a public
subreddit where players, parents, coaches, and fans post about the sport. I collected 200 posts and saw real differences in quality: some posts back what they say with specifics like prices, rules, or named events, some state an opinion with nothing behind it, some are an immediate reaction to a game or play, and many just ask for help or advice. My classifier will sort posts into labels based on those differences.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Makes an argument or explanation backed by specific, checkable detail, like a stat, a rule, a named event, or step-by-step reasoning.

**Example 1:**
> In no official rulebook anywhere, is spin called out as a fault... the USAV
> beach volleyball rule states "Rotation of a set ball may indicate a held ball
> or multiple contacts during the set but in itself is not a fault."

**Example 2:**
> Attack Efficiency: R = .94. As expected, there is a huge correlation between
> attack efficiency and winning games. *(S.V League stats breakdown)*

### `hot_take`

**Definition:** A confident claim with no support offered. It might be true, but it isn't argued.

**Example 1:**
> I feel sad that everything revolves around money and shorter players can't
> make a living out of this like tall players do (% population wise).

**Example 2:**
> setting can't be taught or learned some people really do just have that
> touch, craft, and creativity as a setter.

### `reaction`

**Definition:** The writer's immediate feeling about something that just happened to them, with no argument, no general claim, and no request for help.

**Example 1:**
> I had my first career ace on varsity today!!

**Example 2:**
> Seeing these women celebrate and cry after their win tonight at the NORCECA
> Qualifications made me tear up on my couch!!

### `help_request`

**Definition:** Asks for advice or information, and claims nothing.

**Example 1:**
> I am having difficult finding 3/4 length sleeve shirts with athletic fabric
> (sweat profusely). Does anyone have any recommendations on what they wear?

**Example 2:**
> Jump floats. I always contact it wrong I think it's my toss, any tips on
> drills to help with my tossing?

### The hardest boundary

**Which two labels:** `reaction` vs. `help_request`

**The decision rule I used every time:** If the post ends by asking for advice or opinions, it's `help_request`, even if most of it is feeling. If it asks nothing, it's `reaction`. *(Borderline example: a player describes a bad match and the coach blaming them, then ends with "What are your thoughts?")*

**Order I check in, so the same post always gets the same label:**

1. If it contains a checkable fact that carries an argument, it's `analysis`
   (even if the tone is emotional).
2. Otherwise, if its main purpose is to ask for advice or information, it's
   `help_request`.
3. Otherwise, if it makes a general claim with no support, it's `hot_take`.
4. Otherwise, it's `reaction`.

**Scope note:** Promotions, ads, and listings (apps, directories, tickets, open-gym invites) are left out of the dataset rather than given a label.


---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** I collected 200 public posts from r/volleyball by hand. I left out promos, ads, and listings, and removed duplicates before labelling.

**How I labelled them:** I labelled the first 20 posts (rows 2 to 21) cold, by myself and with no AI help, and wrote a one-line reason for each. I then had an AI tool pre-label the other 180 posts using my label definitions. I read every one of those pre-labels and kept the ones I agreed with. Pre-labelled rows are marked `prelabel` in the `note` column and cold rows are marked `cold`.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `help_request` | 111 | 55.5% |
| `reaction` | 44 | 22% |
| `analysis` | 26 | 13% |
| `hot_take` | 19 | 9.5% |
| **Total** | 200 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* Row 5. A 17-year-old describes a teammate (the setter) who avoids setting her, with a long story of how it played out. It ends with "Some advice would really be appretiated."
>
> *Could have been:* `help_request` or `reaction`.
>
> *I chose* `reaction`, *because:* almost all of the post is her telling the story and how it makes her feel, so the advice request is a small part of it. My written rule says a post that ends by asking for advice is `help_request`, so this was an exception I made on purpose.

**2.**
> *The post:* Row 18. A new JV coach tells a player picking her nails to leave the hitting line, then asks "Do you think that's too harsh?"
>
> *Could have been:* `help_request` or `reaction`.
>
> *I chose* `reaction`, *because:* the coach is reacting to something they just did and second-guessing it. The question is part of that feeling, not a request for advice. My written rule says a post that ends by asking for opinions is `help_request`, so this was another deliberate exception.

**3.**
> *The post:* Row 21. A fan argues that Montenegro can't improve under its coach, who is "hot-tempered" and overuses substitutions, and backs it with an Alexander the Great quote about lions led by a sheep.
>
> *Could have been:* `hot_take` or `analysis`.
>
> *I chose* `hot_take`, *because:* the coach's tempo and substitutions are the poster's opinion, and the quote is rhetoric, not a stat or a checkable fact.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, 128 tokens per post, seed 42. I trained on an Apple Silicon GPU (`mps`) with torch 2.14.1, as recorded in `results.json`.

**Anything I changed from the defaults, and why:** Nothing. I used the notebook's defaults. The only change was setting `LABELS` to my four labels.

**Split sizes:** train 139, val 31, test 30. Test split per label: `help_request` 17, `reaction` 6, `analysis` 4, `hot_take` 3. Three labels have fewer than 8 test examples, so their F1 will jump around between seeds. That is expected and I'll account for it in Unit 6.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* I sent AI my first 200 collected r/volleyball posts and asked it to survey them for duplicates and posts that shouldn't be in the dataset (promos, ads, listings).
- *What came back:* a list of 29 posts to remove, including a duplicate pair, along with the reasons for each.
- *What I changed:* I removed those 29 and collected 29 replacements, then sent them back for the same check. Four were too weak, so I dropped them and found others. That left exactly 200 posts.

**Moment 2**

- *What I asked for:* While writing `criteria.md`, I proposed my own targets and asked AI for a sample to react to. For the confidence criterion I wrote 100% accuracy on the model's most-confident predictions.
- *What came back:* AI told me 100% wasn't the best choice. The most-confident third of a small test split is only about 10 posts, so a single wrong one would fail the criterion. It suggested a high but reachable target instead.
- *What I changed:* I changed the confidence criterion to at least 90% accuracy on the most-confident third of test predictions, in each of the three seeds. It still means "confident should mean right," but one miss doesn't fail it.

**Pre-labelling disclosure:** I had an AI tool pre-label 180 of the 200 posts (rows 22 to 201), given my four label definitions and my decision order. I read each one and kept every label. Those rows are marked `prelabel` in the `note` column. The first 20 posts (rows 2 to 21) were labelled cold, by me alone with no AI help.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.133 | 0.567 | +0.433 |
| Macro F1 | 0.059 | 0.181 | +0.122 |
| F1 — `analysis` | 0.235 | 0.000 | -0.235 |
| F1 — `help_request` | 0.000 | 0.723 | +0.723 |
| F1 — `hot_take` | 0.000 | 0.000 | +0.000 |
| F1 — `reaction` | 0.000 | 0.000 | +0.000 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
I predict that my trained model will beat the baseline most on analysis, because those types of posts typically contain numerical values. Although I think it will struggle with hot_take given that one could be mistaken for as a reaction.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

My prediction was wrong. The trained model scored F1 0.000 on `analysis`, worse than the baseline's 0.235. Its accuracy advantage (+0.433) is not real learning: 0.567 is 17 of 30, exactly the number of `help_request` posts in the test split, so the trained model labelled every post `help_request` and scored 0.000 on the other three labels. The baseline made the mirror-image mistake and labelled every post `analysis`.

I ran the baseline twice. With my written definitions it scored 0.133 accuracy and 0.059 macro F1. With bare label names only (`python baseline.py --definitions none.txt`, saved in `baseline_bare.json`) it scored 0.433 accuracy and 0.451 macro F1, so my definitions cost the baseline 30 points, probably because my `analysis` wording matched almost every long post. The bare-names baseline is the stronger opponent, and it beats my trained model on macro F1 (0.451 vs 0.181). So fine-tuning on 200 posts has not yet added anything beyond predicting my biggest label.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.70 | 0.567 | 0.567 | 0.567 | MISSED |
| 2. Every label F1 | ≥ 0.60 | 0.000 (lowest label) | 0.000 (lowest label) | 0.000 (lowest label) | MISSED |
| 3. Balance (no label over 60%) | ≤ 60% | 55.5% | 55.5% | 55.5% | MET |
| 4. Agreement with staff labels | ≥ 27 / 30 | 28 / 30 | 28 / 30 | 28 / 30 | MET |
| 5. Accuracy on most-confident third | ≥ 0.90 | 0.70 | 0.70 | 0.80 | MISSED |

Device: all three seeds were trained on `mps` (Apple Silicon GPU), torch 2.14.1, as recorded in `results_three_seeds_before.json`. Criterion 3 depends on the file, not the seed, so it is the same in all three columns.

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.567 | 0.567 | 0.567 | 0.000 |
| Macro F1 | 0.181 | 0.181 | 0.181 | 0.000 |
| F1 — `analysis` | 0.000 | 0.000 | 0.000 | 0.000 |
| F1 — `hot_take` | 0.000 | 0.000 | 0.000 | 0.000 |
| F1 — `reaction` | 0.000 | 0.000 | 0.000 | 0.000 |
| F1 — `help_request` | 0.723 | 0.723 | 0.723 | 0.000 |


### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | analysis | hot_take | reaction | help_request |
|---|---|---|---|---|
| **analysis** | 0 | 0 | 0 | 4 |
| **hot_take** | 0 | 0 | 0 | 3 |
| **reaction** | 0 | 0 | 0 | 6 |
| **help_request** | 0 | 0 | 0 | 17 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

The matrix is from seed 42, and the other two seeds are identical. The biggest off-diagonal number is 6: all 6 real `reaction` posts were called `help_request`, and 0 went the other way. In fact every column except `help_request` is empty, so the model has not learned any boundary at all. It labels every post `help_request`, which also sends all 4 `analysis` and all 3 `hot_take` posts to it. Accuracy of 0.567 is just the 17 real `help_request` posts out of 30.



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.70 | MISSED | 0.567 in all three seeds, below 0.70 every time. |
| 2 | Every label F1 | ≥ 0.60 | MISSED | `help_request` is 0.723, but `analysis`, `hot_take` and `reaction` are 0.000 in all three seeds. |
| 3 | No label over 60% of `labels.csv` | ≤ 60% | MET | `help_request` is 55.5%. This is a property of the file, so it is the same in every seed, and it passes by only 4.5 points. |
| 4 | Agreement with staff labels | ≥ 27 / 30 | MET | 28 / 30 (93%). This is one measurement, not three seeds, and it is made on staff's three-label taxonomy, not my four. |
| 5 | Accuracy on the most-confident third | ≥ 0.90 | MISSED | 0.70, 0.70 and 0.80, below 0.90 in every seed. |

**Diagnoses**

**Criteria 1 and 2 (accuracy and per-label F1): the model learned nothing beyond my biggest label.** Accuracy 0.567 is exactly 17 of 30, the number of `help_request` posts in the test split, and my confusion matrix has every prediction in the `help_request` column. The three seeds gave identical results, so this isn't a bad split. It's the same collapse each time. The most likely cause is too few examples: the training split had only 18 `analysis`, 13 `hot_take` and 31 `reaction` posts against 77 `help_request`, and 3 epochs on 139 posts looks too little for the model to move off guessing the majority label. A bare-label-name baseline (macro F1 0.451) beat my trained model (0.181), which suggests the task is reachable and my training setup is what failed.

**Criterion 5 (confidence): the confidence numbers loss their value here.** Because the model labels everything `help_request`, accuracy in its most-confident third is just the share of `help_request` posts that landed there. Its average confidence when right (0.54 in seed 42) is almost the same as when wrong (0.50), so it's barely more sure about the right answers than the wrong ones. This is the same cause as criteria 1 and 2.

**What my agreement report says about labeling versus model.** My 28 of 30 agreement is high, and my only two disagreements were both `reaction` against `hot_take`, so I do apply that boundary inconsistently and it may hurt my `hot_take` and `reaction` labels. But the agreement report can't explain the collapse on `analysis` and `help_request`: I agreed with staff on every `analysis` post, so my `analysis` labeling isn't the problem. And the staff set has no `help_request` label, so the check says nothing about my hardest boundary, `reaction` against `help_request`. My conclusion is that criteria 1, 2 and 5 are mainly a data-size and balance problem in the training set, with a labeling problem on `hot_take` against `reaction` as a smaller second cause.

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 28 / 30 = 93%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:* "The club needs to stop pretending the Tuesday session is for everyone. It isn't and it never has been."
>
> *Staff said / I said:* Staff said `hot_take` / I said `reaction`.
>
> *My call, and why:* I changed my mind, and staff is right. The post is irritated, but it asserts something general about the club ("it never has been") and offers no support. Rule 2 says a post can be heated and still be `hot_take` if it is making a general claim.
>
> *Which pile:* (a) The rule covered it and I applied it loosely.

**2.**
> *The post:* "Every injury thread on this forum is the same injury and the same three replies."
>
> *Staff said / I said:* Staff said `hot_take` / I said `reaction`.
>
> *My call, and why:* I still read it as a `reaction`: it responds to what the writer has been shown on the forum, and there is some irritation at how repetitive it is and how little ground the threads cover. It reports the writer's feeling about something they just read, and it offers no argument. I can see staff's side, since "every injury thread" is a general statement, but I think the irritation is what the post is about.
>
> *Which pile:* (c) The rule is ambiguous here and my reading is defensible. Rule 2 asks whether the post is about a claim or about the writer, and this one is both.

**What the pattern in my disagreements tells me:**

I tend to mix up `hot_take` and `reaction` when comparing them. I infer the intent behind a post, for example irritation, and let that change which label I pick, instead of asking whether the post states a general claim. Both of my disagreements were this same pair, in the same direction, so this is a boundary I applied by feel and not by a fixed rule, and it probably affects my own 200 labels too.



---

## The Improvement

**What I changed:** One training setting: `EPOCHS` from 3 to 10. Everything else stayed the same: the same 200 labels, learning rate 2e-5, batch size 16, 128 tokens, and the same three seeds (42, 7, 2024). `RUN_LABEL` was set to `"after"`.

**Which diagnosis pointed at it:** In my "before" run the model labelled every test post `help_request` in all three seeds, which gave 0.567 accuracy and F1 0.000 on three labels. I diagnosed this as the model never moving off the majority label with only 3 epochs on 139 training posts (about 27 update steps), so I gave it more epochs. I chose this over relabeling because my agreement check found only 2 disagreements out of 30, which can't explain a collapse on every label.

### Run Log — After

`EPOCHS = 10`, trained on `mps`, results in `results_three_seeds_after.json`.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.70 | 0.700 | 0.567 | 0.600 | MISSED |
| 2. Every label F1 | ≥ 0.60 | 0.000 (`hot_take`) | 0.000 (3 labels) | 0.000 (2 labels) | MISSED |
| 3. Balance (no label over 60%) | ≤ 60% | 55.5% | 55.5% | 55.5% | MET |
| 4. Agreement with staff labels | ≥ 27 / 30 | 28 / 30 | 28 / 30 | 28 / 30 | MET |
| 5. Accuracy on most-confident third | ≥ 0.90 | 0.80 | 0.80 | 0.90 | MISSED |

| Measure | Before (3 epochs) | After (10 epochs) |
|---|---|---|
| Accuracy, seeds 42 / 7 / 2024 | 0.567 / 0.567 / 0.567 | 0.700 / 0.567 / 0.600 |
| Macro F1, seeds 42 / 7 / 2024 | 0.181 / 0.181 / 0.181 | 0.452 / 0.185 / 0.256 |

Seed 42's confusion matrix after the change (rows true, columns predicted):

| true \ predicted | analysis | hot_take | reaction | help_request |
|---|---|---|---|---|
| **analysis** | 1 | 0 | 1 | 2 |
| **hot_take** | 0 | 0 | 0 | 3 |
| **reaction** | 0 | 0 | 3 | 3 |
| **help_request** | 0 | 0 | 0 | 17 |

**Did it help, and how do I know:** Slightly, but it did not fix the problem. Accuracy rose in two of three seeds (0.567 to 0.700 and 0.600) and stayed at 0.567 in seed 7, and macro F1 went from 0.181 to between 0.185 and 0.452. Only in seed 42 did the model start predicting other labels, getting 3 of 6 `reaction` and 1 of 4 `analysis` right. In seeds 7 and 2024 it still sent almost everything to `help_request`, and `hot_take` was still 0.000 in all three seeds. No verdict changed: criteria 1, 2 and 5 are still MISSED. Accuracy now swings 0.133 across seeds, more than the ten-point limit, so my dataset is too small and lopsided for a stable measure. With only 30 test posts, one post is worth 3.3 points, so even the seed 42 gain is within noise.

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
