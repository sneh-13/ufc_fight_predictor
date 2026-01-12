Notes:

The reason your ML model is showing a higher win rate for "Fighter 1" is that ufcstats.com follows the official UFC corner assignments. In their database, Fighter 1 is always the Red Corner fighter, and the Red Corner is intentionally reserved for the "A-side" of the match.
1. How the order is chosen

The UFC uses a strict hierarchy to determine who gets the Red Corner (Fighter 1) and who gets the Blue Corner (Fighter 2):

    Champion vs. Challenger: The Champion is always in the Red Corner.

    Ranked vs. Unranked: The higher-ranked fighter is almost always in the Red Corner.

    Veteran vs. Newcomer: If neither is ranked, the fighter with more UFC seniority or the "bigger name" (the one the UFC is promoting more heavily) gets the Red Corner.

    Hometown Advantage: In international events (e.g., a card in Brazil), the local favorite often gets the Red Corner to signify they are the "home" fighter.

2. Why your ML model is "biased"

Your model has likely discovered Target Leakage. Since the UFC matchmakers already know who the "better" fighter is (based on rankings, betting odds, and career history), they place that person in the Red Corner.

Statistically, the Red Corner wins significantly more often than the Blue Corner. In some eras of UFC history, the Red Corner win rate has been as high as 60% or more. By using the fighter order from the website, you are accidentally giving your model a "cheat code"—it isn't learning how to predict a fight; it's learning to recognize that the UFC matchmakers think Fighter 1 is better.

3. How to fix your ML model

If you want to build a truly predictive model that doesn't rely on the UFC's "spoiler" ordering, you should try the following:

    Randomize the Order: For every fight in your training set, randomly swap Fighter 1 and Fighter 2. This forces the model to look at the actual stats (striking, grappling, etc.) rather than the column position.

    Use Relative Stats: Instead of having "Fighter 1 Striking" and "Fighter 2 Striking" as separate features, create a single feature for the difference (e.g., F1_SLpM - F2_SLpM).

    Remove Corner Data: If you have a column for "Corner Color" or "Fighter Position," delete it. It is a proxy for the outcome of the fight.

Summary: Fighter 1 is the "Favorite/Champion/A-Side." If you don't shuffle your data, your model will simply learn that "the person listed first usually wins," which won't help you predict fights where the underdog pulls off an upset.

is there a reason to shuffle the data when you know which person is going to be A-side before a fight happens?

That is a very sharp question. If your goal is strictly to predict the winner of a fight and you know the corner assignments beforehand, it might feel counterintuitive to throw away that information.

However, from a Machine Learning perspective, there are two major reasons why you should still shuffle (or otherwise handle) that bias:
1. Avoiding "Over-Reliance" (Feature Importance)

If your model "sees" that Fighter 1 wins 60% of the time, it might decide that the most important feature is "Which column is the fighter in?" rather than "Who is the better grappler?" If the model relies too heavily on the corner assignment:

    It becomes "lazy." It stops looking for the nuance in the stats (like a styles-make-fights matchup) because it can get a decent accuracy score just by guessing Fighter 1.

    When a major upset happens (an underdog wins), your model will likely fail to predict it because it is "blinded" by the Red Corner bias.

2. Generalization and True Probability

If you want to know why someone is going to win, you need to isolate their skills from their "pre-fight status."

By shuffling the data (or using relative differences), you force the model to learn the underlying mechanics of the fight. Once the model is trained on neutral data, you can then test it on upcoming fights. If your neutral model says Fighter 2 (Blue Corner) actually has better stats than Fighter 1 (Red Corner), you’ve found a potential betting "value" or an upset—something the UFC matchmakers (and your biased model) might have missed.
Recommended Approach: The "Hybrid" Method

Instead of just shuffling and losing the "A-side" information entirely, here is the most robust way to handle this for UFC stats:

    Neutralize the Features: Create features based on the difference between the two fighters (e.g., Reach_Diff = F1_Reach - F2_Reach). This makes the model look at the gap between them rather than their position.

    Add a "Status" Flag: Instead of using "Fighter 1" as the bias, add a binary feature: is_champion or is_higher_ranked.

    Shuffle for Training: Duplicate your dataset and swap the rows (so every fight appears twice: once with Fighter A as F1, and once with Fighter B as F1). This is called Data Augmentation. It teaches the model that the identity of the column doesn't matter, but the stats within them do.

The Bottom Line: If you don't shuffle, your model isn't a "Fight Predictor"—it's a "UFC Matchmaker Predictor." It's telling you who the UFC thinks should win, not necessarily who will win.

Would you like me to show you a Python snippet on how to "double" your dataset by flipping the fighter columns to remove that bias?