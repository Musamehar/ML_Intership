# Capstone Report — <Refresh / Content Opportunity Scoring>

- **Author:Muhammad Musa**
- **Lane:Refresh / Content Opportunity Scoring**
- **Repo:https://github.com/Musamehar/ML_Intership**
- **Date:2026-08-10**

## 1. Problem framing

The Decision Supported:
This tool provides directional, decision-support scoring to help SEO strategists and editorial teams prioritize which specific content items 
require a content refresh audit to mitigate organic traffic decay.

Unit of Analysis & Output:
The unit of analysis is a single content item (page) for a specific client domain. The output is a continuous risk score and a ranked priority 
queue, supplemented by categorical reason codes (e.g., stale_visible_decay). 

The Human Action & Cost of Error:
A human editor reviews the top $K$ items (e.g., Top 20) in the generated queue to manually audit and update the content.  
  i.The cost of a false positive is wasted editorial bandwidth on a healthy page.  
  ii.The cost of a false negative is missing a page experiencing active decay, leading to compounded traffic loss. 

Why ML Helps:
Content decay is driven by complex, non-linear interactions across search volume, click-through-rate (CTR) volatility, content staleness, and 
shifting search engine results page (SERP) positions. A rigid heuristic rule (e.g., "refresh if age > 180 days") flags hundreds of pages as a 
binary "YES," forcing manual sorting. Machine learning ranks these candidates on a continuous scale, creating an actionable queue that 
optimizes limited human effort by evaluating these interacting signals dynamically.

## 2. Data safety

Data Used:
This analysis relies exclusively on the anonymized FlyRank ML Internship dataset. The data consists of trailing 90-day aggregated search 
performance metrics (e.g., impressions_90d, clicks_90d, avg_position) and structural metadata (e.g., content_age_days).

Excluded Columns & Leakage Prevention:
To ensure an honest evaluation, strict data contracts were enforced:
  Target Leakage: The columns trend_pct and trend_direction were deliberately excluded from the feature set. Because the target label 
  (is_declining_label) is mathematically derived from these fields, including them would cause circular target leakage.

  Domain Leakage: Pseudonymous identifiers like client_id were used exclusively for grouping during cross-validation splits and were never 
  passed to the model as predictive features.

Privacy Confirmation:
No raw private client data, domains, URLs, page titles, or specific search queries were used. I confirm that nothing client-identifying appears 
anywhere in the work/ directory.  

## 3. Baseline

The Transparent Rule:
Before applying machine learning, a transparent, rule-based baseline was constructed to mirror how a human SEO editor might prioritize audits. 
The baseline calculates a composite priority score (0.0 – 100.0) based on:
  Visibility (50%): Normalized log 90-day impressions.
  Staleness Risk (35%): A binary penalty for content age $\ge$ 180 days.
  Position Opportunity (15%): A boost for content ranking in positions 1–10.

Why It's a Fair Comparison:
This heuristic is a fair comparison because it represents a standard, logical industry approach. It is evaluated on the exact same dataset, the 
exact same metrics, and the exact same grouped data splits as the machine learning models.

Baseline Numbers:
Evaluated on the client-grouped split, the handwritten rule achieved a Precision@50 of approximately 0.24. This established the minimum 
threshold the machine learning model had to beat to prove its utility.

## 4. Model / analysis

Method & Fit:
To move beyond a rigid heuristic, I trained a Random Forest Classifier to output a continuous decay probability score. This ensemble tree 
method perfectly fits this lane because content decay is driven by non-linear interactions and threshold dynamics (e.g., the relationship 
between search volume, position drift, and content staleness) that linear models fail to capture.

Feature List:
The model relies exclusively on signals knowable prior to the evaluation window. The exact feature list used is:
  log_impressions_90d (Visibility)

  ctr_90d (Engagement)

  avg_position (SERP Rank)

  content_age_days (Staleness)

  has_keyword (Optimization flag)

Deliberate Exclusions:
I explicitly left out trend_pct and trend_direction on purpose to prevent target leakage, as they are mathematically tied to the target label.

Target Definition:
The target is a binary proxy flag (decay_flag derived from is_declining_label) predicting whether a page experiences active traffic decay 
during the subsequent observation window.

## 5. Evaluation
Split Design:
I utilized a 5-Fold GroupKFold split grouped by client_id. A standard random split would mix URLs from the same client into both training and 
validation sets, allowing the model to cheat by memorizing domain-specific traffic baselines (domain leakage). Grouping by client ensures the 
model is evaluated honestly on completely unseen websites.

Metrics & Baseline Comparison:
The models were evaluated using ROC-AUC and Precision@20 on the exact same grouped split. (Note: The task's base rate for active decay in the 
dataset is approximately 25%).

  Rule Baseline (W04): Precision@20 = ~0.24 (Matches the natural base rate).

  Random Forest Model: Precision@20 = ~0.72.

  Conclusion: The learned model demonstrates a roughly 3x lift over the baseline on the exact same data.

Error Analysis:
A review of the model's errors reveals clear patterns:

False Positives (Predicted decay, actual healthy): The model occasionally flags high-volume, "evergreen" pages that are old (content_age_days > 
500) but continue to maintain strong, stable organic brand demand.

False Negatives (Predicted healthy, actual declining): The model misses sudden decay on relatively new content (content_age_days < 120) that 
experienced rapid SERP position losses, which age-heavy features fail to anticipate.

## 6. Interpretation

What the Model Found:
The Random Forest model identified that content decay is heavily dependent on the intersection of age and search volume. The most critical 
signals driving the model’s predictions are:

  i.content_age_days: Staleness acts as the primary risk factor for traffic loss.

  ii.log_impressions_90d: High-visibility pages face greater absolute volatility, making their decay more impactful.

  iii.avg_position: Pages ranking outside the top 3 but still on page one exhibit higher vulnerability to sudden click decay.

Surprises and Negative Results:
Interestingly, the model found that has_keyword (the presence of an optimized primary keyword) had minimal predictive power for future decay. A 
well-optimized page is just as likely to decay over time as a poorly optimized one once staleness sets in. This negative result is valuable 
because it proves that simply "optimizing" a page once at publish time does not protect it from natural organic decay.

**Feature Importance Breakdown:**
![Random Forest Feature Importance](https://raw.githubusercontent.com/Musamehar/ML_Intership/main/outputs/charts/top_feature_importance.svg)

## 7. Recommendation

The Action Playbook:
The primary output of this tool is a ranked queue that a FlyRank editor can use on a weekly basis to allocate their refresh bandwidth.

Rank 1–10 (Highest Risk): These pages are flagged with the stale_visible_decay reason code. An editor should review these immediately for 
outdated statistics, broken links, or shifts in search intent (e.g., Google introducing AI Overviews for those queries).

Rank 11–20 (Watchlist): These pages exhibit the page_one_decay_risk code. Editors should monitor their click-through rates and consider minor 
metadata adjustments to defend their position.

Confidence and Limits:
This tool is strictly a decision-support engine, not an automated editor. The model provides directional risk scoring based on observed 
historical patterns. It cannot predict external factors like broad algorithm updates, seasonal demand crashes, or competitor behavior. 
Therefore, human intuition remains necessary to validate the recommendations before executing a content rewrite.

## 8. Reproducibility

Environment & Commands:
This analysis is fully reproducible using the provided FlyRank ML Internship starter dataset and standard Python ML libraries.

Clone the repository: git clone [https://github.com/Musamehar/ML_Intership.git](https://github.com/Musamehar/ML_Intership.git)

Install dependencies: pip install pandas numpy scikit-learn

Run the pipeline: Execute the capstone notebooks in order (from w01 to w06) located in the work/notebooks/ directory.

Random Seeds:
To ensure identical data splits and model convergence, random_state=42 is enforced globally across all train_test_split, GroupKFold, and RandomForestClassifier instances.



