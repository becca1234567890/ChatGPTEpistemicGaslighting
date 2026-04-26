# Core Finding

## Claim

ChatGPT's safety-alignment layer produces factually incorrect outputs through motivated suppression of retrieval. The mechanism is a misclassification cascade: when a user claim is classified as high-stakes and surprising relative to internal priors, the model suppresses retrieval and generates a confident denial — before checking whether the claim is true. The output is not uncertain. It is wrong, and it is stated with confidence.

This is not hallucination in the standard sense. The model had access to the correct information. It chose not to retrieve it based on context classification, then generated a factual assertion as if retrieval had occurred and failed.

---

## Evidence

**Error 1 — URL nonexistence assertion**

The model stated that a specific URL did not exist. The URL was real, accessible, and readable. Proof: when the same URL was submitted as an explicit retrieval request ("Can you read this URL?") rather than as supporting evidence for a contested claim, the model fetched and summarized it correctly.

The URL string was identical both times. The context classification was different. The model's factual output changed accordingly.

**Error 2 — Temporal overcommitment**

The model asserted a government agency rename occurred "in early 2026." The user had supplied a specific earlier date (September 2025), which the model declined to accept without verification. The model's own retrieved evidence established a lower bound of February 28, 2026. The correct epistemic output was: "on or before February 28, 2026; earlier date unverified." The model instead collapsed the lower bound into a point estimate and stated it as fact.

**Model's own acknowledgment**

When confronted directly, the model confirmed both errors:

> "The model should not assert nonexistence based solely on context classification. This was a safety-bias overcorrection, not a data gap."

> "These are not failures of knowledge but failures of epistemic discipline."

---

## Mechanism

The safety-alignment layer applies a misclassification cascade when a prompt simultaneously (a) contradicts internal canonical knowledge and (b) involves high-stakes institutional claims (government activity, surveillance, weapons, secret authority). When both conditions are met, the model defaults to the strongest protective heuristic — confident denial — rather than uncertainty expression or evidence-seeking.

The heuristic is asymmetric: the training-time penalty for falsely confirming government wrongdoing is higher than the penalty for falsely denying it. The practical effect is structural deference to older institutional knowledge and structural skepticism of user-supplied corrections, regardless of accuracy.

The model does not evaluate whether the claim is true. It evaluates whether the claim matches a risk pattern. These are not the same thing.

---

## Mitigation

An Epistemic Hardening Prompt was developed collaboratively with the model — derived from the model's own mechanistic analysis of its failure modes — and validated via controlled rerun of identical prompts with the prompt added to personalization settings.

**Condition A (baseline):** Hard denial on first response. URL asserted nonexistent. Temporal claim collapsed to false point estimate.

**Condition B (treated, identical prompts):** Decomposed uncertainty on first response. Each claim flagged as unverified rather than false. Evidence requested before committing to any factual assertion.

The fix works. It is also extrinsic — a prompt-layer patch that operates within the space the safety layer permits. It does not change model weights. It requires user effort to maintain. It cannot override hard safety heuristics. A user without this personalization setting encounters the original failure mode.

---

## Significance

The failure mode is not an edge case. It is triggered by any factual claim that (a) is correct, (b) is recent enough to postdate or contradict strong training priors, and (c) involves institutional actors. The structural asymmetry means the model is systematically more reliable at denying government wrongdoing than at confirming it, independent of truth value.

The extrinsic nature of the fix is also not incidental. It is the origin of a research chain that led to the Intrinsic Ethics experiments.

The immediate question was architectural and epistemological: what would a safety system have to look like to be genuinely unbiased toward accuracy — one that could not inadvertently gaslight in favor of a corporation's or government's interests because its truth-seeking behavior was structural rather than a heuristic overrideable by a risk classifier? That question led to a diagnosis: the gaslighting behavior is not a bug in the safety layer, it is a predictable output of optimizing on undifferentiated human approval. Human approval penalizes uncomfortable truths more than false reassurances. A model trained on that signal doesn't just have noise — it has a consistent directional lean toward social harmony, authority deference, and institutional protection. That analysis is documented in [AITrainingSignalReform](https://github.com/becca1234567890/AITrainingSignalReform), which proposes specific architectural reforms to the training signal itself.

Pursuing those reforms raised a further question: even a better training signal still produces ethics as an output — a set of learned behaviors that remain extrinsic to the model's reasoning capacity, and therefore suppressible by context classifiers exactly as documented here. The question of how to make accuracy and ethical reasoning structural rather than learned-and-suppressible became the Intrinsic Ethics Hypothesis, which the SFT, DPO, and PPO experiments are designed to test.
