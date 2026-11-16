# The Whole Story: Fashion-MNIST, Phase 1 to Phase 2

This is the team's study guide for the final presentation and viva. It follows the slide deck
[`phase2_presentation.pptx`](phase2_presentation.pptx) slide by slide, explains what each slide shows and what to say,
and links every important choice to the decision that explains it in [`docs/decisions.md`](../../docs/decisions.md)
(look up the D-number there).

**How to use it.** Everyone reads the **Prologue** and **Chapter 1 (Phase 1)** first: the examiners can ask anyone about
anything, and the viva is individual. Then read your own part closely, slide by slide, including its likely questions.
The **cheat sheet** at the end holds every number worth remembering. Each slide heading below matches the slide's title
in the deck, so you can rehearse with the deck open beside this file.

| Order | Part | Slides | Presenter | Enrollment No. |
|---|---|---|---|---|
| — | Opening | 1–2 | Tanmay Singh | 2401010476 |
| 1 | The problem, Phase 1 in brief, the protocol, training the CNN | 3–9 | Tanmay Singh | 2401010476 |
| 2 | How a CNN works, and our two networks | 10–14 | Rajdeep Sanyal (slides 11–12), Anand Mishra (slides 13–14) | 2401010370, 2401010065 |
| 3 | Test results, significance and cost | 15–18 | Rajat Srivastav | 2401020101 |
| 4 | The hybrid: features or classifier? | 19–23 | Rajdeep Sanyal | 2401010370 |
| 5 | Error analysis, conclusion, limits, questions | 24–31 | Omved Nagre | 2401010311 |

Names appear only on the title slide; who presents each slide is recorded here, not on the slides.

## Slide map

| # | Slide title | What is on it | Presenter | Guide section |
|---|---|---|---|---|
| 1 | Does a Small CNN Beat Classical ML? | Title, team, grid of sample images | Tanmay | Prologue |
| 2 | Three questions, three answers | Three cards: 91.08%, 92.09%, +1.36 pts | Tanmay | Prologue |
| 3 | Part 1 divider | What Part 1 covers | Tanmay | Part 1 |
| 4 | Phase 1 left a strong baseline and one weak class | Pixels-vs-HOG bar chart; 91.08% and 75.2% callouts | Tanmay | Part 1 · Slide 4 |
| 5 | Two predictions, written before any network existed | H1 and H2 cards; class-mean images | Tanmay | Part 1 · Slide 5 |
| 6 | Same split, same rules, same scorer | 54k / 6k / 10k split bar; four protocol rules | Tanmay | Part 1 · Slide 6 |
| 7 | Training: standard settings, fully reproducible | Training pipeline; settings chips; the D-041 bug | Tanmay | Part 1 · Slide 7 |
| 8 | Early stopping caught the overfitting | Training curves; three callouts | Tanmay | Part 1 · Slide 8 |
| 9 | Choosing the final network on validation | Validation bar chart; rule and caveat cards | Tanmay | Part 1 · Slide 9 |
| 10 | Part 2 divider | What Part 2 covers | Rajdeep | Part 2 |
| 11 | How a CNN sees: convolution, ReLU, pooling | Filter → feature map → pooling diagram; four term cards | Rajdeep | Part 2 · Slide 11 |
| 12 | Learned, not designed: the key difference from HOG | HOG examples vs the learning loop | Rajdeep | Part 2 · Slide 12 |
| 13 | Our two networks: CNN2 and CNN3 | Layer-by-layer block diagrams | Anand | Part 2 · Slide 13 |
| 14 | Deeper, yet 43% smaller | Stacked parameter chart; params / compute / speed | Anand | Part 2 · Slide 14 |
| 15 | Part 3 divider | What Part 3 covers | Rajat | Part 3 |
| 16 | One test run each: CNN3 is the most accurate model so far | Test-accuracy bar chart; 101 fewer errors, CI, macro-F1 | Rajat | Part 3 · Slide 16 |
| 17 | Is the gain real? Compare image by image | 2×2 agreement table; McNemar p; "H1 supported" | Rajat | Part 3 · Slide 17 |
| 18 | Cheaper by two orders of magnitude | Log-scale speed chart; ~300× and ~377× | Rajat | Part 3 · Slide 18 |
| 19 | Part 4 divider | What Part 4 covers | Rajdeep | Part 4 |
| 20 | CNN3 changed two things at once | Three models as feature + classifier stacks; the two swaps | Rajdeep | Part 4 · Slide 20 |
| 21 | Built honestly, one component at a time | Four method steps; three facts about the features | Rajdeep | Part 4 · Slide 21 |
| 22 | The gain is in the features | +1.36 vs +0.35 chart; 92.44%; caveat | Rajdeep | Part 4 · Slide 22 |
| 23 | Most accurate is not the best to deploy | SVM vs CNN3 vs hybrid: accuracy, time, size | Rajdeep | Part 4 · Slide 23 |
| 24 | Part 5 divider | What Part 5 covers | Omved | Part 5 |
| 25 | The gain is broad; Shirt is the exception | Per-class recall chart for three models | Omved | Part 5 · Slide 25 |
| 26 | The models trade shirts, they don't fix them | CNN3 confusion matrix; 661 / 90 / 91 / 158; "H2 rejected" | Omved | Part 5 · Slide 26 |
| 27 | Shared errors point to the data, not the model | Misclassified-shirt grids; 398 of 475, 659 of 681 | Omved | Part 5 · Slide 27 |
| 28 | Why H2 failed: an information problem | Four evidence cards | Omved | Part 5 · Slide 28 |
| 29 | Conclusion | 91.08% → 92.09% → 92.44%; H1, H2, deploy CNN3 | Omved | Part 5 · Slide 29 |
| 30 | Limits we know about, and what to try next | Limitations and future work | Omved | Part 5 · Slide 30 |
| 31 | Thank you | Numbers to remember; questions | Omved, then all for questions | Part 5 · Slide 31 |

Where things live:

| What | Where |
|---|---|
| Phase 2 slide deck (this guide follows it) | `report/v2/phase2_presentation.pptx` |
| Final report (PDF, and its LaTeX source) | `report/v2/report.pdf`, `report/v2/report.tex` |
| Phase 1 report, slides, walkthrough | `report/v1/` |
| Phase 1 notebooks | `notebooks/01_data_eda` … `05_results_analysis` |
| Phase 2 notebooks | `notebooks/06_cnn_training`, `07_cnn_vs_classical`, `08_hybrid_cnn_svm` |
| Every result (one JSON per experiment) | `results/metrics/` |
| All 15 experiments in one table | `results/summary_all.csv` |
| Every test-set access | `results/test_log.csv` (15 rows) |
| Why each choice was made | `docs/decisions.md` (D-001 to D-050) |

Every slide below ends with a **Speaker notes** block: who presents it and exactly what to say. The same text, word for word, is in the deck's speaker notes. Study the slide sections here; read from the speaker notes while presenting.

---

## Prologue: the story in one page (slides 1–2)

**The question.** An online clothing shop wants to tag product photos automatically. The tagger must be accurate, fast and
small. Deep learning is the default answer today, but we wanted to know three things honestly:

1. How far do **classical** methods with **handcrafted** features get? (Phase 1)
2. Does a small **convolutional neural network (CNN)**, which learns its own features, do better, and **on which
   classes**? (Phase 2)
3. If it does, is the gain in the **features** it learns or in the **classifier** on top? (Phase 2, the hybrid)

**The dataset.** Fashion-MNIST: 70,000 grayscale 28×28 photos of clothing in 10 classes (T-shirt/top, Trouser, Pullover,
Dress, Coat, Sandal, Shirt, Sneaker, Bag, Ankle boot), 60,000 for training and 10,000 for testing.

**The rule that makes the results trustworthy.** Every choice is made on a validation set, and the test set is read
**exactly once per experiment**, by one function, with every read written to an audit log. After 15 experiments the log
has exactly 15 rows.

**The plot, in five beats.**
1. **Phase 1:** the best classical model, an RBF SVM on HOG features, reaches **91.08%**. Every classical model finds
   **Shirt** the hardest class, confusing it with T-shirt/top, Pullover and Coat.
2. **Two predictions, written down before any network existed:** H1, a small CNN beats 91.08%; H2, it wins *mainly by
   fixing the shirts*.
3. **Phase 2:** CNN3 reaches **92.09%**, is about **300× faster** and **377× smaller**, and the gain is statistically
   significant. H1 is true.
4. **But H2 is false:** Shirt recall does not move (75.1% vs 75.2%). The two models swap shirts rather than fix them,
   and most of their remaining mistakes are the same images, with the same wrong label.
5. **The hybrid:** feeding the CNN's learned features into the Phase 1 SVM gives **92.44%**, the best result. It shows
   the improvement comes mainly from the **learned features** (+1.36 points), not the classifier (+0.35). And Shirt
   still is not fixed, which tells us the remaining errors are a limit of the tiny 28×28 images themselves.

**One-sentence conclusion.** Learned features beat handcrafted ones on accuracy, speed and size, but the hardest
mistakes are in the data, not in the model.

---

## Chapter 1: Phase 1, the classical benchmark (everyone must know this; background for slides 4–5)

Phase 1 is the foundation. Phase 2 only makes sense because Phase 1 was done carefully.

### 1.1 Data, checked before use
- The four official files are downloaded over HTTPS and checked against the **MD5 checksums** published by Zalando
  (D-006, D-012). An MD5 checksum is a fingerprint of a file: if one byte changes, the fingerprint changes.
- Automated checks: correct shapes, labels 0–9, exactly 6,000 training and 1,000 test images per class, no missing values
  and **no exact duplicate images** within or across training and test (a duplicate across them would leak answers).

### 1.2 The fixed split (D-003)
- From the 60,000 training images we take a **stratified** validation set of **6,000** (600 per class, seed 42), leaving
  **54,000** for training. "Stratified" means every class keeps the same share.
- The validation indices are **saved in the repository** (`data/splits/val_indices.npy`, MD5 `54ff6b78…`), so every
  person and every phase uses exactly the same split.
- The official 10,000 test images are the final, untouched exam.

### 1.3 The leakage rules (D-004, D-019)
"Leakage" means information from the test set sneaking into a decision, which makes the reported score too good.
1. The test set is read only inside `final_test_run()`, once per experiment, and each read is logged in
   `results/test_log.csv`.
2. Hyperparameters, HOG settings and the final model are chosen **on validation only**.
3. Feature scaling sits **inside** each scikit-learn `Pipeline`, so the scaler learns only from the rows the model
   trains on.
4. Exploratory analysis uses the training split only.
5. Error analysis reads the **saved predictions** (D-011); no model predicts on the test set twice.

### 1.4 What the data told us before training (D-015)
- **PCA:** 84 of 784 components keep 90% of the variance, so raw pixels are very redundant (mostly background).
- **Class-mean images:** the four upper-body garments look almost identical on average (cosine similarity 0.987 for
  Coat–Shirt, 0.984 Pullover–Coat, 0.983 Pullover–Shirt).
- **t-SNE map:** Pullover, Coat and Shirt form one mixed cloud; Trouser and Bag are cleanly separated.
- **Prediction made before any model:** Shirt will be hardest, and most errors will be among the four tops. Both came
  true for every model.

### 1.5 Two ways to describe an image
- **Raw pixels:** the 28×28 image flattened to 784 numbers. Simple, but flattening throws away which pixels are
  neighbours: a garment shifted by two pixels becomes a very different vector.
- **HOG (Histogram of Oriented Gradients):** cut the image into small cells, and in each cell count how strongly edges
  point in each direction. Neighbouring cells are normalised together, so lighting and contrast matter less. The length
  is d = (28/c − b + 1)² · b² · o.
- **HOG sweep (D-016, D-017):** 12 settings tried (orientations 6/9/12, cell 4 or 7 pixels, block 1 or 2 cells), each
  scored on validation with a fast linear SVM. Winner: **12 orientations, 4×4 cells, 2×2 blocks = 1,728 numbers**
  (validation 0.8818). Small cells win by 4–8 points; block normalisation adds 2–3. Extracting HOG costs about
  **216 ms per 1,000 images**.

### 1.6 The models and how they were tuned
| Model | Idea in one line | Chosen setting (pixels / HOG) |
|---|---|---|
| Majority class (baseline) | Always predicts the same class: the floor (10%) | — |
| Logistic Regression | Linear model, one weight per feature per class | C = 0.01 / 0.01 |
| k-NN | Label an image by its k most similar training images | k = 3, distance-weighted |
| Random Forest | Many decision trees vote | 300 trees |
| Linear SVM | Widest straight boundary between classes | C = 0.01 / 0.01 |
| RBF SVM | Curved boundaries using a similarity (kernel) between images | C = 10, γ = 0.001 / scale |

- **Why the RBF SVM was tuned on 10,000 images (D-005, D-027):** kernel SVM training time grows much faster than the
  data, and the laptop has 8 GB of RAM. The 9 grid points were tried on a stratified 10k subset; only the winner was
  trained on all the data.
- **The fairness fix (D-029):** a model tuned on 10k images looks worse on validation than one trained on 54k. So,
  **before choosing the best model**, the RBF SVM's chosen settings were refit on all 54k and scored on validation. With
  that, the ranking is RBF/HOG 0.9157 > RBF/pixels 0.9082 > Logistic Regression/HOG 0.8927.
- **Final step for every model:** refit on all 60,000 (train + validation) and test **once**.

### 1.7 Phase 1 results
| Question | Answer |
|---|---|
| **RQ1:** how good are raw pixels? | Best is RBF SVM, **89.70%**; linear models only 83.78–84.72% |
| **RQ2:** does HOG help? | Yes for linear models (+5.20 points Linear SVM, +4.14 Logistic Regression) and RBF SVM (+1.38 to **91.08%**); no for Random Forest (−0.53) or k-NN (+0.20) |
| **RQ3:** best trade-off? | Random Forest/pixels is fastest (≈12 ms per 1,000 images, but 366 MB); Linear SVM/HOG is the practical choice (88.98%, 181 KB, ≈219 ms with HOG extraction, D-032); RBF/HOG is most accurate but up to 29 s per 1,000 images (26,343 support vectors) |
| **RQ4:** which classes fail? | **Shirt** is the lowest class for every model (50.9–75.2% recall); confusions among the four tops are 56–66% of every model's errors |

- **Selected model:** RBF SVM on HOG, chosen on validation (0.9157), **test 91.08%** (95% CI 90.51–91.62).
- **Is HOG really better than pixels for the SVM?** McNemar test: HOG fixes 515 images the pixel model gets wrong and
  breaks 377; p = 4.5 × 10⁻⁶, so yes.
- **Stability:** 5-fold cross-validation on a 10k subset gives 0.8775 ± 0.0054.
- **Phase 1's last finding:** the two best models share 515 errors, 391 with the same wrong label. A first hint that
  some errors live in the images themselves.

### 1.8 How Phase 1 set up Phase 2
Phase 1 ended with a precise target and two falsifiable predictions:
- **H1 (accuracy):** a small CNN trained under the same rules beats 91.08%.
- **H2 (mechanism):** it wins *chiefly* by fixing the confusions among T-shirt/top, Pullover, Coat and Shirt, because
  learned filters can see collars, buttons and cuffs that fixed edge histograms miss.

---

## Opening — slides 1 and 2 (Tanmay Singh)

### Slide 1 · Does a Small CNN Beat Classical ML?
**On the slide:** the title, the question "is the gain in the features or the classifier?", the five team members with
enrollment numbers, and a grid of ten training images per class.

**What to say:** A shop needs an accurate, fast and small clothing tagger. Phase 1 gave us the strongest classical answer.
Phase 2 asks whether a small network that learns its own features does better, where it does better, and why.

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> Good morning. We are Tanmay Singh, Anand Mishra, Rajdeep Sanyal, Rajat Srivastav and Omved Nagre.
>
> Picture an online clothing shop that wants to tag product photos automatically. The tagger must be accurate, fast and small.
>
> In Phase 1 we built the strongest classical answer. In Phase 2 we ask: does a small CNN that learns its own features do better, where does it do better, and why?
>
> These are real Fashion-MNIST images: 28 by 28 grayscale, ten clothing classes.

### Slide 2 · Three questions, three answers
**On the slide:** three cards, one per question, each with its headline number: **91.08%** (best classical),
**92.09%** (CNN3), **+1.36 points** (learned features, against +0.35 from the classifier). Tagline: learned features
win on accuracy, speed and size, but the hardest errors live in the data.

**What to say:** Give the three answers up front (the Prologue's five beats). Everything after this slide shows how we
got them honestly.

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> Here is the whole story in three answers.
>
> One: handcrafted features go far. An RBF SVM on HOG features reaches 91.08%, the best of 12 classical experiments.
>
> Two: a small CNN does better. CNN3 reaches 92.09%, a statistically significant gain, and it is about 300 times faster and 377 times smaller. But it does not fix the hardest class, Shirt.
>
> Three: the gain comes from the features. Learned features add 1.36 points; swapping the classifier adds only 0.35.
>
> The rest of the talk shows how we got each number honestly.

---

## Part 1 — slides 3 to 9: the problem, the protocol and training the CNN (Tanmay Singh)

### Slide 3 · Part 1 divider
**On the slide:** the five topics of Part 1.

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> Part 1 sets the stage: Phase 1 in one slide, the two predictions we wrote down before training, the evaluation rules, how we trained the network, and how we chose CNN3.

### Slide 4 · Phase 1 left a strong baseline and one weak class
**On the slide:** a bar chart of test accuracy for the five classical models on pixels (grey) and HOG (terracotta), and
two callouts: **91.08%** (RBF SVM on HOG, chosen on validation, 0.9157) and **75.2%** (Shirt recall, the lowest class
for every classical model).

**What to say:**
- HOG helps the linear models most (+5.20 points for the Linear SVM, +4.14 for Logistic Regression) and lifts the RBF
  SVM by +1.38 to 91.08%. It does not help Random Forest or k-NN (Chapter 1, 1.7).
- The weak spot is **Shirt**: 50.9–75.2% recall across models, confused with T-shirt/top, Pullover and Coat.

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> Phase 1 compared classical models on raw pixels and on HOG, a handcrafted description of edge directions.
>
> HOG helps the linear models most: plus 5.20 points for the linear SVM, plus 4.14 for logistic regression. It lifts the RBF SVM by 1.38 points to 91.08%, our best classical model, chosen on validation at 0.9157. It does not help Random Forest or k-NN.
>
> The weak spot is Shirt: recall between 50.9% and 75.2% across models. Shirts get confused with T-shirt/top, Pullover and Coat.

### Slide 5 · Two predictions, written before any network existed
**On the slide:** two cards, H1 (accuracy) and H2 (mechanism), and the class-mean images showing that the four tops look
almost identical on average.

**What to say:**
- **H1:** a small CNN trained under the same rules beats 91.08%.
- **H2:** it wins *mainly* by fixing the confusion among T-shirt/top, Pullover, Coat and Shirt, because learned filters
  can see collars, buttons and cuffs that fixed edge histograms miss.
- Both were written down **before** any network was trained, so the results cannot have shaped them (Chapter 1, 1.8).
- The class-mean images (cosine similarity 0.98 or more among the tops) are why we expected the errors there (D-015).

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> Before training any network, we wrote down two predictions.
>
> H1 is about accuracy: a small CNN trained under the same rules beats 91.08%.
>
> H2 is about mechanism: it wins mainly by fixing the confusion among the four tops, T-shirt/top, Pullover, Coat and Shirt, because learned filters can see collars, buttons and cuffs that edge histograms miss.
>
> These class-mean images show why we expected that: on average the four tops look almost identical, with cosine similarity 0.98 or more.
>
> Because both predictions were written first, the results could not shape them.

### Slide 6 · Same split, same rules, same scorer
**On the slide:** a bar split into 54,000 train, 6,000 validation and 10,000 test (test shaded as locked), and four rules
with icons.

**What to say:**
- **Same split** as Phase 1 (D-003). Only the 54,000 training images ever update weights.
- **Test set read once per experiment**, only by `final_test_run_cnn()`, and every read is logged: 15 experiments,
  15 rows in `results/test_log.csv`. **There is no test DataLoader at all** (D-035).
- **Every choice on validation:** early stopping, CNN2 vs CNN3, the hybrid's settings.
- **One `evaluate()` for all 15 models:** `CNNPredictor` wraps the network so Phase 1's unchanged scoring code can call
  `.predict()`, always with dropout and gradients off (D-037).
- **Phase 1 outputs never rewritten** (D-033, D-037): Phase 2 writes `summary_all.csv` and new figure names. So any
  difference between models comes from the models, not the measuring.

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> To compare fairly, we kept everything from Phase 1.
>
> Same split: 54,000 training images, 6,000 validation, 10,000 test. Only the training images ever update the weights.
>
> The test set is read once per experiment, only through final_test_run_cnn, and every read is logged: 15 experiments, 15 rows in the test log. There is no test data loader at all.
>
> Every choice is made on validation: early stopping, CNN2 versus CNN3, and the hybrid's settings.
>
> One evaluate function scores all 15 models. A small wrapper lets the network plug into Phase 1's unchanged scoring code, always with dropout off.
>
> So any difference between models comes from the models, not from the measuring.

### Slide 7 · Training: standard settings, fully reproducible
**On the slide:** a five-step pipeline (image → mini-batch → loss → optimizer → early stop), five settings chips, and a
card on the reproducibility bug.

**What to say:**
- Images go in as 1×28×28 tensors; pixels are already in [0, 1], so no statistic is computed from any data.
- `make_loaders()` builds a training loader (54,000 images, shuffled in batches of 64 with a seeded generator, so 844
  weight updates per epoch) and a validation loader (6,000).
- **Loss:** cross-entropy, which punishes low probability on the right class: the same loss as logistic regression, but
  now the features are learned at the same time. **Optimizer:** Adam, learning rate 0.001 (gradient descent that
  adapts the step size per weight). At most 30 epochs; dropout 0.5.
- **Settings were not tuned** (D-034): the brief asked for a small standard CNN, not a hyperparameter search.
- **The bug (D-041):** the shuffle order depended on which notebook cells had run before, so retraining gave different
  numbers. Now `train_cnn()` creates its own seed, model and loaders every time; on CPU with deterministic algorithms
  (D-036), a fresh retrain reproduces every validation number exactly.

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> Each image goes in as a 1 by 28 by 28 tensor. Pixels are already between 0 and 1, so we compute no statistics from the data.
>
> We train in shuffled mini-batches of 64, which is 844 weight updates per epoch.
>
> The loss is cross-entropy, the same loss as logistic regression, except that the features are now learned at the same time. The optimizer is Adam with learning rate 0.001. At most 30 epochs, dropout 0.5.
>
> We did not tune these settings: the brief asked for a small standard CNN, not a search.
>
> We also found and fixed a reproducibility bug: the shuffle order depended on which notebook cells had run before. Now every training run creates its own seed, model and loaders, and a fresh retrain reproduces every validation number exactly.

### Slide 8 · Early stopping caught the overfitting
**On the slide:** the training curves for both networks (loss on top, accuracy below; dashed line at the kept epoch) and
three callouts.

**What to say:**
- After every epoch the network is scored on validation. We keep the weights with the **lowest validation loss** and stop
  after **5 epochs** without improvement (patience 5).
- CNN3 is best at **epoch 12 of 17**; CNN2 at **epoch 9 of 14**.
- After epoch 12, CNN3's training loss keeps falling (0.175 → 0.127) while validation loss rises (0.193 → 0.208):
  **overfitting**, the network starting to memorise. Early stopping restores epoch 12.
- At the kept epoch, train and validation accuracy are within half a point (93.54% vs 93.08%).
- Curious detail: in epoch 1 validation accuracy (85%) beats training accuracy (75%), because training accuracy is
  averaged over an epoch of still-improving weights with dropout switched on.

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> After every epoch we score the network on validation. We keep the weights with the lowest validation loss and stop after 5 epochs without improvement.
>
> CNN3 is best at epoch 12 of 17; CNN2 at epoch 9 of 14.
>
> Look at CNN3 after epoch 12: training loss keeps falling, from 0.175 to 0.127, while validation loss rises, from 0.193 to 0.208. That is overfitting, the network starting to memorise. Early stopping restores epoch 12.
>
> At the kept epoch, training and validation accuracy are within half a point: 93.54% versus 93.08%.

### Slide 9 · Choosing the final network on validation
**On the slide:** a bar chart of validation accuracy (RBF SVM/HOG 0.9157, CNN2 0.9228, CNN3 0.9308), a card with the
selection rule and a caveat card.

**What to say:**
- The rule was fixed before training: higher validation accuracy wins, ties to fewer parameters. **CNN3 is the final
  CNN** (D-035, D-040). Both networks beat the best classical validation score.
- **Honest caveat:** an earlier CNN2 run that differed only in batch order reached 0.9320. So we do **not** claim the
  third block helps accuracy; that gap is within run-to-run noise. What depth buys is a smaller model (slide 14).

**Speaker notes** (word for word in the deck) · **Presenter:** Tanmay Singh

> The selection rule was fixed before training: higher validation accuracy wins, ties go to fewer parameters.
>
> CNN2 scores 0.9228 and CNN3 0.9308, both above the best classical model at 0.9157. So CNN3 is our final network.
>
> One honest caveat: an earlier CNN2 run that differed only in batch order reached 0.9320. So we do not claim the third block improves accuracy; that gap is within run-to-run noise. What depth buys is a smaller model, as slide 14 shows.
>
> We have a trained network and a fair way to judge it. Next, Rajdeep explains what is inside it.

### Likely questions (Part 1)
- *Why not refit the CNN on train + validation like the classical models?* Early stopping needs validation to decide when
  to stop. So the CNN learns from 54k images and the classical models from 60k. That handicap can only make the CNN look
  worse, never better.
- *Why monitor loss and not accuracy for early stopping?* Loss also punishes confident mistakes, so it starts getting
  worse before accuracy does. CNN3's validation accuracy even touched 0.9333 at epoch 15, but its loss was higher, so we
  kept epoch 12.
- *Is validation used for training?* Only to stop and to choose; it is scored with gradients switched off.
- *What if you ran it again?* Identical numbers: CPU, seeded, deterministic, and the fix in D-041.

---

## Part 2 — slides 10 to 14: how a CNN works, and our two networks (Rajdeep Sanyal, Anand Mishra)

### Slide 10 · Part 2 divider
**On the slide:** the four topics of Part 2. Rajdeep presents slides 11–12 (how a CNN works); Anand presents slides 13–14 (our two architectures).

**Speaker notes** (word for word in the deck) · **Presenter:** Rajdeep Sanyal

> Part 2 opens the network up. I will explain how a CNN sees and how it learns its own features. Anand will then walk through our two networks, CNN2 and CNN3, and why the deeper one is smaller.

### Slide 11 · How a CNN sees: convolution, ReLU, pooling (Rajdeep)
**On the slide:** a diagram of an image patch with a 3×3 filter window, the resulting feature map, and a 2×2 max-pooled
output; four cards define convolution, ReLU, pooling and weight sharing.

**What to say:**
- **Convolution.** A small 3×3 filter of learned weights slides over the image. At each position it multiplies the 9
  pixels under it by its weights and adds them up. Where the image matches the filter's pattern (an edge, a corner), the
  output is large. The result is a **feature map**: "where in the image is this pattern?"
- **Local connectivity and weight sharing.** Each output looks only at a small patch, and the same filter is used
  everywhere, so a collar detector works anywhere in the image. A layer with F filters of size k×k over C input channels
  has only (k²·C + 1)·F parameters.
- **ReLU** keeps positive responses and sets negative ones to zero: max(0, z), a cheap non-linearity.
- **Max pooling (2×2)** keeps the largest value in each 2×2 window: the map halves, and small shifts no longer matter.
- **Stacking grows the receptive field:** 10×10 pixels after two blocks, 22×22 after three. Early layers see edges;
  later layers see parts like sleeves and collars.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajdeep Sanyal

> Convolution: a small 3 by 3 filter of learned weights slides over the image. At each position it multiplies the 9 pixels under it by its weights and adds them up. Where the image matches the pattern, such as an edge, the output is large. The result is a feature map: where in the image is this pattern?
>
> The same filter is used everywhere. That is weight sharing: a collar detector works anywhere in the image, with very few parameters.
>
> ReLU keeps positive responses and sets negative ones to zero.
>
> Max pooling keeps the largest value in each 2 by 2 window. The map halves, and small shifts stop mattering.
>
> Stacking blocks grows what each unit sees: 10 by 10 pixels after two blocks, 22 by 22 after three. Early layers see edges; later layers see sleeves and collars.

### Slide 12 · Learned, not designed: the key difference from HOG (Rajdeep)
**On the slide:** left, HOG visualisations of sample images ("designed by people"); right, the four-step learning loop
(predict → loss → backprop → Adam step).

**What to say:**
- HOG is a fixed recipe: edge directions in 4×4 cells, 1,728 numbers, the same for every dataset.
- A CNN learns its filters. The final feature maps are flattened, passed to a dense layer of 128 units, then dropout,
  then 10 class scores; softmax turns them into probabilities.
- **How it learns:** the loss measures how wrong the predictions are; **backpropagation** computes each weight's share
  of the error; Adam nudges every weight to reduce it. Repeating this over 844 batches per epoch turns the filters
  themselves into feature detectors. Nobody designs them.
- **Dropout (p = 0.5):** during training each of the 128 units is switched off with probability 0.5, so the network
  cannot rely on any single unit. At test time dropout is off.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajdeep Sanyal

> This is the key difference from Phase 1. HOG is a fixed recipe designed by people: edge directions in 4 by 4 cells, 1,728 numbers, the same for every dataset.
>
> A CNN learns its filters. The final feature maps are flattened into a dense layer of 128 units, then dropout, then 10 class scores.
>
> How it learns: the network predicts, the loss measures how wrong it is, backpropagation computes each weight's share of the error, and Adam nudges every weight to reduce it. Repeat this over 844 batches per epoch and the filters become feature detectors. Nobody designs them.
>
> Dropout switches off each of the 128 units with probability 0.5 during training, so the network cannot rely on any single unit. At test time it is off.
>
> Now Anand will show our two networks.

### Slide 13 · Our two networks: CNN2 and CNN3 (Anand)
**On the slide:** two block diagrams, layer by layer, with tensor shapes; conv blocks in orange, the 128-unit feature
layer in green.

**What to say (D-034):**
- **One block = 3×3 convolution (padding 1) + ReLU + 2×2 max pooling.** Padding keeps the size; only pooling halves it.
- **CNN2:** 2 blocks (32 then 64 filters): 28×28 → 14×14 → 7×7. Flatten 64×7×7 = **3,136** → Dense 128 → Dropout →
  Dense 10.
- **CNN3:** adds a third block (128 filters): 7×7 → 3×3 (pooling rounds down). Flatten 128×3×3 = **1,152** → Dense 128
  → Dropout → Dense 10.
- The green layer is **φ(x)**: the 128 numbers CNN3 computes for each image. Part 4's hybrid taps exactly here.

**Speaker notes** (word for word in the deck) · **Presenter:** Anand Mishra

> Each block is a 3 by 3 convolution with padding 1, then ReLU, then 2 by 2 max pooling. Padding keeps the size; only pooling halves it.
>
> CNN2 has two blocks, 32 then 64 filters: 28 by 28 becomes 14 by 14, then 7 by 7. Flattened, that is 3,136 numbers, then a dense layer of 128, dropout, and 10 outputs.
>
> CNN3 adds a third block with 128 filters: 7 by 7 becomes 3 by 3, because pooling rounds down. Flattened, that is only 1,152 numbers, then the same dense layer of 128, dropout and 10 outputs.
>
> The green layer is phi of x: the 128 numbers CNN3 computes for each image. Part 4's hybrid taps exactly here.

### Slide 14 · Deeper, yet 43% smaller (Anand)
**On the slide:** a stacked bar chart of parameters by layer (conv layers, the first dense layer, the output layer), and
three CNN2-vs-CNN3 rows: parameters, compute per image, prediction time.

**What to say:**
- **The surprise: the deeper network is 43% smaller** (241,546 vs 421,642 parameters). Almost all of CNN2's parameters
  sit in its first dense layer (3,136 × 128 + 128 = 401,536). CNN3's extra pooling shrinks that input to 1,152, so the
  layer drops to 147,584.
- **But deeper costs more compute:** 7.60 million multiply-adds per image vs 4.24 million, because the third convolution
  runs at every position of a 7×7 map. So CNN3 predicts more slowly (94 vs 58 ms per 1,000 images). Speed follows
  compute, not parameter count.

| Layer | CNN2 | CNN3 |
|---|---|---|
| Conv 1 (1→32) | 320 | 320 |
| Conv 2 (32→64) | 18,496 | 18,496 |
| Conv 3 (64→128) | — | 73,856 |
| Dense (→128) | 401,536 | 147,584 |
| Dense (128→10) | 1,290 | 1,290 |
| **Total** | **421,642** | **241,546** |

**Speaker notes** (word for word in the deck) · **Presenter:** Anand Mishra

> Here is the surprise: the deeper network is 43% smaller, 241,546 parameters against 421,642.
>
> Almost all of CNN2's parameters sit in its first dense layer: 3,136 times 128, plus 128, which is 401,536. CNN3's extra pooling shrinks that input to 1,152, so the same layer drops to 147,584.
>
> But deeper costs more compute: 7.60 million multiply-adds per image against 4.24 million, because the third convolution runs over a 7 by 7 map. So CNN3 predicts more slowly: 94 against 58 milliseconds per 1,000 images.
>
> Speed follows compute, not parameter count.
>
> Both networks are built and trained. Rajat will now show how they did on the test set.

### Likely questions (Part 2)
- *Why 3×3 filters?* Small filters stacked give a large receptive field with few parameters; it is the standard choice.
- *Why padding 1?* It keeps the map size unchanged after each convolution, so only pooling shrinks it.
- *Why 7×7 → 3×3, not 3.5?* Pooling rounds down; one row and column are dropped.
- *Why no batch normalisation or augmentation?* Not part of the specified architecture; we kept the CNN small and
  standard so the comparison with Phase 1 is clean.
- *Does a deeper network help?* Not measurably here (slide 9): the depth gap is within run-to-run variation. What depth
  buys is a smaller model.

---

## Part 3 — slides 15 to 18: test results, significance and cost (Rajat Srivastav)

### Slide 15 · Part 3 divider
**On the slide:** the three topics of Part 3.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajat Srivastav

> Part 3 is the test: one test run per network, whether the gain is statistically real, and what each model costs in speed and size.

### Slide 16 · One test run each: CNN3 is the most accurate model so far
**On the slide:** a bar chart of test accuracy (RBF SVM/pixels 89.70, RBF SVM/HOG 91.08, CNN2 91.85, CNN3 92.09) and three
callouts: **101** fewer errors, **±0.55 points** (95% Wilson interval), **0.9208** macro-F1.

**What to say:**
- Only after CNN3 was chosen did `final_test_run_cnn()` score each network **once** on the 10,000 test images with the
  shared `evaluate()` (D-035, D-037). The audit log went from 12 to 14 rows (15 later, with the hybrid).
- **Accuracy** is the share of test images classified correctly. **Macro-F1** averages each class's F1 (which balances
  precision and recall), so a weak class counts as much as a strong one. The **95% Wilson interval** is the range the
  true accuracy probably lies in, about ±0.55 points at 91% with 10,000 images.

| Model | Test accuracy (95% CI) | Macro-F1 | Errors |
|---|---|---|---|
| **CNN3** (final) | **92.09%** (91.54–92.60) | **0.9208** | **791** |
| CNN2 | 91.85% (91.30–92.37) | 0.9176 | 815 |
| RBF SVM / HOG (best classical) | 91.08% (90.51–91.62) | 0.9108 | 892 |
| RBF SVM / pixels | 89.70% (89.09–90.28) | 0.8967 | 1,030 |

- **Validation is slightly optimistic:** both CNNs score higher on validation than on test (CNN3 by 0.99 points, CNN2 by
  0.43), as expected when the best model is chosen on validation. The ranking does not change; the test score is the
  headline.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajat Srivastav

> Only after CNN3 was chosen did we score each network once on the 10,000 test images, with the same evaluate function as Phase 1.
>
> CNN3 reaches 92.09% and CNN2 91.85%, against 91.08% for the best classical model and 89.70% for the SVM on raw pixels.
>
> That is 101 fewer errors than the SVM: 791 against 892.
>
> The 95% Wilson interval is about plus or minus 0.55 points: the range the true accuracy probably lies in.
>
> Macro-F1 is 0.9208. It averages F1 over the ten classes, so a weak class counts as much as a strong one.
>
> Both CNNs score a little lower on test than on validation, as expected when you pick the best model on validation. The ranking does not change.

### Slide 17 · Is the gain real? Compare image by image
**On the slide:** the 2×2 agreement table (8,792 both right, 417 only CNN3, 316 only the SVM, 475 both wrong), the χ²
calculation, **p = 2.2 × 10⁻⁴**, and the badge "H1 supported".

**What to say:**
- Both models are tested on the same images, so we compare them image by image. Images both get right or both get wrong
  say nothing about which is better; only the **733 disagreements** matter. That is **McNemar's test**.
- χ² = (|417 − 316| − 1)² / (417 + 316) = **13.6**, **p = 2.2 × 10⁻⁴**: if the two models were equally good, a split
  this lopsided would happen about 2 times in 10,000. **H1 is supported.**
- CNN2 also beats the SVM (407 vs 330, p = 0.005). CNN3 vs CNN2: 265 vs 241, **p = 0.31**, not significant, which agrees
  with slide 9's caution about depth.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajat Srivastav

> Both models are tested on the same images, so we compare them image by image.
>
> 8,792 images both get right and 475 both get wrong. Those say nothing about which model is better. Only the 733 disagreements matter: 417 only CNN3 gets right, 316 only the SVM gets right. That is McNemar's test.
>
> Chi-squared is 417 minus 316, minus 1, squared, over 733: that is 13.6, and p is 2.2 times 10 to the minus 4. If the two models were equally good, a split this lopsided would happen about 2 times in 10,000.
>
> So H1 is supported.
>
> CNN3 against CNN2 is not significant, p 0.31, which agrees with our caution about depth.

### Slide 18 · Cheaper by two orders of magnitude
**On the slide:** a log-scale bar chart of end-to-end prediction time per 1,000 images (RBF SVM/HOG 29,093 ms, Linear
SVM/HOG 219, CNN3 94, CNN2 58) and two callouts: **~300× faster**, **~377× smaller**.

**What to say (RQ8, D-042):**
- **Speed:** CNN3 classifies 1,000 images in **94 ms**; the SVM needs about **29.1 s** (28,877 ms model time + 216 ms
  HOG extraction).
- **Size:** CNN3 is **0.97 MB**; the SVM stores 26,343 support vectors in **366 MB**.
- **Why:** the SVM compares every new image with every stored support vector, so its cost grows with the training data.
  The CNN's cost is fixed by its architecture.
- Training cost is similar: a few minutes on a laptop CPU (CNN3: 416 s).
- Only two classical models predict faster than the CNNs, Random Forest on pixels (12 ms) and the Linear SVM on pixels
  (1.2 ms), and they are 4.3 and 8.1 points less accurate than CNN2.

matters."

**Speaker notes** (word for word in the deck) · **Presenter:** Rajat Srivastav

> The CNN is also far cheaper. Note the log scale.
>
> CNN3 classifies 1,000 images in 94 milliseconds. The SVM needs about 29.1 seconds, including HOG extraction. That is about 300 times faster.
>
> CNN3 is 0.97 MB; the SVM stores 26,343 support vectors in 366 MB. That is about 377 times smaller.
>
> Why: the SVM compares every new image with every stored support vector, so its cost grows with the training data. The CNN's cost is fixed by its architecture.
>
> So the CNN wins. But it changed two things at once, the features and the classifier. Rajdeep will show which one matters.

### Likely questions (Part 3)
- *Why McNemar and not just compare accuracies?* The results are paired (same images). McNemar uses that pairing and looks
  only at disagreements, which is more sensitive and correct.
- *Why the continuity correction (the −1)?* It corrects for using a smooth χ² curve on whole-number counts; the exact
  binomial test gives the same conclusion.
- *Timings from different laptops?* CNN on an Apple M3, SVMs on an M3 Pro (Phase 1). Only large gaps (10× or more)
  should be read as firm; 300× is far beyond that.
- *Is 1 point a big deal?* It is 101 fewer mistakes out of 10,000, significant, and it comes with a 300× speed-up.

---

## Part 4 — slides 19 to 23: the hybrid, features or classifier? (Rajdeep Sanyal)

### Slide 19 · Part 4 divider
**On the slide:** the four topics of Part 4.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajdeep Sanyal

> Part 4 asks one question: is CNN3's gain in the features it learned, or in the classifier at the end? We answer it with an ablation, changing one component at a time, and then ask which model we would actually deploy.

### Slide 20 · CNN3 changed two things at once
**On the slide:** three models drawn as a feature box over a classifier box: RBF SVM/HOG (HOG → RBF SVM, 91.08%), the
hybrid (CNN3 features → RBF SVM, 92.44%) and CNN3 (CNN3 features → softmax, 92.09%). Double arrows mark the two swaps.

**What to say:**
- CNN3 beat the SVM, but it differs in **two** ways: it learns its features (instead of HOG) and it classifies with a
  softmax layer (instead of an RBF SVM). Which change made the difference? Change **one thing at a time**: an
  *ablation*.
- CNN3 splits into a **feature extractor** (everything up to the 128-unit layer: an image becomes 128 numbers, φ(x)) and
  a **classifier** (the final layer: 128 numbers become 10 class scores).
- The **hybrid** keeps CNN3's feature extractor **frozen** and replaces its final layer with the Phase 1 RBF SVM (D-044).
- **RBF SVM/HOG vs hybrid:** same classifier, different features, so it measures the **features**.
- **Hybrid vs CNN3:** same features, different classifier, so it measures the **classifier**.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajdeep Sanyal

> CNN3 beat the SVM, but it differs in two ways: it learns its features instead of using HOG, and it classifies with a softmax layer instead of an RBF SVM.
>
> So we split CNN3 in two. The feature extractor turns an image into 128 numbers. The classifier turns those 128 numbers into 10 class scores.
>
> The hybrid keeps CNN3's feature extractor frozen and puts the Phase 1 RBF SVM on top.
>
> Now each pair changes one thing. SVM on HOG versus the hybrid: same classifier, different features, so it measures the features. Hybrid versus CNN3: same features, different classifier, so it measures the classifier.

### Slide 21 · Built honestly, one component at a time
**On the slide:** four numbered method steps and a card with three facts about CNN3's features: **128** numbers,
**77.5%** zeros, **< 1.5 s** per tuning fit.

**What to say:**
1. The protocol was written down and committed **before** the run (D-044), so the results could not shape it.
2. `extract_cnn_features()` runs CNN3 with dropout off and stops before the last two layers. **Check:** CNN3's own final
   layer on the extracted validation features gives 0.9308, exactly CNN3's validation accuracy, so we tapped the right
   layer.
3. Then **exactly the Phase 1 RBF SVM procedure**: the same 9-point grid on a 10k subset, refit on 54k to score
   validation, refit on 60k, **one** test run.
4. The audit log reaches **15 rows**. No leakage: the features come from weights learned on the 54k training images only,
   and extracting them fits nothing, so they are a fixed formula like HOG.

- **Sparse:** 77.5% of the values are zero (ReLU), and **47 of the 128 units are zero for every image** ("dead"
  units), so the representation is effectively 81-dimensional. That is also why γ = scale works out to 1/81.
- **Compact:** 128 numbers vs HOG's 1,728, so every SVM similarity computation is over 13× cheaper; each tuning fit took
  under 1.5 s, against minutes on HOG.
- Code detail: the hybrid's model name is still `rbf_svm`, so a `features` filter keeps it out of Phase 1's
  `summary.csv`.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajdeep Sanyal

> We built it one step at a time.
>
> One: we committed the protocol before the run, so the results could not shape it.
>
> Two: we extract CNN3's 128 features with dropout off. As a check, CNN3's own final layer on these features gives 0.9308, exactly CNN3's validation accuracy, so we tapped the right layer.
>
> Three: exactly the Phase 1 SVM procedure: the same 9-point grid on a 10k subset, refit on 54k to score validation, refit on 60k, one test run.
>
> Four: the audit log reaches 15 rows, with no leakage.
>
> The features are compact and sparse: 128 numbers against HOG's 1,728, and 77.5% of the values are zero. So each tuning fit took under 1.5 seconds.

### Slide 22 · The gain is in the features
**On the slide:** a bar chart of the two effects (+1.36 and +0.35 points, with p-values), the hybrid's **92.44%**, the
"≈ 4×" callout and a caveat card.

**What to say (D-045):**
- Tuning picked C = 1, γ = scale. Validation (54k refit): **0.9313**, tied with CNN3 (0.9308).
- **Test: 92.44%** (95% CI 91.91–92.94), macro-F1 **0.9241**, **756 errors**: the best of all 15 experiments.

| Comparison | Held fixed | Gain | Only A right / only B right | p |
|---|---|---|---|---|
| Hybrid vs RBF SVM/HOG | the classifier | **+1.36 points** | 430 / 294 | 5.2 × 10⁻⁷ |
| Hybrid vs CNN3 | the features | **+0.35 points** | 110 / 75 | 0.012 |
| CNN3 vs RBF SVM/HOG | nothing | +1.01 points | 417 / 316 | 2.2 × 10⁻⁴ |

- **The finding:** the learned features are worth about **four times** as much as the choice of classifier. What moved
  Phase 2 past Phase 1 is **what the network learned to see**, not how it makes the final decision.
- **The +0.35 is fragile:** significant on test (p = 0.012), but tied on validation, and the hybrid's SVM was refit on 60k
  images while CNN3's final layer learned from 54k. The feature effect is the robust result.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajdeep Sanyal

> Tuning picked C equals 1 and gamma equals scale. On validation the hybrid ties CNN3: 0.9313 against 0.9308.
>
> On test it reaches 92.44%, 756 errors, the best of all 15 experiments.
>
> Now the decomposition. Changing the features, with the classifier fixed, adds 1.36 points, p 5.2 times 10 to the minus 7. Changing the classifier, with the features fixed, adds 0.35 points, p 0.012.
>
> So the learned features are worth about four times as much as the classifier. What moved Phase 2 past Phase 1 is what the network learned to see.
>
> One caveat: the 0.35 is fragile. It is a tie on validation, and the hybrid's SVM learned from 60k images while CNN3 learned from 54k. The feature effect is the robust result.

### Slide 23 · Most accurate is not the best to deploy
**On the slide:** three columns, RBF SVM/HOG vs CNN3 (highlighted, "recommended") vs the hybrid, with accuracy, time per
1,000 images and model size.

**What to say:**
- The hybrid's SVM keeps 7,919 support vectors (vs 26,343 on HOG): ~59× faster on precomputed features. End to end, with
  118 ms of CNN3 feature extraction, about **609 ms per 1,000 images**: ~48× faster than the HOG SVM, but ~6.5× slower
  than CNN3, and about 9.7 MB (10× CNN3).
- **For deployment, CNN3 is the better choice:** it gives up only 0.35 points for being ~6.5× faster and 10× smaller.

**Speaker notes** (word for word in the deck) · **Presenter:** Rajdeep Sanyal

> Most accurate is not the same as best to deploy.
>
> The hybrid needs about 609 milliseconds per 1,000 images, including CNN feature extraction, and about 9.7 MB. That is 48 times faster than the HOG SVM, but 6.5 times slower than CNN3 and 10 times larger.
>
> CNN3 gives up only 0.35 points for that. So for the shop, we recommend CNN3.
>
> Better features help almost everywhere. But did anything finally fix the shirts? Omved will answer that.

### Likely questions (Part 4)
- *Why the 128-unit layer and not the conv output?* It is exactly what CNN3's own final layer sees, so the comparison
  changes only the classifier.
- *Why is the CNN frozen?* If it were retrained, the features would no longer be CNN3's, and we could not separate the two
  effects.
- *Is there leakage through the training features?* CNN3 saw the training images, so their features are slightly
  "easier" than test features (training accuracy 93.54% vs validation 93.08%). That is why the SVM is tuned and judged on
  validation, and tested only once.
- *Why does the SVM do slightly better than softmax?* A kernel SVM can draw curved boundaries in the 128-number space,
  while the softmax layer is linear in it. But the gain is small and not visible on validation.
- *An earlier paper found an SVM output layer worse than softmax on Fashion-MNIST. Contradiction?* No. There the SVM was
  linear and trained jointly with the network, so the features changed too; here it is a kernel SVM on frozen features.
  In both cases the classifier effect is about a point or less (report, Discussion).

---

## Part 5 — slides 24 to 31: why Shirt stays hard, conclusion and limits (Omved Nagre)

### Slide 24 · Part 5 divider
**On the slide:** the four topics of Part 5.

**Speaker notes** (word for word in the deck) · **Presenter:** Omved Nagre

> Part 5 looks at the errors: where the gain came from, what happened to Shirt, and what the shared mistakes tell us. Then our conclusion and limits.

### Slide 25 · The gain is broad; Shirt is the exception
**On the slide:** a grouped bar chart of test recall for all ten classes (RBF SVM/HOG, CNN3, hybrid), a Shirt-recall card
(75.2 / 75.1 / 73.8%) and the per-class change in CNN3's errors.

**What to say (RQ6):**
- Compared with the SVM, CNN3 makes fewer errors on Pullover (−28), T-shirt/top (−22), Coat (−15), Trouser (−14),
  Sneaker (−14) and Sandal (−12), and slightly more on Dress (+8). Shirt: +1.
- Every class moves up except Shirt. Across all 14 trained models in the project, Shirt is always the lowest-recall
  class.

**Speaker notes** (word for word in the deck) · **Presenter:** Omved Nagre

> This chart shows test recall for every class and all three models.
>
> Compared with the SVM, CNN3 makes fewer errors on Pullover, 28 fewer, T-shirt/top, 22 fewer, and Coat, 15 fewer, and also on Trouser, Sneaker and Sandal. Dress gets slightly worse, 8 more errors.
>
> Every class improves except Shirt: 75.2% for the SVM, 75.1% for CNN3, 73.8% for the hybrid.
>
> Across all 14 trained models in the project, Shirt is always the lowest class.

### Slide 26 · The models trade shirts, they don't fix them
**On the slide:** CNN3's row-normalised confusion matrix, four tiles for the 1,000 test shirts (661 / 90 / 91 / 158) and
the badge "H2 rejected".

**What to say:**
- Shirt recall: **75.1%** (CNN3) vs **75.2%** (SVM). Shirt → T-shirt/top is still the biggest confusion (110 vs 104).
- CNN3 calls fewer non-shirts "Shirt" (precision 77.4% vs 75.3%) but finds no more real shirts.
- Of the 1,000 test shirts: 661 right for both, **90 right only for CNN3, 91 right only for the SVM**, 158 wrong for both.
  McNemar p = 1.0: the models **swap** shirts; they do not fix them.
- Because CNN3 removes easier errors first, the four tops make up a **larger** share of its errors: 539 of 791 (68.1%) vs
  573 of 892 (64.2%).
- **H2 is rejected.** The gain is real but spread across classes, not concentrated on the tops.

**Speaker notes** (word for word in the deck) · **Presenter:** Omved Nagre

> Shirt recall is 75.1% for CNN3 against 75.2% for the SVM. Shirt to T-shirt/top is still the biggest confusion.
>
> Look at the 1,000 test shirts. 661 are right for both models and 158 are wrong for both. 90 are right only for CNN3, and 91 right only for the SVM.
>
> McNemar p is 1.0: the models swap shirts, they do not fix them.
>
> And because CNN3 removes easier errors first, the four tops are a larger share of its errors: 68.1% against 64.2%.
>
> So H2 is rejected. The gain is real, but it is spread across classes, not concentrated on the tops.

### Slide 27 · Shared errors point to the data, not the model
**On the slide:** the first eight test shirts each model calls T-shirt/top (SVM on top, CNN3 below) and three callouts:
**398 of 475**, **659 of 681**, **1–2 px**.

**What to say:**
- 475 test images are wrong for **both** CNN3 and the SVM; on **398** (83.8%) both choose **the same wrong label**, and
  362 are upper-body garments. These models see images completely differently (gradient histograms and a kernel vs learned
  filters). When they agree on a wrong answer, the image or its label is the likely problem.
- The grids (`errors_misclassified_*_shirt_vs_t-shirt_top.png`, first 8 in test order, not hand-picked) show "shirts" that
  are collarless short-sleeved tops and two sleeveless tank tops (#612 and #1129). At 28×28 a collar or button placket is
  one or two pixels wide, if visible at all.
- The models catch different details: CNN3 gets #40 (buttons visible), which the SVM misses; the SVM gets #612 and #1129,
  which CNN3 misses.
- **The hybrid makes the point stronger:** its Shirt recall is 73.8%, its gains over CNN3 are on Dress (+2.5) and Coat
  (+1.5), and it shares **681** errors with CNN3, **659** (96.8%) with the same wrong label: a different classifier on
  the same features makes almost exactly the same mistakes.

**Speaker notes** (word for word in the deck) · **Presenter:** Omved Nagre

> 475 test images are wrong for both CNN3 and the SVM. On 398 of them, 83.8%, both choose the same wrong label.
>
> These models see images in completely different ways. When they agree on a wrong answer, the image or its label is the likely problem.
>
> These are the first eight shirts each model calls T-shirt/top, not hand-picked. Many are collarless short-sleeved tops, and two are sleeveless tank tops. At 28 by 28, a collar or a button placket is one or two pixels wide, if visible at all.
>
> The hybrid makes the point stronger: it shares 681 errors with CNN3, and 659 of them, 96.8%, have the same wrong label. A different classifier on the same features makes almost the same mistakes.

### Slide 28 · Why H2 failed: an information problem
**On the slide:** four evidence cards with icons.

**What to say:** H2 assumed the shirt problem was a **representation** problem: the detail is in the image and HOG misses
it. The evidence says it is an **information** problem:
1. **Different eyes, same mistakes:** models with nothing in common fail on the same images with the same wrong label.
2. **The gain is broad:** that is what a generally better feature extractor gives; a targeted fix would have concentrated
   on Shirt.
3. **The classifier moves other classes:** swapping softmax for an SVM moves Dress and Coat, not Shirt.
4. **Some labels are ambiguous:** some "shirts" could reasonably be labelled T-shirt by a person.

More capacity or data can sharpen boundaries, but it cannot recover a collar two pixels wide or settle an ambiguous
label. Say clearly that this is an inference from shared errors, not a measured ceiling.

**Speaker notes** (word for word in the deck) · **Presenter:** Omved Nagre

> H2 assumed the shirt problem was a representation problem: the detail is in the image and HOG misses it. The evidence says it is an information problem.
>
> One: different eyes, same mistakes. Unrelated models fail on the same images with the same wrong label.
>
> Two: the gain is broad, which is what a generally better feature extractor gives. A targeted fix would have concentrated on Shirt.
>
> Three: swapping the classifier moves Dress and Coat, not Shirt.
>
> Four: some labels are ambiguous. A person could reasonably call some of these shirts T-shirts.
>
> More capacity cannot recover a collar two pixels wide. To be clear, this is an inference from shared errors, not a measured ceiling.

### Slide 29 · Conclusion
**On the slide:** the headline "Learned features beat handcrafted ones; the hardest errors live in the data", the
progression **91.08% → 92.09% → 92.44%**, and three badges: H1 supported, H2 rejected, deploy CNN3.

**What to say:**
- Classical best 91.08%. CNN3 92.09%, ~300× faster, ~377× smaller. Hybrid 92.44%.
- **H1 supported, H2 rejected.** The improvement comes mainly from the **learned representation**, not the classifier.
- The remaining errors point to the **data** (resolution and labels), not to the choice between handcrafted and learned
  features.
- **Recommended model for the shop: CNN3.**

**Speaker notes** (word for word in the deck) · **Presenter:** Omved Nagre

> To conclude. The best classical model reaches 91.08%. CNN3 reaches 92.09% while being about 300 times faster and 377 times smaller. The hybrid reaches 92.44%.
>
> H1 is supported; H2 is rejected.
>
> The improvement comes mainly from the learned representation, not the classifier.
>
> The remaining errors point to the data, its resolution and its labels, not to the choice between handcrafted and learned features.
>
> Our recommendation for the shop is CNN3.

### Slide 30 · Limits we know about, and what to try next
**On the slide:** two cards, limitations and future work.

**What to say (say these before the examiners do):**
- One training run per network (seed variance not measured; one repeat suggests about ±1 point on validation).
- CNNs trained on 54k images; classical models and the hybrid's SVM on 60k.
- No hyperparameter search for the CNNs, so results are lower bounds; three classical models picked a value at the edge
  of their grid.
- Timings from two laptops (only large gaps are firm). Ambiguous labels were judged by eye; no image was relabelled.
- **Future work:** several seeds per network; refit the hybrid's SVM on 54k to remove the data confound; a relabelling
  study of the shared errors; test whether augmentation or higher-resolution images move Shirt recall.

**Speaker notes** (word for word in the deck) · **Presenter:** Omved Nagre

> We want to name our limits before you do.
>
> One training run per network, so seed variance is not measured.
>
> The CNNs learned from 54k images; the classical models and the hybrid's SVM from 60k.
>
> No hyperparameter search for the CNNs, so their results are lower bounds.
>
> Timings come from two laptops, so only large gaps are firm. And ambiguous labels were judged by eye; we relabelled nothing.
>
> Next we would train several seeds per network, refit the hybrid's SVM on 54k, run a relabelling study of the shared errors, and test whether augmentation or higher resolution moves Shirt recall.

### Slide 31 · Thank you
**On the slide:** "Thank you", an invitation to questions, and a card of numbers to remember (all from the cheat sheet
below). Everyone takes questions; the viva is individual.

**Speaker notes** (word for word in the deck) · **Presenter:** Omved Nagre; all members take questions

> Thank you. The code, results, test-set audit log and decision log are all in our repository.
>
> We are happy to take your questions.

### Likely questions (Part 5)
- *If the CNN didn't fix shirts, was Phase 2 a failure?* No. H1 held with a significant gain and a huge cost advantage,
  and a rejected hypothesis is a result: it tells us where the limit is.
- *How do you know the labels are ambiguous?* Two independent models agree on the same wrong label for 398 images, and the
  grids show collarless and sleeveless "shirts". It is strong evidence, not proof; we did not relabel images.
- *Would a bigger network fix it?* It would sharpen boundaries elsewhere, but it cannot see a collar that is not in the
  pixels.

---

## Decision map: which decision answers which question

| Topic | Decisions | In one line |
|---|---|---|
| Stack and structure | D-001, D-002, D-007, D-009 | Python + scikit-learn locally; logic in `src/`, thin notebooks; constants in `config.py`; results committed |
| Data integrity | D-006, D-012, D-014 | Official files over HTTPS, checked by MD5; loaded straight from the .gz files |
| Split | D-003 | Stratified 54k/6k split with committed indices |
| Leakage controls | D-004, D-019, D-011 | Pipelines, one test function, test features built only there, predictions saved |
| Tuning approach | D-005, D-018 | Single validation set; strict "highest validation accuracy" rule |
| EDA | D-015 | Train only; PCA and t-SNE |
| HOG | D-016, D-017 | Sweep scored with a fast linear SVM; 12 orientations, 4×4 cells, 2×2 blocks |
| Logistic Regression | D-020, D-022, D-023, D-024 | Convergence budget, weight maps, timings excluded as not comparable |
| SVMs | D-026, D-027 | LinearSVC solver; RBF SVM tuned on a 10k subset, refit on 60k |
| Fair best-model choice | D-029, D-030 | Refit RBF on 54k before choosing; ranking rules |
| Error analysis and speed | D-032 | Saved predictions only; end-to-end time includes HOG |
| Phase 2 setup | D-033, D-039 | Same repository, append-only; PyTorch on CPU as the only new dependency |
| CNN design and training | D-034, D-036 | CNN2/CNN3 layers and fixed settings; CPU, seeded, deterministic |
| CNN data and choice | D-035, D-040 | Train on 54k only; CNN3 chosen on validation; depth gap within noise |
| Reproducibility bug | D-041 | `train_cnn()` builds its own seed, model and loaders |
| Shared evaluation | D-037, D-038 | CNN scored by the unchanged `evaluate()`; Phase 1 outputs protected |
| CNN vs classical analysis | D-042 | Models compared, example pairs, end-to-end time |
| t-SNE figure | D-043 | Slightly different after installing PyTorch; committed figure kept |
| Hybrid | D-044, D-045 | Protocol fixed before the run; 92.44%, mostly a features effect |
| Final report | D-046, D-047, D-048, D-049 | One report for everything; cites only the three works it builds on (Fashion-MNIST, the CNN–SVM paper, our Phase 1 report) |
| Slides and this guide | D-050 | A native PowerPoint deck; this guide follows it slide by slide |

---

## Cheat sheet: numbers to remember

| Fact | Number |
|---|---|
| Images | 70,000 (60,000 train, 10,000 test), 28×28, 10 classes |
| Split | 54,000 train / 6,000 validation / 10,000 test |
| Experiments / test-log rows | 15 / 15 |
| HOG length | 1,728 (12 orientations, 4×4 cells, 2×2 blocks); 216 ms per 1,000 images |
| Best classical (RBF SVM / HOG) | val 0.9157, test **91.08%**, 892 errors, 366 MB, ~29.1 s per 1,000 |
| CNN2 | 421,642 params, best epoch 9/14, val 0.9228, test 91.85% |
| CNN3 (final) | 241,546 params, best epoch 12/17, val 0.9308, test **92.09%**, 791 errors, 94 ms, 0.97 MB |
| CNN3 vs SVM | 417 vs 316, χ² 13.6, p = 2.2 × 10⁻⁴; ~300× faster, ~377× smaller |
| CNN3 vs CNN2 | 265 vs 241, p = 0.31 (not significant) |
| Hybrid (RBF SVM on CNN3 features) | val 0.9313, test **92.44%**, 756 errors, ~609 ms, ~9.7 MB |
| Features vs classifier | +1.36 points (p = 5.2 × 10⁻⁷) vs +0.35 points (p = 0.012) |
| Shirt recall | SVM 75.2%, CNN3 75.1%, hybrid 73.8% |
| Shared errors | CNN3 & SVM: 475 (398 same label); hybrid & CNN3: 681 (659 same label) |
| Tops' share of errors | SVM 64.2%, CNN3 68.1%, hybrid 68.9% |
| CNN features | 128 numbers, 77.5% zeros, 47 dead units |

## Glossary
- **Validation set:** data held out from training to make choices; never the test set.
- **Leakage:** test information influencing a decision, which inflates the score.
- **HOG:** a fixed feature that counts edge directions in small cells.
- **Kernel / RBF SVM:** an SVM that measures similarity between images to draw curved boundaries; prediction compares a new
  image with every stored support vector.
- **Convolution / filter / feature map:** a small learned pattern slid over the image; the map shows where it appears.
- **Pooling:** keeping the largest value in each small window; halves the size, tolerates small shifts.
- **Receptive field:** the patch of the input one unit can see.
- **Dropout:** randomly switching off units during training so the network cannot rely on any one of them.
- **Epoch:** one pass over all training images.
- **Early stopping:** keep the weights with the lowest validation loss; stop when it stops improving.
- **Overfitting:** training loss keeps falling while validation loss rises; the model memorises.
- **Macro F1:** average of per-class F1, every class weighted equally.
- **Wilson interval:** a 95% range for the true accuracy.
- **McNemar test:** compares two models on the same test images using only the images where they disagree.
- **Ablation:** changing one component at a time to see what it contributes.
- **Frozen network:** a trained network whose weights are no longer updated; it acts as a fixed feature formula.
