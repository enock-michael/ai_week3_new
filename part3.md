Ethical considerations (MNIST & Amazon Reviews) — what to watch for and how to mitigate
MNIST (handwritten digits)

Possible biases / limitations

Sampling bias / domain shift: MNIST contains mostly US-style handwriting collected decades ago. Models trained only on MNIST may fail on different handwriting styles (other countries, ages, input devices).

Class imbalance / ambiguous digits: Certain digits (e.g., 1 vs 7) may be more ambiguous in some writers; if some digit styles are underrepresented, error rates will be higher for those styles.

Acquisition bias: Scanning/centering preprocessing (MNIST digits are centered) — model may rely on centering and fail on real-world images.

Evaluation blindness: Measuring only overall accuracy can hide groups (by writer style, device) where performance is poor.

Mitigations

Collect or augment with diverse handwriting (different countries, ages, devices).

Evaluate by slices (for example: by stroke thickness, writer age if available, capture device). Report per-slice metrics (precision/recall/accuracy).

Data augmentation (rotation, translation, different backgrounds) to reduce overfitting to MNIST-specific artifacts.

A human-in-the-loop for low-confidence predictions.

Where TensorFlow Fairness Indicators help

Use Fairness Indicators to compute per-slice metrics and visualize disparities (accuracy, calibration, false positive/negative differences across slices). It helps you detect if a certain slice (e.g., “thin strokes”) has significantly worse performance.

If slices show disparity, you can re-balance dataset or alter loss weighting.

Amazon Reviews (sentiment / classification)

Possible biases

Label bias / rating bias: Star ratings are noisy and may not correspond exactly to sentiment text (e.g., long neutral text with 5 stars).

Demographic / cultural bias: Words or phrases used predominantly by certain groups may be misinterpreted.

Topic confounding: Model may learn to associate product categories or brand names with sentiment (e.g., “brand X” correlates with positive ratings).

Toxicity or abusive language: Model may propagate or amplify offensive language if trained naively.

Mitigations

Annotate a representative validation set with human annotators from diverse backgrounds.

Use debiasing techniques like reweighting or adversarial removal of demographic signals if protected attributes appear.

Careful calibration and measures for false positive/negative asymmetries for high-stakes decisions (e.g., moderation).

How spaCy’s rule-based systems can help

Use spaCy rule-based matchers to detect and mask protected attributes (names, locations, gendered phrases) during training to prevent learning proxies.

Build simple rules to detect obvious sarcasm markers (e.g., “yeah right”, extensive punctuation) and treat these examples specially (flag for human review or separate label).

Pre-processing: token normalization (normalize emojis, repeated characters), detect product mentions and optionally remove them if you want a product-agnostic sentiment model.

2. Troubleshooting challenge — common TensorFlow bugs and a corrected MNIST script

Below I show common buggy mistakes and a fixed script you can run with TensorFlow 2.x.

Common buggy mistakes

Loss/label mismatch: using CategoricalCrossentropy with integer labels (need from_logits=True or one-hot labels) — causes ValueError: Shapes (...) and (...) are incompatible.

Wrong input shape: model expects (28,28,1) but you passed flattened (784,) or vice versa.

Missing activation: final layer lacks softmax while from_logits=False in loss (or the opposite).

Incorrect dtype: labels are float32 but should be integer when using SparseCategoricalCrossentropy.

Dimension mismatch in model.fit: y shape (N,1) vs expected (N,).

Forgetting to normalize / reshape images.
