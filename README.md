# ai201-project3-takemeter

# TakeMeter: Evaluating Discourse in r/soccer

## 1. Community Choice and Reasoning
I chose the r/soccer community because it represents a high-entropy environment where discourse constantly oscillates between structured tactical analysis, impulsive emotional venting, and bold, unsubstantiated claims. This makes it an ideal testing ground for a classification model designed to measure discourse quality. 

## 2. Label Taxonomy
* **analysis:** A post that evaluates team performance, tactics, or footballing economics using specific, verifiable evidence like stats, tactical observations, or financial logic.
    * Example 1: "The team is very strong and they have good players, but other than Diaz, they don’t have too many standout stars like a team like Portugal, yet they completely outplayed them and dominated them. I’ve thought this for many years. The sum is way greater than the parts."
    * Example 2: "I went down the rabbit hole a little on how much these guys get paid... Like Haaland as most in prem is under $30m. You got guys in the nba who are half as good... I figured they’d compete with nba/mlb/nfl salaries."
* **hot_take:** A bold, definitive claim about a team, player, or manager presented as fact without providing credible, verifiable evidence to support it.
    * Example 1: "3 host countries getting favorable seeding for the knockouts is absolute silliness"
    * Example 2: "Pretty clear Ronaldo has to play against FIFA as well which makes the World Cup not as important when considering who’s the better player."
* **reaction:** An immediate expression of feelings, personal anecdotes, or community chatter that does not attempt to offer a structured argument.
    * Example 1: "I feel like there's been a surprising amount of negativity here. Not even toward FIFA, although there's been plenty of that, but toward teams and players, in a way that makes me sad."
    * Example 2: "Can't believe one of Brazil/Japan and one of Netherlands/Morocco will be eliminated tomorrow."

## 3. Data Collection
* **Source:** 200 public comments from r/soccer Daily Discussion threads.
* **Labeling Process:** Manually labeled after reading each post to ensure context-awareness.
* **Difficult Edge Cases:**
    1. *Sarcastic Reaction vs. Analysis:* A post about older games being better lacked verifiable evidence and was labeled **reaction**.
    2. *Host Nation Advantage:* A post citing tournament rules (pot allocation) was labeled **analysis**.
    3. *Player Injury Anecdote:* A casual query about an ACL tear was labeled **reaction**.

## 4. Fine-Tuning Approach
* **Base Model:** `distilbert-base-uncased`
* **Setup:** 3 epochs, learning rate 2e-5, batch size 16.
* **Hyperparameter Decision:** I chose 3 epochs to balance learning against the small dataset size (200 examples), which proved effective in maintaining accuracy without significant overfitting.

## 5. Baseline Description
* **Tool:** Groq `llama-3.3-70b-versatile`.
* **Method:** Used a zero-shot prompt including label definitions and one example per class, requiring the model to output only the label name.

## 6. Evaluation Report

### Performance Metrics
| Model | Overall Accuracy |
| Zero-shot baseline (Groq) | 0.667 |
| Fine-tuned DistilBERT | 0.567 |

### Confusion Matrix (Fine-Tuned Model)
| | Pred: Analysis | Pred: Hot_Take | Pred: Reaction |
| :--- | :--- | :--- | :--- |
| **True: Analysis** | 5 | 0 | 5 |
| **True: Hot_Take** | 0 | 0 | 3 |
| **True: Reaction** | 5 | 0 | 12 |

### Wrong Predictions Analysis
1. **Text:** "Probably because France has one of the best defenses in the world."
    * **True:** analysis | **Predicted:** reaction
    * **Analysis:** The model misinterpreted the casual "Probably because" phrasing as conversational chatter, ignoring the reference to defensive quality.
2. **Text:** "Tactically, that sub was meant to hold the lead, but it invited unnecessary pressure instead."
    * **True:** analysis | **Predicted:** reaction
    * **Analysis:** The model flagged the sentence as reaction because it describes a subjective coaching choice ("invited pressure") rather than using hard stats, revealing a bias toward emotional language.
3. **Text:** "Wait until you see how bad the groups are, you'll change your mind quickly."
    * **True:** hot_take | **Predicted:** reaction
    * **Analysis:** The model misread the conversational "you'll change your mind" as a personal exchange, causing it to fall back to the majority class (reaction).

### Sample Classifications
| Post Text | Predicted Label | Confidence |
| :--- | :--- | :--- |
| "Tactically, that sub was meant to hold the lead..." | reaction | 0.36 |
| "The team is very strong and they have good players..." | analysis | 0.42 |
| "Argentina's defense wasn't exactly elite..." | reaction | 0.38 |

## 7. Reflections
* **Intended vs. Captured:** I intended the model to capture the structure of a logical argument. Instead, the model overfit to the informal, conversational markers common in "reaction" posts, likely because "reaction" was the most common class in my training set.
* **Spec Reflection:** The spec helped guide the necessity of the baseline comparison, which provided a clear reality check. My implementation diverged in that I had to add a custom renaming logic in Section 1 because my CSV headers initially didn't match the notebook requirements.
* **AI Usage:** 1. Used Gemini to identify themes in my wrong predictions (e.g., the tendency to default to the "reaction" class on informal sentences).
    2. Used Gemini to help me re-frame the label boundary definitions in my `planning.md` after I realized my initial definitions were too vague.
