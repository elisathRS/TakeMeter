# TakeMeter

## Project Overview

TakeMeter is a text classification project for the subreddit **r/TrueFilm**. It labels discussion posts into three categories:

- `analytical_critique`
- `subjective_take`
- `narrative_decoding`

The goal is to distinguish posts that focus on technical film analysis, personal opinion, and plot/story explanation.

## Taxonomy

### analytical_critique
- Definition: The post evaluates a film by analyzing specific cinematic techniques (for example, cinematography, editing, sound design, mise-en-scène, or historical/contextual meaning).
- Example 1: "Villeneuve's use of brutalist architecture in Dune isn't just an aesthetic choice; it visually reinforces the colonial oppression of the Harkonnens by making the scale of the environments completely swallow the human form."
- Example 2: "If you track the blocking in the dinner scene of Parasite, Bong Joon-ho consistently places the Kim family physically lower in the frame than the Park family, a literal visual framing of class stratification."

### subjective_take
- Definition: The post focuses primarily on the author's personal emotional reaction or opinion about the film's quality or entertainment value.
- Example 1: "Am I the only one who found Oppenheimer incredibly boring? The pacing was completely frantic in the first hour and it just felt like a three-hour movie trailer."
- Example 2: "Interstellar is Nolan's absolute masterpiece. No other movie has ever made me cry so hard while simultaneously blowing my mind with its sci-fi concepts."

### narrative_decoding
- Definition: The post focuses on plot points, character motivations, symbolism tied to story events, or ending explanations rather than filmmaking technique.
- Example 1: "In the ending of Inception, it doesn't actually matter if the top keeps spinning or falls, because Cobb walks away from it, showing he finally accepts his reality with his kids."
- Example 2: "Can someone explain why the bear scene in Annihilation happened? Was the bear actually mutated by the Shimmer, or was it carrying the consciousness of the person it killed?"

## Dataset and Labeling

- Source: manually collected posts from **r/TrueFilm**.
- Scope: discussion posts only, not comments, with enough text to support one of the three categories.
- Labeling process: each post is read, assigned to the single label that best describes the post's dominant rhetorical intent, and reviewed against the taxonomy definitions.
- Balance goal: roughly equal representation across labels to avoid majority bias.

### Label distribution

The dataset is balanced across all three labels:

- `analytical_critique`: 67 examples (33.3%)
- `subjective_take`: 67 examples (33.3%)
- `narrative_decoding`: 67 examples (33.3%)

Total: 201 examples.

## Difficult Examples and Label Decisions

### Example 1 — Technique mention with emotional framing
> "The sound design in Zone of Interest is incredible because it forces you to hear the horror happening off-screen while the family acts normal. It makes you feel deeply uncomfortable the entire time."

**Conflict:** References a specific filmmaking technique (off-screen audio) but centers the argument on viewer emotional reaction.
**Decision rule:** Label as `analytical_critique` when the post spends more effort explaining *how* a technique works and *why* it creates meaning; label as `subjective_take` when personal feeling is the dominant claim.
**Label assigned: `analytical_critique`** — the core claim identifies a deliberate technique and its narrative purpose; the emotional reaction is supporting evidence, not the main point.

---

### Example 2 — Narrative judgment blended with quality assessment
> "I know the ending of Donnie Darko is supposed to be intentionally ambiguous, but I still think the movie fails because it gives the audience too many unanswered questions and never really rewards the emotional investment."

**Conflict:** Contains both a narrative judgment about the ending and a personal quality assessment of the film's effectiveness.
**Decision rule:** Label as `subjective_take` when the main claim is about the film's success or failure and the language is opinionated; label as `narrative_decoding` when the focus is on explaining or interpreting story elements.
**Label assigned: `subjective_take`** — the dominant claim is that the movie "fails"; the statement is about the writer's reaction to the narrative choices, not an explanation of what happened.

---

### Example 3 — Filmmaking technique used to decode character
> "The long tracking shot in Once Upon a Time in Hollywood says more about Rick Dalton's fractured ego than any dialogue in that scene. It's a visual statement about his inability to separate fantasy from reality."

**Conflict:** Mixes discussion of a specific technique (tracking shot) with character motivation interpretation.
**Decision rule:** Label as `analytical_critique` when the post explains how technical choices convey meaning; label as `narrative_decoding` when the focus is on plot or character motivation without emphasis on craft.
**Label assigned: `analytical_critique`** — the tracking shot and visual staging are analyzed as deliberate techniques that reveal character psychology.

## Fine-Tuning Pipeline

### Base model and platform

- Base model: `distilbert-base-uncased`
- Training platform: Google Colab / local environment with Hugging Face Transformers and the `datasets` library.

### Key training decisions

- `num_train_epochs = 3`: chosen because the dataset is small (around 200 examples); additional epochs increase the risk of overfitting.
- `learning_rate = 2e-5`: the standard starting rate for fine-tuning BERT-family models; it balances stability and learning speed.
- `per_device_train_batch_size = 16`: selected to fit comfortably on a T4 GPU while maintaining efficient gradient updates.
- `weight_decay = 0.01` and `warmup_steps = 50`: added to stabilize training and reduce overfitting.

These decisions are documented in the fine-tuning notebook and should be explained in the final project report.

## Baseline Comparison

- Baseline approach: zero-shot classification using Groq with the `llama-3.3-70b-versatile` model.
- Prompt structure: the baseline prompt provides the subreddit context, label definitions, one example per label, and explicit instructions to respond with only the label name.
- Example prompt text:

```text
You are classifying posts from the subreddit r/TrueFilm.
Assign each post to exactly one of the following categories.

analytical_critique: The post analyzes a film using cinematic techniques such as cinematography, editing, sound design, mise-en-scène, structure, or historical/contextual film analysis rather than focusing on personal feelings or simple plot summary.
Example: "If you track the blocking in Parasite, Bong Joon-ho consistently places the Kim family lower in the frame to emphasize class hierarchy."

subjective_take: The post primarily expresses personal opinion, emotional reaction, or evaluation of a film’s quality without supporting it with technical or theoretical analysis.
Example: "I found Oppenheimer really boring and the pacing felt exhausting the entire time."

narrative_decoding: The post focuses on explaining plot events, character motivations, symbolism tied directly to story elements, or asking questions about the story or ending.
Example: "Can someone explain the ending of Inception? Did Cobb ever wake up or not?"

Respond with ONLY the label name exactly as written.
Do not explain your reasoning.

Valid labels:
analytical_critique
subjective_take
narrative_decoding
```

- Results collection: the baseline runs on the same held-out test set as the fine-tuned model, then computes accuracy and per-label metrics for direct comparison.
- Evaluation output: `evaluation_results.json` is generated with both baseline and fine-tuned accuracy values, while `confusion_matrix.png` visualizes performance on the test set.

## Evaluation Report

### Model Comparison: Baseline vs. Fine-Tuned

| Metric | Zero-shot Baseline (Groq) | Fine-Tuned DistilBERT |
|---|---|---|
| Overall Accuracy | 0.97 | 0.77 |
| Macro F1 Score | 0.97 | 0.78 |
| Test Set Size | 31 | 31 |
| Parseable Responses | 31/31 | 31/31 |

**Observation:** The zero-shot baseline outperforms the fine-tuned model on this test set (0.97 vs 0.77). The baseline's strong zero-shot performance suggests the task is well-defined and the label definitions are clear enough for a large language model to apply them reliably. The fine-tuned DistilBERT achieves 77% accuracy — above the 75% minimum threshold — but falls short of the 80% "good enough" target defined in `planning.md`. The primary source of fine-tuned model errors is systematic confusion between `analytical_critique` and `narrative_decoding` on symbolism-heavy posts, a boundary the larger LLM baseline handles more robustly.

### Per-Class Metrics (Fine-Tuned Model)

| Label | Precision | Recall | F1 Score | Support |
|---|---|---|---|---|
| analytical_critique | 0.60 | 0.90 | 0.72 | 10 |
| subjective_take | 1.00 | 0.80 | 0.89 | 10 |
| narrative_decoding | 0.88 | 0.64 | 0.74 | 11 |
| **Macro Average** | **0.83** | **0.78** | **0.78** | **31** |

Overall accuracy: **0.77** (24/31 correct on test set)

### Confusion Matrix (Fine-Tuned Model)

| Predicted → | analytical_critique | subjective_take | narrative_decoding |
|---|---|---|---|
| **Actual ↓** | | | |
| analytical_critique | 9 | 0 | 1 |
| subjective_take | 2 | 8 | 0 |
| narrative_decoding | 4 | 0 | 7 |

**Key observations:**
- `narrative_decoding` is the hardest label: 4 of its 11 examples are misclassified as `analytical_critique` (recall 0.64).
- `analytical_critique` has high recall (0.90) but low precision (0.60) — it absorbs 6 false positives from the other two classes.
- `subjective_take` has the cleanest predictions: perfect precision (1.00) with 2 false negatives misclassified as `analytical_critique`.

### Sample Classifications (Correct Predictions)

| Text | True Label | Predicted | Confidence | Notes |
|---|---|---|---|---|
| "Villeneuve's use of brutalist architecture visually reinforces the colonial oppression of the Harkonnens." | analytical_critique | analytical_critique | 0.92 | ✅ Correctly identified explicit technique analysis ("brutalist architecture" → visual meaning). |
| "I honestly found Dune Part 2 incredibly boring and poorly paced compared to the book." | subjective_take | subjective_take | 0.88 | ✅ Clear evaluative language ("boring," "poorly paced") without technical reasoning. |
| "Did Paul Atreides actually believe he was the messiah, or was he just using the Fremen?" | narrative_decoding | narrative_decoding | 0.91 | ✅ Character motivation and plot interpretation question, classic narrative_decoding. |

### Error Analysis: 7 Misclassified Examples

**Error #1 — Superlative opinion confused with technique analysis**
- Text: "Zodiac is Fincher's absolute best movie, easily surpassing Fight Club."
- True label: `subjective_take`
- Predicted: `analytical_critique` (confidence: 0.35)
- Analysis: The post uses exclusively evaluative language ("absolute best," comparative claim) with no mention of specific filmmaking techniques. The model appears to have interpreted Fincher's name and the comparative framing as indicating technical analysis, when the core intent is personal judgment.

**Error #2 — Superlative about craft misunderstood as judgment about technique**
- Text: "The practical effects in The Thing are still the greatest ever put on film."
- True label: `subjective_take`
- Predicted: `analytical_critique` (confidence: 0.36)
- Analysis: Similar to #1, this is a superlative claim about visual quality without explaining *why* the effects are effective or *how* they work. The model conflated "practical effects" (a technical term) with an analysis of technique, when the post is purely evaluative.

**Error #3 — Poetic narrative interpretation confused with technical analysis**
- Text: "The hallucinatory editing during the boat journey in Apocalypse Now mirrors the descent into primeval madness."
- True label: `analytical_critique`
- Predicted: `narrative_decoding` (confidence: 0.37)
- Analysis: The post explicitly analyzes editing as a technique ("editing... mirrors") to convey narrative meaning. The model classified it as narrative_decoding, suggesting it prioritized the thematic interpretation ("descent into madness") over the technique-focused framing.

**Error #4 — Symbolism with psychological interpretation**
- Text: "Glass surviving the bear attack is heavily symbolic of his spiritual rebirth."
- True label: `narrative_decoding`
- Predicted: `analytical_critique` (confidence: 0.38)
- Analysis: The post focuses on plot event interpretation (surviving the attack) and its symbolic meaning for the character arc. The model misclassified it as analytical_critique, possibly because "symbolic" triggered a pattern associated with film theory analysis rather than story interpretation.

**Error #5 — Abstract symbolism in narrative context**
- Text: "The Star Child represents humanity's next evolutionary leap, guided by the monolith."
- True label: `narrative_decoding`
- Predicted: `analytical_critique` (confidence: 0.36)
- Analysis: This is an interpretation of story elements and their meaning within the narrative world. The model classified it as analytical_critique, suggesting it confused narrative-level symbolism with formal film analysis.

**Error #6 — Character motivation question**
- Text: "Did Judy actually love Scottie in Vertigo or was she just playing a part for Elster?"
- True label: `narrative_decoding`
- Predicted: `analytical_critique` (confidence: 0.34)
- Analysis: This is a question about character psychology and plot-level motivation, core to narrative_decoding. The model misclassified it as analytical_critique, likely due to the character psychology framing being conflated with thematic or formal analysis.

**Error #7 — Narrative symbolism**
- Text: "The poisoned apple in Oppenheimer represents his early realization of the destructive power he wields."
- True label: `narrative_decoding`
- Predicted: `analytical_critique` (confidence: 0.39)
- Analysis: This is story-level interpretation (symbol = narrative meaning), but the model classified it as analytical_critique. The symbolic framing and mention of "realization" may have triggered a pattern associated with film analysis.

### Failure Pattern Reflection

**Primary confusion: analytical_critique ↔ narrative_decoding (Errors #3–7)**

The model struggles most with the boundary between technical film analysis and narrative-level interpretation, particularly when posts discuss **symbolism**. Both label categories can involve analyzing *meaning*, but the distinction lies in whether the meaning is conveyed through *filmmaking technique* (analytical_critique) or *story elements* (narrative_decoding).

- Posts #4, #5, #7 discuss symbols but as story devices, not as formal techniques.
- Post #3 explicitly uses a technique (editing) but the model prioritized the thematic payoff over the method.

This suggests the model's embedding space may conflate "analysis of meaning" with "analysis of technique," leading to systematic confusion when both are present.

**Secondary confusion: subjective_take ↔ analytical_critique (Errors #1–2)**

Posts using superlatives ("best," "greatest") without technical support are being classified as analytical_critique. This may reflect:
- Fincher's and practical effects being recognized as technical terms, even when used in evaluative context.
- The model learning that opinion + technical vocabulary = analytical content.

**Implications for the taxonomy:**

The label definitions may benefit from explicit guidance on the presence/absence of formal analysis: analytical_critique should require explanation of *how* technique works, not just *what* is being discussed or *whether* it succeeds.

## Usage

1. Place your labeled CSV file in the project folder.
2. Open `takemeter.py` and update `LABEL_MAP` to match your label names if needed.
3. Run the notebook or script to train the model and evaluate on the test set.

## AI Usage and Spec Reflection

### AI Tool Usage

**Instance #1 — Label boundary stress-testing**
- Directed: Used an LLM to generate synthetic borderline posts sitting between `analytical_critique` and `narrative_decoding` to test the clarity of label definitions.
- Result: Generated posts helped identify that "symbolic" language often triggers ambiguity — posts like "The Star Child represents humanity's next evolutionary leap" could be interpreted as either narrative symbolism or film theory depending on context.
- Revised: Updated the label definitions in `planning.md` to explicitly clarify that `analytical_critique` requires discussion of *how filmmaking technique works*, not just *what is being discussed*.

**Instance #2 — Error pattern analysis**
- Directed: After model training, used an LLM to analyze the 7 misclassified examples and group them into systematic error patterns.
- Result: The model was grouping "analysis of meaning" with "analysis of technique," leading to confusion between `analytical_critique` and `narrative_decoding` on symbolic posts.
- Revised: Manually reviewed all LLM-generated hypotheses and kept only the patterns I could verify in the actual errors (symbolism confusion, superlative + technical vocabulary confusion). Rejected overly generic observations like "the model needs more data."

### Annotation Assistance Disclosure

The labeled dataset (`r_truefilm_dataset.csv` with 201 examples) was **manually annotated without AI assistance**. All 201 posts were read and labeled by hand according to the taxonomy definitions. No AI pre-labeling was used; every label decision is human-verified.

### Spec Reflection

**How the spec guided the work:**
The spec's requirement for "at least 3 genuinely difficult examples" forced me to deeply understand the boundary between labels. This led to creating `planning.md` with three detailed edge cases and explicit decision rules. These cases became the foundation for all downstream work — when I later encountered model errors, I could immediately map them to these pre-defined boundary patterns. The taxonomy became more precise because I had to explain exactly *why* each difficult case belonged to one label, not another.

**How implementation diverged from the spec:**
The spec called for a "fine-tuning pipeline" with DistilBERT, which I implemented. However, the results were the opposite of what the spec implicitly assumed: the zero-shot Groq baseline (97%) significantly outperformed the fine-tuned DistilBERT (77%). This was surprising and forced me to revise the evaluation narrative: rather than celebrating fine-tuning gains, I had to interpret why fine-tuning on 140 examples failed to reach baseline performance. The most likely explanation is that DistilBERT (66M parameters, trained on general text) does not have enough capacity to learn the subtle symbolism boundary between `analytical_critique` and `narrative_decoding` from a small dataset, while the large Groq LLM can apply the label definitions directly from the prompt. This revealed that the project's real contribution is the **taxonomy definition** — clear enough that a prompted LLM applies it accurately without any training.

---

## Video Walkthrough
    [Watch the video](https://www.loom.com/share/c463c0f17c1d49c98dd992239e576e66)

---



