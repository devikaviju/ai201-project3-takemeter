# Planning.md

## 1. Community Context
I have chosen the r/soccer community for this project because the comments go back and forth between high-level analysis and impulsive emotional venting. My labels (analysis, hot_take, reaction) are designed to separate these modes of communication. This distinction matters to this community because it separates users who contribute to the 'collective footballing IQ' from those who are simply sharing a temporary, non-constructive feeling.

## 2. Label Taxonomy
analysis - A post that evaluates team performance, tactics, or footballing economics using specific, verifiable evidence like stats, tactical observations, or financial logic.

Example 1: The team is very strong and they have good players, but other than Diaz, they don’t have too many standout stars like a team like Portugal, yet they completely outplayed them and dominated them. I’ve thought this for many years. The sum is way greater than the parts.

Example 2: I went down the rabbit hole a little on how much these guys get paid... Like Haaland as most in prem is under $30m. You got guys in the nba who are half as good... I figured they’d compete with nba/mlb/nfl salaries.

hot_take - A bold, definitive claim about a team, player, or manager presented as fact without providing credible, verifiable evidence to support it.

Example 1: 3 host countries getting favorable seeding for the knockouts is absolute silliness

Example 2: Pretty clear Ronaldo has to play against FIFA as well which makes the World Cup not as important when considering who’s the better player.

reaction - An immediate expression of feelings, personal anecdotes, or community chatter that does not attempt to offer a structured argument.

Example 1: I feel like there's been a surprising amount of negativity here. Not even toward FIFA, although there's been plenty of that, but toward teams and players, in a way that makes me sad.

Example 2: Can't believe one of Brazil/Japan and one of Netherlands/Morocco will be eliminated tomorrow.

## 3. Hard Edge Cases
1. The "Sarcastic Reaction vs. Analysis"

Post: "you know older games are better when you see Pedri and Borja Iglesias playing 2010 world cup game"

The Conflict: This could be read as a casual reaction (sharing a feeling/anecdote) or as analysis (making a comparative critique of modern vs. past gameplay quality).

The Decision: Reaction. Even though it makes a comparative statement, it lacks specific, verifiable evidence (stats, tactical observations). It is a subjective opinion expressed as a feeling about how "games used to be better," which fits the community definition of community chatter.

2. The "Host Nation Advantage" Debate

Post: "And we’re also hosts… hosts always end up pot 1 teams even when they aren’t actually that level"

The Conflict: This involves a verifiable fact about tournament structure (pot allocation for hosts), which usually pushes a post toward analysis. However, the phrasing "aren’t actually that level" shifts the tone toward a hot_take.

The Decision: Analysis. Per the decision rule, the post cites a verifiable tournament rule (host pot 1 allocation) as the foundation for the claim. Even though it contains an opinion on skill level, the core of the post is an observation of tournament structure rather than just a subjective complaint.

3. The "Player Injury/Resolve" Anecdote

Post: "Didn’t he sign for Wolves and tore his acl after 10 minutes or something silly like that"

The Conflict: This is a factual query/statement about a player's injury history, which often signals analysis. However, it is presented as a casual, informal question/chatter.

The Decision: Reaction. This is personal community chatter used to verify a player's history in the middle of a discussion. It does not attempt to build a structured argument about tactics or economics; it is simply asking for/confirming an anecdote, making it a reaction to the ongoing conversation.

## 4. Data Collection Plan
Method: I will manually copy 200–250 comments into a CSV file with columns for text, label, and notes. Manual collection allows me to read each comment thoroughly, ensuring I understand the context before applying a label.

Label Distribution: My target is approximately 33% per label (~66–70 examples each) to ensure the model does not become biased toward a majority class.

Handling Imbalance:

If one label (e.g., reaction) naturally accounts for the majority of the thread, I will intentionally skip repetitive, comments in that category and actively look for technical comments (analysis) or controversial claims (hot_take) in the thread to restore balance.

If a label remains underrepresented after the initial 200, I will browse a different r/soccer Daily Discussion thread from a previous day to "top-up" the missing category.

## 5. Evaluation Metrics
Precision and Recall: I need to know not just how often the model is correct, but where it is failing. For instance, if my analysis label has high precision but low recall, the model is being too conservative—it only labels posts as analysis when it is extremely confident, missing many valid analytical arguments.

F1-Score: This is the mean of precision and recall. It is the most important metric for this project because it provides a single "balance" score for each label, preventing a model that ignores a difficult category from appearing successful.

Confusion Matrix: This is essential for identifying systematic failure modes. It will show me exactly which labels the model confuses (e.g., if it frequently mislabels analysis as hot_take). This allows me to see if the boundary I designed is actually clear to the model or if it needs to be redefined.

## 6. Definition of Success
Performance Thresholds:

Minimum Baseline: An overall accuracy of >75% on the test set is the bare minimum, as it must significantly outperform the Groq zero-shot baseline to justify the time spent fine-tuning.

Class Stability: An F1-score of at least 0.70 for all labels. A model that excels at identifying analysis but fails entirely on reaction is not useful for a real community tool, as it would only provide biased insights.

Deployment Readiness:

Failure Mode Analysis: The model must not make "catastrophic" errors (e.g., classifying a deeply emotional reaction post as analysis). I will consider it "good enough" if the errors it makes are logical confusions—such as mislabeling a well-written hot_take as analysis—rather than random, nonsensical errors.

Generalization: I will test the model against a small, hand-picked set of 5–10 new posts from a different r/soccer Daily Discussion thread than the one I used for training. Success means the model maintains at least 70% accuracy on this "unseen" data.



## 7. AI Tool Plan
Label Stress-Testing
Before annotating, I will provide my label definitions (analysis, hot_take, reaction) and my Hard Edge Case rule to an LLM (e.g., Claude or GPT-4o). I will prompt it: "Generate 10 posts for r/soccer that sit at the boundary between these labels. Ensure some examples are designed to trigger the decision rule regarding factual vs. opinion-based assertions." I will then attempt to classify these myself. If I struggle to assign a label, I will clarify my definitions in the Label Taxonomy section before starting my 200-example manual annotation.

Annotation Assistance
I will use an LLM to pre-label a batch of 50 examples to speed up the process.

Tracking: I will add a column named pre_labeled_by_ai to my CSV.

Workflow: I will review every single pre-assigned label, correcting any errors. For any post where the AI's label and my review differ, I will update the notes column to explain the correction. This ensures 100% human-verified accuracy while maintaining efficiency.

Failure Analysis
After running inference on the test set, I will gather all misclassified examples. I will provide the text, true_label, and predicted_label for these errors to an LLM and prompt it: "Identify systematic patterns in these misclassifications. Are there common themes like sarcasm, post length, or specific label pairs that are frequently confused?" I will treat these AI suggestions as hypotheses and manually verify them by reading the specific examples to confirm if the patterns are genuine before including them in my final report.
