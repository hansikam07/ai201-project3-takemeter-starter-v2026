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

Community: r/books (Reddit)

Candidate distinctions noticed while reading:
1. Some posts name multiple books; most don't.
2. Some posts discuss reading in general, not a specific book; most don't.
3. Some posts give specific reasons for their opinion (what made them stop reading, a particular flaw); most just state a reaction/verdict with no backup.


---

## Label Taxonomy

### analysis
Definition: Gives a specific reason, detail, scene, or quote from the book to support its opinion or review.
Example 1: "I DNF'd Pachinko" post — points to specific issues like lack of character depth and time-skips.
Example 2: "Dead Weight by Hildur Knútsdóttir" review — discusses specific character details, plot structure, and genre comparisons (e.g. to Glass Houses by Madeline Ashby).

### reaction
Definition: States an opinion, feeling, or verdict about a book with no specific reasoning behind it.
Example 1: "The Moustache" post — hypes the book with vibes and rhetorical questions, no specific craft reasoning.
Example 2: "Ann Patchett" appreciation post — lists many books and feelings about them, but no specific reasoning for any one book.

### The hardest boundary
Which two labels: analysis vs. reaction
Decision rule: If the post explains the reason for the reader's feelings or review of the book with specific details, scenes, or quotes, it's analysis — even if the post is also emotional or opinion-driven.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

**Counts per label:**

| Label | Count | Share |
|---|---|---|
|  |  |  |
|  |  |  |
|  |  |  |
| **Total** |  | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**2.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**3.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max_length 128, seed 42. I trained on `mps` (the Apple Silicon GPU), with torch 2.14.1.

**Anything I changed from the defaults, and why:** Nothing. I left every setting at the notebook's default.

**Split sizes:** train 23, val 5, test 5. That's the standard 70/15/15 stratified split applied to my 33 total rows.

| label | train | val | test |
|---|---|---|---|
| analysis | 14 | 3 | 3 |
| reaction | 9 | 2 | 2 |

Both labels ended up with fewer than 8 examples in the test split (3 for analysis, 2 for reaction), so I'm under the notebook's own threshold for a result I can trust. With only 5 posts in the whole test set, getting even one of them wrong swings accuracy by 20 points, and that's basically what happened: macro F1 came out to 0.375, with analysis scoring 0.75 F1 and reaction scoring 0.00. The model just predicted analysis for every test post. I don't think that means the model can't learn the boundary between these two labels. It means my dataset, at 33 rows total and well short of the 150 to 200 target, is too small for this particular split to give a stable answer.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.600 | 0.600 | +0.000 |
| Macro F1 | 0.583 | 0.375 | -0.208 |
| F1 - `analysis` | 0.500 | 0.750 | +0.250 |
| F1 - `reaction` | 0.667 | 0.000 | -0.667 |

Both models were scored on the same 5 held-out posts in test_split.csv (3 analysis, 2 reaction). The baseline is facebook/bart-large-mnli, zero-shot, using the definitions in label_definitions.txt. The trained model is distilbert-base-uncased, seed 42.

**What I predicted before I looked:** I did not record a prediction before seeing the numbers, so I have none to report.

**What the gap actually means:** Fine-tuning added nothing on accuracy: both models got 3 of 5 right. The trained model was worse on macro F1 (0.375 vs 0.583) because it predicted `analysis` for every test post, which gives it F1 0.000 on `reaction`. The baseline caught both `reaction` posts but missed 2 of 3 `analysis` posts. With only 5 test posts, a single prediction moves accuracy by 20 points, so this comparison is weak evidence either way.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

Device: mps (Apple Silicon GPU), torch 2.14.1. Training: 3 epochs.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | >= 0.73 | 0.600 | 0.600 | 0.800 | MISSED |
| 2. Per-label F1 | analysis >= 0.70 and reaction >= 0.65 | analysis 0.750 / reaction 0.000 | analysis 0.750 / reaction 0.000 | analysis 0.857 / reaction 0.667 | MISSED |
| 3. Balance | no label > 60% | analysis 60.6% | analysis 60.6% | analysis 60.6% | MISSED |
| 4. Self-agreement on 20 cold posts | >= 70% | not measured | not measured | not measured | MISSED (not measured) |
| 5. Model accuracy on 20 cold posts | >= 0.65 | not measured | not measured | not measured | MISSED (not measured) |

Balance is a property of the whole dataset (20 analysis, 13 reaction out of 33), so it is the same on every seed.

### Confusion matrix

Seed 42. Seed 7 gave the same matrix.

| true \ predicted | analysis | reaction |
|---|---|---|
| **analysis** | 3 | 0 |
| **reaction** | 2 | 0 |

**My biggest off-diagonal number, and what it means:** 2 real reaction posts were called analysis, and 0 analysis posts were called reaction. On seeds 42 and 7 the model predicted analysis for every test post, so it never learned the reaction side of the boundary and leaned toward the majority label.



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | >= 0.73 | MISSED | Seeds gave 0.600, 0.600, 0.800. The target has to hold on all three seeds, and only one is above it, and that one is still above only because of a lucky split. |
| 2 | Per-label F1 | analysis >= 0.70, reaction >= 0.65 | MISSED | Reaction F1 was 0.000 on seeds 42 and 7 and 0.667 on seed 2024. Analysis F1 held (0.750, 0.750, 0.857), but reaction missed on two of three seeds. |
| 3 | Balance | no label > 60% | MISSED | 20 analysis out of 33 rows is 60.6%, just over the 60% ceiling. |
| 4 | Self-agreement on 20 cold posts | >= 70% | MISSED (not measured) | I never re-labeled a set of 20 cold posts, and nothing in labels.csv marks which posts were cold, so no number exists. |
| 5 | Model accuracy on 20 cold posts | >= 0.65 | MISSED (not measured) | No cold subset is identified in labels.csv, so accuracy on it can't be computed. |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Criteria 1 and 2 (accuracy and reaction F1): too few examples, and a very small test set.** The dataset is 33 rows, well short of the 150-200 target, and the test split is 5 posts (3 analysis, 2 reaction), so one prediction moves accuracy by 20 points. Evidence: the three seeds ranged from 0.600 to 0.800, a 20-point spread. On seeds 42 and 7 the confusion matrix shows the model predicted analysis for every test post (2 reaction posts called analysis, 0 correct), so it never learned reaction. Reaction had only 9 training rows. It is not a training-time problem: raising epochs from 3 to 10 gave identical numbers on all three seeds (see The Improvement). And it is not an impossible task, because the zero-shot baseline with no training got reaction F1 0.667 on the same posts. I have not yet checked whether my labels were applied inconsistently at the analysis/reaction boundary, because I don't have the staff comparison yet, so I can't rule that out.

**Criterion 3 (balance): dataset composition.** 20 of 33 posts are analysis (60.6%), a miss of 0.6 percentage points. It matters because the model leaned toward the majority label (analysis) on two of three seeds.

**Criteria 4 and 5 (cold-post criteria): the criteria couldn't be measured from my records.** I think I labeled about 20 posts cold, but labels.csv marks every row as ai-prelabeled-reviewed and has no column identifying the cold ones. The criteria depend on a group I can't identify from the data, so I could not compute either number. I did not revise these criteria in criteria.md.

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

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
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:** One setting: epochs, from 3 to 10 (EPOCHS = 10 in the notebook, passed into train_model by the section 6 three-seed loop). I changed nothing else: same labels, same data, same learning rate (2e-5), same batch size (16), same seeds. The results JSON files don't record the epoch count, so I confirmed the setting from the notebook code.

**Which diagnosis pointed at it:** On seeds 42 and 7 the model predicted analysis for every test post, giving reaction F1 0.000. With 23 training rows and batch size 16, 3 epochs is only about 6 optimizer steps, so I suspected the model simply hadn't trained long enough to separate reaction from analysis. Of my diagnoses, this was the only one a training setting could address. I did not change labels or data because those are frozen this unit.

### Run Log — After

Device: mps (Apple Silicon GPU), torch 2.14.1. Training settings: 10 epochs.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | >= 0.73 | 0.600 | 0.600 | 0.800 | MISSED |
| 2. Per-label F1 | analysis >= 0.70 and reaction >= 0.65 | analysis 0.750 / reaction 0.000 | analysis 0.750 / reaction 0.000 | analysis 0.857 / reaction 0.667 | MISSED |
| 3. Balance | no label > 60% | analysis 60.6% | analysis 60.6% | analysis 60.6% | MISSED |
| 4. Self-agreement on 20 cold posts | >= 70% | not measured | not measured | not measured | MISSED (not measured) |
| 5. Model accuracy on 20 cold posts | >= 0.65 | not measured | not measured | not measured | MISSED (not measured) |

The confusion matrix for seed 42 was identical to the before run (analysis row 3/0, reaction row 2/0).

**Did it help, and how do I know:** No. Accuracy, macro F1, and per-label F1 were identical to the 3-epoch run on all three seeds (accuracy 0.600, 0.600, 0.800; reaction F1 0.000, 0.000, 0.667). So training time was not the bottleneck: the model reached the same predictions by epoch 3, and more epochs changed nothing. That points back at the data (only 9 reaction training rows and 5 test posts) rather than training. With a 5-post test set, a small real change could also be invisible, so I can't rule out a tiny effect. I'd try adding reaction examples next, but I did not do that this unit.

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## Stretch

Stretch features I am adding: (1) a confidence check, (2) a per-label error write-up for reaction, the worst label.

### Confidence check

From results.json (seed 42, 3 epochs trained model):
- mean_when_right: 0.553
- mean_when_wrong: 0.548
- accuracy_most_confident_third: 1.000
- accuracy_least_confident_third: 0.000
- n_per_third: 1

The model's average confidence was nearly identical when right (0.553) and when wrong (0.548), so its confidence did not meaningfully separate right from wrong answers. The confident-third vs unsure-third accuracy (1.000 vs 0.000) rests on 1 post per third, so it is not reliable evidence.

### Per-label error write-up for reaction

Reaction is the worst label (F1 0.000). Both reaction test posts were predicted as analysis. Reading the two posts from baseline_results.json (gold = reaction):

**Post 1:** "In the Shadow of Eight of Collin Gosselin: thoughts and feelings. Just finished this memoir, narrated by Collin himself. It was absolutely devastating. I don't usually cry at books but I cried a couple times. I will no longer be supporting TLC by watching their shows."

**Post 2:** "Only a week until the new Vollmann! The new Vollmann, a table for fortune, drops next week on four volumes and 3k pages. Should get it the day after my birthday, excited! I pre-ordered for $140 a few months ago, it hits at $200 even."

Both posts are first-person and emotional. Both mention concrete details (a named memoir "Collin Gosselin" and a TV show "TLC" in post 1; a named author "Vollmann", specific count "four volumes and 3k pages", and prices "$140" and "$200" in post 2). The details are present but not used to argue anything — no reasoning is given. This analysis is based on 2 posts.

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

- **Criterion 1 (accuracy >= 0.73) and criterion 2 (reaction F1 >= 0.65):** Still missed. Next I would add more reaction posts (I only have 13 in total, 9 in training) and enlarge the test split so one post doesn't swing accuracy by 20 points. I stopped because labels and data are frozen this unit, and I ran out of time.
- **Criterion 3 (balance, no label > 60%):** Still missed at 60.6% analysis (20 of 33). Adding about 3 more reaction posts would fix it. I stopped for the same reason: the data is frozen this unit.
- **Criteria 4 and 5 (cold-post criteria):** Still not measured. I would mark the cold-labeled posts in labels.csv, or re-label a fresh set of 20 without AI help, and then compute both numbers. I stopped because I can't identify which posts were cold.
- **Agreement report:** Not done yet. I still need the staff labels from my TF, and I haven't run the agreement check, so I haven't tested whether I applied the analysis/reaction boundary consistently.

**The gap between what I meant my labels to capture and what the model learned:** I meant analysis to mean "gives a specific reason, detail, scene, or quote" and reaction to mean "a feeling or verdict with no specific reasoning." The confusion matrix shows the model learned neither side of that boundary on seeds 42 and 7: it called every test post analysis, including both reaction posts (2 reaction posts called analysis, 0 correct). It most likely learned "analysis is the common label" rather than the specific-detail rule, because it had only 9 reaction examples to learn from.

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
