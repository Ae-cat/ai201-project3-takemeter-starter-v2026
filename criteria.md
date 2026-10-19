# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy

Accuracy on the held-out test split is at least 0.70, in each of the three seeds.

**Why this target:** With four labels, guessing gets about 25%, so 70% is nearly three times chance. A classifier that gets 7 of 10 posts right is useful for sorting posts, though not for anything high-stakes.


---

## 2. Per-label performance

Every label has an F1 of at least 0.60 on the held-out test split, in each of the three seeds.

**Why this target:** I don't want the model to ignore a small label. `analysis` will have the fewest posts, so its score will be noisy, and I'll say so if it misses.


---

## 3. Balance

No label is more than 60% of the rows in `labels.csv`.

**Why this target:** I picked 60% instead of 50% because 50% is too in-between: a label at half the file isn't clearly lopsided or clearly fine. 60% is where one label clearly dominates and teaches the model to guess it. My largest label is about 30% by my rough count, so I expect to pass. The notebook also warns at 60%.


---

## 4. Consistency

I agree with the staff labels on at least 27 of the 30 staff posts (90%).

**Why this target:** If I can't apply someone else's written rules the same way they did, my own 200 labels probably weren't consistent either.


---

## 5. Confidence

On the most-confident third of test predictions, accuracy is at least 0.90, in each of the three seeds.

**Why this target:** A model's confidence should mean something. With about 30 test posts, a third is about 10 posts, so this allows at most one miss.


---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
