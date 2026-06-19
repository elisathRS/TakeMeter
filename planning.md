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

### Example
> "The sound design in Zone of Interest is incredible because it forces you to hear the horror happening off-screen while the family acts normal. It makes you feel deeply uncomfortable the entire time."

### The Conflict
This post sits directly on the boundary between `analytical_critique` and `subjective_take`. It references a specific filmmaking technique ("sound design" and the use of off-screen audio), but the main justification centers on the viewer's emotional response ("makes you feel deeply uncomfortable").

### Decision Rule
- Label as **`analytical_critique`** when the post spends more effort explaining *how* a filmmaking technique works and *why* it creates meaning.
- Label as **`subjective_take`** when the post primarily emphasizes personal feelings, enjoyment, boredom, shock, or emotional impact, even if it briefly mentions a technical element.
- When both are present, prioritize the label representing the **majority of the argument**, not isolated keywords.

### Final Classification for This Example
**`analytical_critique`** — because the core claim identifies and explains a deliberate filmmaking technique (off-screen sound) and its narrative purpose, while the emotional reaction serves as supporting evidence.

## Data collection plan
<!-- Where will you collect examples? How many per label? What will you do if a label is underrepresented after 200 examples? -->
**Source:** Collect posts directly from the Reddit community **r/TrueFilm** using Reddit search and subreddit browsing. Focus on discussion posts rather than comments, since posts typically contain enough text for meaningful classification.

### Target Dataset Size

**200 manually labeled posts**

| Label | Target Count |
|---------|-------------|
| analytical_critique | 65–67 |
| subjective_take | 65–67 |
| narrative_decoding | 65–67 |

A roughly balanced dataset helps prevent the model from favoring one label simply because it appears more frequently than others.

### Collection Strategy

- Sort posts by **Top**, **Hot**, and **New** to capture a variety of discussion styles.
- Sample films from different genres, decades, countries, and directors.
- Manually label each post according to the defined taxonomy.
- Exclude posts that are too short, deleted, or lack sufficient context for labeling.

### Handling Underrepresented Labels

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

## Evaluation metrics
<!-- Which metrics will you use to evaluate your model and why are those the right ones for this specific task? (Accuracy alone is not enough — explain what else you need and why.) -->

## Definition of success
<!-- What performance would make this classifier genuinely useful? What would you accept as "good enough" for deployment in a real community tool? -->
