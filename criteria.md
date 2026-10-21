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

Overall accuracy on the held-out test set is at least 0.73.

**Why this target:** I picked 73% because it shows the model is clearly identifying the labels beyond chance (50% for two labels), while still accounting for the ambiguity in natural language that makes some posts hard to classify even for a human.

## 2. Per-label F1

F1 for `analysis` is at least 0.70, and F1 for `reaction` is at least 0.65.

**Why this target:** I set a slightly lower target for `reaction` because it's vaguer than `analysis` — without a specific detail or quote to anchor it, there's more room for borderline judgment calls, so I expect the model to have a slightly harder time with it.

## 3. Balance

No label exceeds 60% of the dataset.

**Why this target:** I set this tighter than the required 70% ceiling because I want a more even split, so the model is forced to actually learn both labels instead of leaning toward whichever one is more common.

## 4. Consistency (self-agreement)

If I relabeled the same 20 cold-labeled posts again later, I'd agree with my original labels at least 70% of the time.

**Why this target:** I picked 70% rather than something closer to 100% because the analysis/reaction boundary is genuinely subjective in places, so I expect some of my own calls to shift on a second pass.

## 5. Accuracy on cold-labeled posts

The model's accuracy on just the 20 cold-labeled posts is at least 0.65.

**Why this target:** These are the posts I labeled without any help or tools, so they're my most honest signal of how I actually apply the taxonomy — if the model struggles here, it's likely struggling with the same ambiguity I do.
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
