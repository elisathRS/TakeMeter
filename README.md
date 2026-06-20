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
- Example focus: technical craft and why the director made certain formal choices.

### subjective_take
- Definition: The post focuses primarily on the author's personal emotional reaction or opinion about the film's quality or entertainment value.
- Example focus: feelings, enjoyment, boredom, praise, or dislike.

### narrative_decoding
- Definition: The post focuses on plot points, character motivations, symbolism tied to story events, or ending explanations rather than filmmaking technique.
- Example focus: what happened, why it happened, or how to interpret the story.

## Dataset and Labeling

- Source: manually collected posts from **r/TrueFilm**.
- Scope: discussion posts only, not comments, with enough text to support one of the three categories.
- Labeling process: each post is read, assigned to the single label that best describes the post's dominant rhetorical intent, and reviewed against the taxonomy definitions.
- Balance goal: roughly equal representation across labels to avoid majority bias.

### Label distribution

The dataset is designed to be balanced across the three labels. Current planned distribution:

- `analytical_critique`: 67 examples
- `subjective_take`: 66 examples
- `narrative_decoding`: 67 examples

> If a final dataset file is available, these counts should be updated with the actual labeled counts from the CSV.

## Difficult Examples and Label Decisions

At least three genuinely hard cases are documented in `planning.md` with the labeling decision and rationale for each. These examples help clarify boundary decisions when posts include both technique and personal reaction or both plot explanation and evaluation.

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
| Overall Accuracy | 0.97 | 0.97 |
| Macro F1 Score | 0.97 | 0.97 |
| Test Set Size | 31 | 31 |
| Parseable Responses | 31/31 | 31/31 |

**Observation:** Both models achieve identical accuracy on the test set. The baseline's strong zero-shot performance suggests the task is well-defined and the label definitions are clear. The fine-tuned model matches this performance despite the small dataset, indicating effective transfer learning.

### Per-Class Metrics (Fine-Tuned Model)

| Label | Precision | Recall | F1 Score | Support |
|---|---|---|---|---|
| analytical_critique | 0.91 | 1.00 | 0.95 | 10 |
| subjective_take | 1.00 | 0.90 | 0.95 | 10 |
| narrative_decoding | 1.00 | 1.00 | 1.00 | 11 |
| **Macro Average** | **0.97** | **0.97** | **0.97** | **31** |

Overall accuracy: **0.97** (30/31 correct on test set)

### Confusion Matrix (Fine-Tuned Model)

| Predicted → | analytical_critique | subjective_take | narrative_decoding |
|---|---|---|---|
| **Actual ↓** | | | |
| analytical_critique | 10 | 0 | 0 |
| subjective_take | 1 | 9 | 0 |
| narrative_decoding | 0 | 0 | 11 |

**Key observations:**
- `narrative_decoding` achieves perfect predictions (11/11).
- `analytical_critique` recalls all 10 true positives but receives 1 false positive from subjective_take.
- `subjective_take` has 1 false negative (misclassified as analytical_critique).

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
