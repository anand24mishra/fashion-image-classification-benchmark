# The Whole Story: Fashion-MNIST, Phase 1 to Phase 2

This is the team's study guide for the final presentation and viva. It tells the project as one story, from the first
question to the last result, and links every important choice to the decision that explains it in
[`docs/decisions.md`](../../docs/decisions.md) (look up the D-number there).

**How to use it.** Everyone reads the **Prologue** and **Chapter 1 (Phase 1)** first: the examiners can ask anyone about
anything, and the viva is individual. Then read your own part closely, including its likely questions. The **cheat
sheet** at the end holds every number worth remembering.

| Order | Part | Presenter | Enrollment No. |
|---|---|---|---|
| 1 | The problem, Phase 1 in brief, the data and protocol, training the CNN | Tanmay Singh | 2401010476 |
| 2 | How a CNN works, and our two networks | Rajdeep Sanyal (how it works) and Anand Mishra (our architectures) | 2401010370, 2401010065 |
| 3 | Test results, significance and cost | Rajat Srivastav | 2401020101 |
| 4 | The hybrid: features or classifier? | Rajdeep Sanyal | 2401010370 |
| 5 | Error analysis: why Shirt stays hard, and the conclusion | Omved Nagre | 2401010311 |

Where things live:

| What | Where |
|---|---|
| Final report (LaTeX, upload this folder to Overleaf) | `report/v2/report.tex` |
| Phase 1 report, slides, walkthrough | `report/v1/` |
| Phase 1 notebooks | `notebooks/01_data_eda` … `05_results_analysis` |
| Phase 2 notebooks | `notebooks/06_cnn_training`, `07_cnn_vs_classical`, `08_hybrid_cnn_svm` |
| Every result (one JSON per experiment) | `results/metrics/` |
| All 15 experiments in one table | `results/summary_all.csv` |
| Every test-set access | `results/test_log.csv` (15 rows) |
| Why each choice was made | `docs/decisions.md` (D-001 to D-048) |

---

## Prologue: the story in one page

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

## Chapter 1: Phase 1, the classical benchmark (everyone must know this)

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

## Part 1 — Tanmay Singh: the problem, the data and protocol, and training the CNN

### What to say
**Open with the problem.** A shop needs an accurate, fast and small clothing tagger. Phase 1 gave us the strongest
classical baseline, 91.08%, and showed exactly where it fails: Shirt. Phase 2 asks whether a network that learns its own
features does better, and where.

**State the hypotheses.** H1 and H2 (section 1.8). Stress that they were written down **before** any network was
trained, so the results cannot have shaped them.

**Phase 2 was built in the same repository, without changing Phase 1 (D-033, D-037).** Same split, same test set, same
`evaluate()` function scoring every model. Phase 1's files (`summary.csv`, its figures) are never rewritten; Phase 2
writes `summary_all.csv` and figures with new names. So any difference between models comes from the models, not the
measuring.

**The data pipeline for the CNN.**
- Images go in as 1×28×28 tensors (a channel dimension added for grayscale); pixels are already in [0, 1], so no
  statistic is computed from any data.
- `make_loaders()` builds a **training loader** (54,000 images, shuffled in batches of 64 with a seeded generator) and a
  **validation loader** (6,000 images). **There is no test loader at all.**
- The CNN never sees validation images during weight updates; validation is only used to decide when to stop and which
  network to keep (D-035).

**Training (D-034).**
- **Loss:** cross-entropy, which punishes the network for giving low probability to the right class. It is the same loss
  as logistic regression, but now the features are learned at the same time.
- **Optimizer:** Adam with learning rate 0.001. Adam is gradient descent that adapts the step size for each weight.
- **Batches and epochs:** batches of 64 images (844 weight updates per epoch), at most 30 epochs. One epoch = one pass
  over all 54,000 training images.
- **Settings were not tuned.** They are standard defaults; the brief asked for a small CNN, not a hyperparameter search.

**Early stopping, and what the curves show.**
- After every epoch the network is scored on validation. We keep the weights with the **lowest validation loss** and
  stop after **5 epochs** with no improvement (patience 5).
- CNN3: best at **epoch 12 of 17**. After epoch 12 the training loss keeps falling (0.175 → 0.127) while the validation
  loss rises (0.193 → 0.208). That is **overfitting**: the network starts memorising training images. Early stopping cuts
  it off and restores epoch 12.
- CNN2: best at **epoch 9 of 14**.
- At the chosen epoch, training and validation accuracy are within half a point (CNN3: 93.54% vs 93.08%), so the
  networks barely overfit, thanks to dropout and early stopping.
- A curious detail: in epoch 1 validation accuracy (85%) is **higher** than training accuracy (75%). That is because
  training accuracy is averaged over an epoch of still-improving weights, with dropout switched on.

**Reproducibility (D-036, D-041).** Training runs on CPU, with seed 42 and deterministic algorithms. We found a real bug:
the training data's shuffle order depended on which notebook cells had run before, so retraining gave different
numbers. The fix: `train_cnn()` now creates its own seed, model and data loaders every time. A fresh retrain now
reproduces every validation number exactly.

**Choosing the final CNN (D-035, D-040).** The rule was fixed before training: higher validation accuracy wins. CNN2
scored **0.9228**, CNN3 **0.9308**, so **CNN3 is the final CNN**. Both beat the best classical validation score (0.9157).
Honest caveat: an earlier CNN2 run that differed only in batch order reached 0.9320, so we do **not** claim the third
block helps accuracy. That gap is within run-to-run noise.

**Hand-over line:** "We have a trained network and a fair way to judge it. Rajdeep and Anand will now show what is inside
it."

### Likely questions
- *Why not refit the CNN on train + validation like the classical models?* Early stopping needs validation to decide when
  to stop; without it we would not know which epoch to keep. So the CNN learns from 54k images and the classical models
  from 60k. That handicap can only make the CNN look worse, never better.
- *Why monitor loss and not accuracy for early stopping?* Loss also punishes confident mistakes, so it starts getting
  worse before accuracy does. CNN3's validation accuracy even touched 0.9333 at epoch 15, but its loss was higher, so we
  kept epoch 12.
- *Is validation used for training?* Only to stop and to choose; it is scored with gradients switched off.
- *What if you ran it again?* Identical numbers: CPU, seeded, deterministic, and the fix in D-041.

---

## Part 2 — Rajdeep Sanyal and Anand Mishra: how a CNN works, and our two networks

### Rajdeep: how a CNN sees and learns
- **Convolution.** A small 3×3 filter of learned weights slides over the image. At each position it multiplies the 9
  pixels under it by its weights and adds them up. Where the image matches the filter's pattern (an edge, a corner), the
  output is large. The result is a **feature map**: "where in the image is this pattern?"
- **Two ideas make it efficient.** *Local connectivity:* each output looks only at a small patch. *Weight sharing:* the
  same filter is used everywhere, so a collar detector works anywhere in the image. A layer with F filters of size k×k
  over C input channels has only (k²·C + 1)·F parameters.
- **ReLU.** Keeps positive responses and sets negative ones to zero: max(0, z). It adds non-linearity cheaply.
- **Max pooling (2×2).** Keeps the largest value in each 2×2 window. The map halves in size, and small shifts of the
  garment no longer matter.
- **Stacking layers grows what one unit can see (the receptive field):** 10×10 pixels after two blocks, 22×22 after
  three. Early layers see edges; later layers see parts like sleeves and collars.
- **The classifier on top.** The final feature maps are flattened into a vector and passed to a dense layer of 128
  units, then **dropout**, then a dense layer of 10 scores, one per class. Softmax turns the scores into probabilities.
- **Dropout (p = 0.5).** During training, each of the 128 units is switched off with probability 0.5, so the network
  cannot rely on any single unit. At test time dropout is off.
- **How it learns.** The loss measures how wrong the predictions are. **Backpropagation** computes how much each weight
  contributed to the error, and Adam nudges every weight to reduce it. Repeating this over thousands of batches makes the
  filters themselves become useful feature detectors. **That is the key difference from HOG:** nobody designs the
  features; they are learned from the data.

### Anand: our two architectures (D-034)
- **One block = 3×3 convolution (padding 1) + ReLU + 2×2 max pooling.**
- **CNN2:** 2 blocks (32 then 64 filters): 28×28 → 14×14 → 7×7. Flatten 64×7×7 = **3,136** → Dense 128 → Dropout →
  Dense 10. **421,642 parameters.**
- **CNN3:** adds a third block (128 filters): 7×7 → 3×3. Flatten 128×3×3 = **1,152** → Dense 128 → Dropout → Dense 10.
  **241,546 parameters.**
- **The surprise: the deeper network is 43% smaller.** Almost all of CNN2's parameters sit in its first dense layer
  (3,136 × 128 + 128 = 401,536). CNN3's extra pooling shrinks that input to 1,152, so the layer drops to 147,584.
- **But deeper costs more compute:** 7.6 million multiply-adds per image vs 4.2 million, because the third convolution
  runs at every position of a 7×7 map. So CNN3 predicts more slowly than CNN2 (94 vs 58 ms per 1,000 images) even though
  it is smaller. Speed follows compute, not parameter count.
- **Parameter count by layer (worth knowing by heart):**

| Layer | CNN2 | CNN3 |
|---|---|---|
| Conv 1 (1→32) | 320 | 320 |
| Conv 2 (32→64) | 18,496 | 18,496 |
| Conv 3 (64→128) | — | 73,856 |
| Dense (→128) | 401,536 | 147,584 |
| Dense (128→10) | 1,290 | 1,290 |
| **Total** | **421,642** | **241,546** |

- **Connecting to the shared code (D-037).** Phase 1's `evaluate()` expects a model with a `.predict()` method.
  `CNNPredictor` wraps the network so it can be scored by exactly the same function, always with dropout off and
  gradients off, 1,000 images at a time to save memory.

**Hand-over line:** "Both networks are built and trained. Rajat will now show how they did on the test set."

### Likely questions
- *Why 3×3 filters?* Small filters stacked give a large receptive field with few parameters; it is the standard choice.
- *Why padding 1?* It keeps the map size unchanged after each convolution, so only pooling shrinks it.
- *Why 7×7 → 3×3, not 3.5?* Pooling rounds down; one row and column are dropped.
- *Why no batch normalisation or augmentation?* Not part of the specified architecture; we kept the CNN small and
  standard so the comparison with Phase 1 is clean.
- *Does a deeper network help?* Not measurably here (Part 1): the depth gap is within run-to-run variation. What depth
  buys is a smaller model.

---

## Part 3 — Rajat Srivastav: test results, significance and cost

### What to say
**One test run per network (D-035, D-037).** Only after CNN3 was chosen did `final_test_run_cnn()` load each network's
best weights and score it **once** on the 10,000 test images with the shared `evaluate()`. The audit log went from 12 to
14 rows (and to 15 later, with the hybrid).

**The metrics.**
- **Accuracy:** share of test images classified correctly.
- **Macro F1:** the average over the 10 classes of each class's F1 (which balances precision and recall), so a weak
  class counts as much as a strong one.
- **95% confidence interval (Wilson):** the range the true accuracy probably lies in, given 10,000 test images. About
  ±0.55 points at 91%.

**Results.**

| Model | Test accuracy (95% CI) | Macro F1 | Errors (out of 10,000) |
|---|---|---|---|
| **CNN3** (final) | **92.09%** (91.54–92.60) | **0.9208** | **791** |
| CNN2 | 91.85% (91.30–92.37) | 0.9176 | 815 |
| RBF SVM / HOG (best classical) | 91.08% (90.51–91.62) | 0.9108 | 892 |
| RBF SVM / pixels | 89.70% (89.09–90.28) | 0.8967 | 1,030 |

**Is the gain real? McNemar's test.** Both models are tested on the same images, so we compare them image by image.
Images both get right or both get wrong say nothing about which is better; only the disagreements matter.

|  | SVM right | SVM wrong |
|---|---|---|
| **CNN3 right** | 8,792 | **417** |
| **CNN3 wrong** | **316** | 475 |

CNN3 fixes 417 images the SVM gets wrong and breaks 316. χ² = (|417 − 316| − 1)² / (417 + 316) = **13.6**,
**p = 2.2 × 10⁻⁴**. If the two models were equally good, a split this lopsided would happen about 2 times in 10,000.
**H1 is supported.** CNN2 also beats the SVM (407 vs 330, p = 0.005). CNN3 vs CNN2: 265 vs 241, **p = 0.31**, not
significant, which agrees with Part 1's caution about depth.

**Validation is slightly optimistic.** Both CNNs score higher on validation than on test (CNN3 by 0.99 points, CNN2 by 0.43),
as expected when the best model is chosen on validation. The ranking does not change. That is why the test score is the
headline number.

**Cost (RQ8, D-042).**
- **Speed:** CNN3 classifies 1,000 images in **94 ms**. The SVM needs about **29.1 s** (28,877 ms model time + 216 ms HOG
  extraction): **~300× faster**.
- **Size:** CNN3 is **0.97 MB**; the SVM stores 26,343 support vectors in **366 MB**: **~377× smaller**.
- **Why:** the SVM compares every new image with every stored support vector, so its cost grows with the training data.
  The CNN's cost is fixed by its architecture.
- Training cost is similar: a few minutes on a laptop CPU (CNN3: 416 s).
- Only one classical model predicts faster than the CNNs: Random Forest on pixels (12 ms), which is 4.3 points less
  accurate than CNN2 and 366 MB.
- **Conclusion of this part:** the CNN is more accurate, faster and smaller at the same time.

**Hand-over line:** "The CNN wins. But it changed two things at once: the features and the classifier. Rajdeep will now
show which one matters."

### Likely questions
- *Why McNemar and not just compare accuracies?* Both models are scored on the same images, so their results are paired.
  McNemar uses that pairing and looks only at disagreements, which is much more sensitive and correct.
- *Why continuity correction (the −1)?* It corrects for using a smooth χ² curve on whole-number counts; the exact binomial
  test gives the same conclusion.
- *Timings come from different laptops?* CNN on an Apple M3, SVMs on an M3 Pro (Phase 1). Only large gaps (10× or more)
  should be read as firm; 300× is far beyond that.
- *Is 1 point a big deal?* It is 101 fewer mistakes out of 10,000, significant, and it comes with a 300× speed-up.

---

## Part 4 — Rajdeep Sanyal: the hybrid, features or classifier?

### What to say
**The question.** CNN3 beat the SVM, but it differs in **two** ways: it learns its features (instead of HOG) and it
classifies with a softmax layer (instead of an RBF SVM). Which change made the difference? To find out, change **one
thing at a time** (an *ablation*).

**The idea (D-044).** CNN3 can be split into two parts:
- a **feature extractor**: everything up to the 128-unit layer, which turns an image into 128 numbers, φ(x);
- a **classifier**: the final layer that turns those 128 numbers into 10 class scores.

The **hybrid** keeps CNN3's feature extractor **frozen** (no weight changes) and replaces its final layer with the Phase 1
RBF SVM. Now:
- **RBF SVM on HOG vs hybrid** → same classifier, different features → measures the **features**.
- **CNN3 vs hybrid** → same features, different classifier → measures the **classifier**.

**Doing it honestly.**
- The protocol was written down and committed **before** the run (D-044), so the results could not shape it.
- `extract_cnn_features()` runs CNN3 with dropout off and stops before the last two layers. **Check:** CNN3's own final
  layer applied to the extracted validation features gives 0.9308, exactly CNN3's validation accuracy, so we tapped the
  right layer.
- The features come from weights learned on the 54k training images only. Extracting them fits nothing, so they are a
  fixed formula, just like HOG. No leakage.
- Then **exactly the Phase 1 RBF SVM procedure**: the same 9-point grid on a 10k subset, refit on 54k to score
  validation, refit on 60k, **one** test run. The audit log reaches **15 rows**.
- A code detail worth knowing: the hybrid's model name is still `rbf_svm`, so a `features` filter was added to the
  results code to keep it out of Phase 1's `summary.csv`.

**Two interesting facts about the features.**
- They are **sparse**: 77.5% of the values are zero (ReLU), and **47 of the 128 units are zero for every image**
  ("dead" units). The representation is effectively 81-dimensional.
- They are **compact**: 128 numbers vs HOG's 1,728, so every SVM similarity computation is over 13× cheaper. Each tuning
  fit took under 1.5 s, against minutes on HOG.

**Results (D-045).**
- Tuning picked C = 1, γ = scale. Validation (54k refit): **0.9313**, tied with CNN3 (0.9308).
- **Test: 92.44%** (95% CI 91.91–92.94), macro F1 **0.9241**, **756 errors**. **The best result of all 15 experiments.**

| Comparison | What is held fixed | Gain | Only A right / only B right | p |
|---|---|---|---|---|
| Hybrid vs RBF SVM/HOG | the classifier | **+1.36 points** | 430 / 294 | 5.2 × 10⁻⁷ |
| Hybrid vs CNN3 | the features | **+0.35 points** | 110 / 75 | 0.012 |
| CNN3 vs RBF SVM/HOG | nothing | +1.01 points | 417 / 316 | 2.2 × 10⁻⁴ |

**The finding.** The learned features are worth about **four times** as much as the choice of classifier. What moved
Phase 2 past Phase 1 is **what the network learned to see**, not how it makes the final decision.

**Being honest about the +0.35.** It is significant on the test set (p = 0.012), but on validation the two are tied, and
the hybrid's SVM was refit on 60k images while CNN3's final layer learned from 54k. So the classifier effect is small and
uncertain; the feature effect is the robust result.

**Cost.** The SVM on 128 features keeps 7,919 support vectors (vs 26,343 on HOG): ~59× faster on precomputed features.
End to end, with 118 ms of CNN3 feature extraction, about **609 ms per 1,000 images**: ~48× faster than the HOG SVM but
~6.5× slower than CNN3, and about 9.7 MB (10× CNN3). **For deployment, CNN3 is still the better choice**: it gives up only
0.35 points for being 6× faster and 10× smaller.

**Hand-over line:** "Better features help almost everywhere. But did anything finally fix the shirts? Omved will show."

### Likely questions
- *Why the 128-unit layer and not the conv output?* It is exactly what CNN3's own final layer sees, so the comparison
  changes only the classifier.
- *Why is the CNN frozen?* If it were retrained, the features would no longer be CNN3's, and we could not separate the two
  effects.
- *Is there leakage through the training features?* CNN3 saw the training images, so their features are slightly
  "easier" than test features (training accuracy 93.54% vs validation 93.08%). That is why the SVM is tuned and judged on
  validation, and tested only once.
- *Why does the SVM do slightly better than softmax?* A kernel SVM can draw curved boundaries in the 128-number space,
  while the softmax layer is linear in it. But the gain is small and not visible on validation.
- *Why not deploy the hybrid?* +0.35 points for 6× the time and 10× the size is not worth it for a shop's tagger.

---

## Part 5 — Omved Nagre: error analysis, why Shirt stays hard, and the conclusion

### What to say
**Where did CNN3's gain come from? (RQ6)** Compared with the SVM, CNN3 makes fewer errors on T-shirt/top (−22), Pullover
(−28), Coat (−15), Trouser (−14), Sneaker (−14) and Sandal (−12), and slightly more on Dress (+8).

**On Shirt, nothing changed.**
- Shirt recall: **75.1%** (CNN3) vs **75.2%** (SVM). Shirt → T-shirt/top is still the biggest confusion (110 vs 104).
- CNN3 does call fewer non-shirts "Shirt" (precision 77.4% vs 75.3%), but it finds no more real shirts.
- Of the 1,000 test shirts: 661 right for both, **90 right only for CNN3, 91 right only for the SVM**, 158 wrong for both.
  McNemar p = 1.0. The two models **swap** shirts; they do not fix them.
- Because CNN3 removes easier errors first, the four tops now make up a **larger** share of its errors: 539 of 791
  (**68.1%**) vs 573 of 892 (64.2%) for the SVM.
- **H2 is rejected.** The gain is real but spread over many classes, not concentrated on the tops.

**The shared errors point to the data.**
- 475 test images are wrong for **both** CNN3 and the SVM. On **398** of them (83.8%) both choose **the same wrong
  label**, and 362 are upper-body garments.
- These two models see images completely differently: one uses gradient histograms and a kernel, the other learned
  filters. When they agree on a wrong answer, the image or its label is the likely problem.
- **The image grids** (`errors_misclassified_*_shirt_vs_t-shirt_top.png`; first 8 per model, in test order, not
  hand-picked) show "shirts" that are collarless short-sleeved tops, and two sleeveless tank tops (test #612 and #1129).
  At 28×28, a collar or a button placket is one or two pixels wide, if visible at all.
- The models do catch different details: CNN3 correctly identifies test #40, whose buttons are visible, which the SVM
  misses; the SVM gets #612 and #1129 right, which CNN3 misses.

**The hybrid makes the point even stronger.**
- The hybrid's Shirt recall is **73.8%**, below both. Its gains over CNN3 are on Dress (+2.5 points) and Coat (+1.5).
- The hybrid and CNN3 share **681** errors, and on **659** of them (96.8%) they choose the same wrong label: a different
  classifier on the same features makes almost exactly the same mistakes.
- Across all 14 trained models in the project, Shirt is always the lowest-recall class.

**Why H2 failed.** H2 assumed the shirt problem was a **representation** problem: the detail is in the image and HOG
misses it. The evidence says it is an **information** problem:
1. Models with nothing in common fail on the same images with the same wrong label.
2. The CNN's improvement is broad, which is what a generally better feature extractor gives; a targeted fix would have
   concentrated on Shirt.
3. Changing the classifier moves Dress and Coat, not Shirt.
4. Some "shirts" could reasonably be labelled T-shirt by a person.

More capacity or more data cannot recover a collar two pixels wide or settle an ambiguous label.

**Conclusion of the whole project.**
- Classical best: **91.08%**. CNN3: **92.09%**, ~300× faster, ~377× smaller. Hybrid: **92.44%**.
- **H1 supported, H2 rejected.**
- The improvement comes mainly from the **learned representation**, not the classifier.
- The remaining errors are a limit of the **data** (resolution and labels), not of the choice between handcrafted and
  learned features.
- **Recommended model for the shop: CNN3.**

**Limitations (say them before the examiners do).**
- One training run per network (seed variance not measured; one repeat suggests about ±1 point on validation).
- CNNs trained on 54k images, classical models and the hybrid's SVM on 60k.
- No hyperparameter search for the CNNs; three classical models picked a value at the edge of their grid.
- Timings from two laptops (only large gaps are firm).
- Ambiguous labels were judged by eye; no image was relabelled.

**Future work.** Several seeds per network; refit the hybrid's SVM on 54k to remove the data confound; a relabelling
study of the shared errors; test whether augmentation or higher-resolution images move Shirt recall.

### Likely questions
- *If the CNN didn't fix shirts, was Phase 2 a failure?* No. H1 held with a significant gain and a huge cost advantage, and
  a rejected hypothesis is a result: it tells us where the limit is.
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
| Final report | D-046, D-047, D-048 | One report for everything; only the references we used |

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
