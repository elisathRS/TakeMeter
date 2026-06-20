# Community 

**Community Selection:** r/TrueFilm

**Description:**  
r/TrueFilm is a subreddit dedicated to structured, in-depth discussions about cinema, film theory, and industry history. Unlike mainstream movie forums, it explicitly mandates a 180-character minimum for posts to discourage low-effort content. However, quality still varies dramatically between academic film criticism, highly opinionated subjective rants, and surface-level plot observations, making it an ideal landscape for text classification.

---

# Taxonomy 

## Label 1: `analytical_critique`

**Definition:**  
The post evaluates a film by analyzing specific cinematic techniques (e.g., cinematography, editing, structural motifs, historical context) rather than simply expressing personal enjoyment or dislike.

### Example 1
> "Villeneuve's use of brutalist architecture in Dune isn't just an aesthetic choice; it visually reinforces the colonial oppression of the Harkonnens by making the scale of the environments completely swallow the human form."

### Example 2
> "If you track the blocking in the dinner scene of Parasite, Bong Joon-ho consistently places the Kim family physically lower in the frame than the Park family, a literal visual framing of class stratification."

### Uncertain / Borderline Case
> "The sound design in Zone of Interest is incredible because it forces you to hear the horror happening off-screen while the family acts normal. It makes you feel deeply uncomfortable the entire time."

---

## Label 2: `subjective_take`

**Definition:**  
The post focuses primarily on the author's personal emotional reaction, entertainment value, or opinion about a film's quality without supporting the claim through technical analysis or film theory.

### Example 1
> "Am I the only one who found Oppenheimer incredibly boring? The pacing was completely frantic in the first hour and it just felt like a three-hour movie trailer."

### Example 2
> "Interstellar is Nolan's absolute masterpiece. No other movie has ever made me cry so hard while simultaneously blowing my mind with its sci-fi concepts."

### Uncertain / Borderline Case
> "I think Horizon: An American Saga failed because audiences today completely lack the attention span required for classical, slow-burn Western storytelling."

---

## Label 3: `narrative_decoding`

**Definition:**  
The post focuses on explaining, debating, or recapping plot points, character motivations, lore, symbolism tied directly to story events, or ending explanations rather than analyzing filmmaking techniques.

### Example 1
> "In the ending of Inception, it doesn't actually matter if the top keeps spinning or falls, because Cobb walks away from it, showing he finally accepts his reality with his kids."

### Example 2
> "Can someone explain why the bear scene in Annihilation happened? Was the bear actually mutated by the Shimmer, or was it carrying the consciousness of the person it killed?"

### Uncertain / Borderline Case
> "Mulholland Drive is a brilliant critique of the Hollywood dream machine, functioning entirely as a guilt-induced dream sequence created by Diane after she hires a hitman to kill Camilla."

---

# Edge Cases & Decision Rules

## Hardest Anticipated Edge Case

### Example 1
> "The sound design in Zone of Interest is incredible because it forces you to hear the horror happening off-screen while the family acts normal. It makes you feel deeply uncomfortable the entire time."

### The Conflict
This post sits directly on the boundary between `analytical_critique` and `subjective_take`. It references a specific filmmaking technique ("sound design" and the use of off-screen audio), but the main justification centers on the viewer's emotional response ("makes you feel deeply uncomfortable").

### Decision Rule
- Label as **`analytical_critique`** when the post spends more effort explaining *how* a filmmaking technique works and *why* it creates meaning.
- Label as **`subjective_take`** when the post primarily emphasizes personal feelings, enjoyment, boredom, shock, or emotional impact, even if it briefly mentions a technical element.
- When both are present, prioritize the label representing the **majority of the argument**, not isolated keywords.

### Final Classification for This Example
**`analytical_critique`** — because the core claim identifies and explains a deliberate filmmaking technique (off-screen sound) and its narrative purpose, while the emotional reaction serves as supporting evidence.

## Additional Difficult Examples

### Example 2
> "I know the ending of Donnie Darko is supposed to be intentionally ambiguous, but I still think the movie fails because it gives the audience too many unanswered questions and never really rewards the emotional investment."

### The Conflict
This post contains both a narrative judgement about the ending and a personal quality assessment of the film's effectiveness.

### Decision Rule
- Label as **`subjective_take`** when the main claim is about the film's success or failure and the language is opinionated.
- Label as **`narrative_decoding`** when the post focuses on explaining or interpreting story elements and their meaning.

### Final Classification for This Example
**`subjective_take`** — because the dominant claim is that the movie "fails" and the statement is about the writer's reaction to the narrative choices rather than an explanation of what happened.

### Example 3
> "The long tracking shot in Once Upon a Time in Hollywood says more about Rick Dalton's fractured ego than any dialogue in that scene. It's a visual statement about his inability to separate fantasy from reality."

### The Conflict
This post mixes discussion of a specific filmmaking technique with an interpretation of character motivation.

### Decision Rule
- Label as **`analytical_critique`** when the post explains how technical choices convey meaning.
- Label as **`narrative_decoding`** when the post focuses primarily on plot or character motivation without enough emphasis on craft.

### Final Classification for This Example
**`analytical_critique`** — because the tracking shot and visual staging are analyzed as deliberate techniques that reveal character psychology.

# Data collection plan
<!-- Where will you collect examples? How many per label? What will you do if a label is underrepresented after 200 examples? -->
**Source:** Collect posts directly from the Reddit community **r/TrueFilm** using Reddit search and subreddit browsing. Focus on discussion posts rather than comments, since posts typically contain enough text for meaningful classification.

Target Dataset Size: **200 manually labeled posts**

| Label | Target Count |
|---------|-------------|
| analytical_critique | 65–67 |
| subjective_take | 65–67 |
| narrative_decoding | 65–67 |

A roughly balanced dataset helps prevent the model from favoring one label simply because it appears more frequently than others.

## Collection Strategy

- Sort posts by **Top**, **Hot**, and **New** to capture a variety of discussion styles.
- Sample films from different genres, decades, countries, and directors.
- Manually label each post according to the defined taxonomy.
- Exclude posts that are too short, deleted, or lack sufficient context for labeling.

## Handling Underrepresented Labels

After collecting the initial 200 posts, review the label distribution. If any category contains fewer than **65 examples**, perform targeted searches to gather additional samples.

**Suggested search terms:**

| Search Term | Likely Label |
|-------------|-------------|
| "ending explained" | narrative_decoding |
| "symbolism" | narrative_decoding |
| "why did this movie fail" | subjective_take |
| "why did this movie succeed" | subjective_take |
| "personal opinion" | subjective_take |
| "cinematography" | analytical_critique |
| "editing" | analytical_critique |
| "mise-en-scène" | analytical_critique |
| "film theory" | analytical_critique |

Continue collecting and labeling posts until each class contains at least **65 examples**, ensuring adequate representation across all categories.

# Evaluation Metrics
<!-- Which metrics will you use to evaluate your model and why are those the right ones for this specific task? (Accuracy alone is not enough — explain what else you need and why.) -->
To evaluate the classifier, I will use **Accuracy, Precision, Recall, F1 Score, and a Confusion Matrix**.

## Accuracy

Accuracy measures the percentage of correctly classified posts overall. It provides a general view of model performance, but it is not sufficient by itself because it does not reveal whether the model performs equally well across all three labels.

## Precision

Precision measures how often a predicted label is correct. This is important because incorrectly labeling a post as **analytical_critique** when it is actually **subjective_take** would reduce the usefulness of the classifier.

## Recall

Recall measures how many true examples of a label the model successfully identifies. This helps determine whether the model is missing important examples of a category.

## F1 Score

F1 Score balances Precision and Recall into a single metric. Because the boundaries between **analytical_critique**, **subjective_take**, and **narrative_decoding** are often subtle and overlapping, F1 Score provides a better measure of classification quality than Accuracy alone.

I will report:
- Precision for each label
- Recall for each label
- F1 Score for each label
- Macro-average F1 Score across all labels

## Confusion Matrix

A confusion matrix shows exactly where the model makes mistakes. This is especially important for this project because the most challenging cases occur when the model confuses:

- **analytical_critique** with **subjective_take**
- **analytical_critique** with **narrative_decoding**

The confusion matrix helps identify these label-boundary errors and supports deeper error analysis.

## Why These Metrics Are Appropriate

These metrics are appropriate because the task involves distinguishing between three closely related discussion styles rather than simply identifying positive or negative sentiment. Accuracy alone could hide systematic confusion between labels, while Precision, Recall, F1 Score, and the Confusion Matrix provide a detailed view of how well the model understands the distinctions defined in the taxonomy.

# Definition of success

A classifier becomes genuinely useful when it can assist moderators, researchers, or community members with minimal manual correction while maintaining consistent performance across all categories.

## Minimum Acceptable Performance

For a three-class classification task (`analytical_critique`, `subjective_take`, and `narrative_decoding`), a reasonable baseline for deployment would be:

- **Accuracy:** ≥ 75%
- **Macro F1 Score:** ≥ 0.70
- **Per-Class Precision and Recall:** ≥ 0.65

At this level, the model can provide useful recommendations or automatically organize posts, although occasional misclassifications would still require human review.

## Good Performance

A model would be considered reliable for everyday community use if it achieves:

- **Accuracy:** 80–85%
- **Macro F1 Score:** ≥ 0.80
- **Per-Class Precision and Recall:** ≥ 0.75

This performance level indicates that the classifier can distinguish between analytical criticism, subjective opinion, and narrative interpretation with relatively few errors. Most predictions would be trustworthy enough for content tagging, search filtering, or recommendation systems.

## Strong Deployment Target

For automated moderation support or large-scale content organization, an ideal target would be:

- **Accuracy:** ≥ 85%
- **Macro F1 Score:** ≥ 0.85
- **Per-Class Precision and Recall:** ≥ 0.80

At this level, the model would consistently identify discussion styles and require only limited human oversight.

## Evaluation Focus

Because the dataset is intentionally balanced, **Macro F1 Score** should be treated as the primary metric rather than accuracy alone. Macro F1 ensures that all three categories are classified well and prevents strong performance on one label from masking weak performance on another.

## Definition of "Good Enough"

For this project, a classifier with a **Macro F1 score of at least 0.80 and an accuracy above 80%** would be considered "good enough" for deployment as a community assistance tool. It would provide reliable post categorization while keeping manual review effort relatively low.

# AI Tool Plan

This project uses AI tools in three key stages of the workflow: label design validation, annotation support, and error analysis. These tools are used to improve consistency, efficiency, and the overall quality of the dataset, while ensuring that final decisions remain human-driven.

---

## Label Stress-Testing 

Before collecting the full dataset of 200 Reddit posts, an LLM will be used to generate synthetic examples that sit on the boundary between categories.

**Objective:**
Identify ambiguity, overlap, or unclear boundaries in the current label definitions:
- analytical_critique  
- subjective_take  
- narrative_decoding  

**Process:**
- Provide the model with the full label definitions and examples.
- Ask it to generate 5–10 borderline posts per iteration.
- Focus specifically on “hard cases” where classification may be unclear.

**When adjustments are needed:**
If generated examples cannot be consistently classified, the taxonomy will be refined by:
- Clarifying label definitions
- Strengthening decision rules for edge cases
- Improving or adding borderline examples

---

## Annotation Assistance (AI Pre-labeling)

An LLM may be used to assist with initial labeling of posts before manual review.

**Tool:**
ChatGPT (or equivalent large language model classifier)

**Process:**
- The model assigns a preliminary label to each post.
- The human annotator reviews and corrects all labels.
- Final dataset labels are always manually verified.

**Tracking and transparency:**
- Pre-labeled data will be stored separately (e.g., `pre_labeled.json` or `pre_labeled.csv`).
- Each entry will include an `ai_predicted_label` field.
- This ensures transparency about which examples were assisted by AI.

**Purpose:**
- Speed up annotation of straightforward examples
- Improve consistency in early labeling stages
- Reduce manual workload without replacing human judgment

---

## Failure Analysis (Error Review and Pattern Detection)

After model training and evaluation, AI tools will be used to analyze misclassified examples.

**Input to the model:**
- List of incorrectly predicted posts
- Model prediction vs ground truth label
- Full text of each post

**Objective:**
Identify systematic error patterns such as:
- Confusion between analytical_critique and subjective_take
- Misclassification of posts combining opinion and technical analysis
- Keyword bias (e.g., over-weighting terms like “cinematography” or “boring”)

**Process:**
- The LLM groups similar errors into categories
- Suggests possible reasons for misclassification
- Recommends improvements to:
  - Label definitions
  - Data sampling strategy
  - Annotation guidelines

**Validation:**
All insights generated by the AI will be manually reviewed before being included in the final report or used to modify the dataset.
