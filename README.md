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

## Usage

1. Place your labeled CSV file in the project folder.
2. Open `takemeter.py` and update `LABEL_MAP` to match your label names if needed.
3. Run the notebook or script to train the model and evaluate on the test set.

## Notes

- The README should be updated with the actual dataset filename and exact label counts once the labeled CSV is available.
- If you want to submit this project, include the labeled CSV or a brief summary of dataset counts in the final report.
