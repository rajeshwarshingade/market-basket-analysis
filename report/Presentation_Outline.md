# Presentation Outline

**Project:** Market Basket Analysis Using Association Rule Mining  
**Student:** [Student Name] · **Guide:** [Guide Name] · **College:** [College Name]

**Suggested duration:** 10-12 minutes + 3-5 minutes for questions  
**Demo:** open `Market_Basket_Analysis.ipynb` and run the Apriori/rules cells live.

---

## Slide 1 — Title

**On the slide**

- Market Basket Analysis Using Association Rule Mining
- A minor project using Python, Apriori, and the Groceries dataset
- Student name, roll number, department, guide, academic year

**Speaker notes**

> Good morning. My project studies which products are bought together in a supermarket. I use a public
> dataset of 9,835 shopping baskets and the Apriori algorithm to find association rules. The final output is
> a set of measured rules and a few practical suggestions for a store.

---

## Slide 2 — Problem and motivation

**On the slide**

- Stores record thousands of baskets but do not automatically know what is bought together
- "Milk is popular" is not enough; we need combinations
- Market basket analysis finds co-occurrence patterns
- Goal: support better placement, bundles, promotions, and recommendations

**Speaker notes**

> A product being popular does not tell us what to place next to it. Market basket analysis measures
> co-occurrence. The classic example is bread and butter: if they appear together much more often than
> chance, the store can act on it. The project is about measuring that "more often than chance" properly.

---

## Slide 3 — Objectives and scope

**On the slide**

- Clean and describe the transaction data
- Find the most frequent products
- Convert baskets into a one-hot table
- Mine frequent itemsets with Apriori
- Generate and rank rules using support, confidence, and lift
- Visualise the results and interpret them for a retailer

**Speaker notes**

> The scope is descriptive. I do not use prices, profit, customer IDs, or dates. That keeps the project
> focused, but it also creates limitations that I will mention at the end. The analysis is reproducible:
> the notebook uses a fixed seed, although no random sampling is actually needed.

---

## Slide 4 — Dataset

**On the slide**

- Public **Groceries** dataset, distributed with the R package `arules`
- One basket per line; products separated by commas
- **9,835 transactions**, **169 products**
- Average basket: **4.41 items**; median: **3**; largest: **32**
- **2,159 single-item baskets (22.0%)** kept for correct support

**Speaker notes**

> The dataset is an older public snapshot, often described as 1990s grocery data. It has no date field, so
> I cannot verify the exact period. Single-item baskets are kept because they are real purchases and they
> affect the support denominator. Removing them would make every pair look more common than it is.

---

## Slide 5 — Cleaning and one-hot encoding

**On the slide**

- Strip whitespace; lowercase product names
- Remove empty and missing-like tokens
- Remove duplicate items inside a basket
- Drop completely empty baskets
- Keep valid single-item baskets
- Convert to a **9,835 x 169** table of `True`/`False`
- Density: only **2.61%** of cells are `True`

**Speaker notes**

> The full file was already clean: no blank rows, no missing-like tokens, and no duplicate items. The
> cleaning code is still part of the project because real retail files are rarely this tidy. The one-hot
> table is mostly zeros, which is normal for basket data.

---

## Slide 6 — How Apriori works

**On the slide**

- Step 1: count single products; keep those above minimum support
- Step 2: join frequent singles into pairs; count and prune
- Step 3: continue with triples; stop when no larger set is frequent
- **Apriori principle:** if a set is infrequent, every superset is also infrequent
- Threshold used: **1% support = at least 99 baskets**

**Speaker notes**

> Apriori avoids checking every possible combination. If a pair is rare, there is no point testing a triple
> that contains that pair. At 1% support, a product set must appear in at least 99 of the 9,835 baskets.
> That is a business choice; I will show what happens when it changes during the demo.

---

## Slide 7 — The three metrics

**On the slide**

For a rule **A -> B**:

- **Support** = baskets with A and B / all baskets
- **Confidence** = baskets with A and B / baskets with A
- **Lift** = confidence / support(B)

Worked example: `root vegetables -> other vegetables`

- Support **4.74%** (466 / 9,835)
- Confidence **43.47%** (466 / 1,072)
- Lift **2.25** (43.47% / 19.35%)

**Speaker notes**

> Support tells us how common the rule is. Confidence tells us how reliable it is when A is present. Lift
> compares that with the normal popularity of B. Here, other vegetables are already in 19.35% of baskets,
> so a confidence of 43.47% is 2.25 times the baseline. Lift is the number that stops us from being
> impressed by a popular product.

---

## Slide 8 — Most frequent products

**On the slide**

- Show `images/02_top_products.png`
- Whole milk: **2,513 baskets (25.55%)**
- Other vegetables: **1,903 (19.35%)**
- Rolls/buns: **1,809 (18.39%)**
- Soda: **1,715 (17.44%)**
- Yogurt: **1,372 (13.95%)**

**Speaker notes**

> Whole milk is the clear traffic driver: one in four baskets contains it. These products are important for
> store layout because most customers will pass them. But frequency alone does not tell us what to put next
> to what, so we need the rules.

---

## Slide 9 — Frequent itemsets and strongest rules

**On the slide**

- **333 frequent itemsets**: 88 singles, 213 pairs, 32 triples
- **62 rules** after confidence >= 40% and lift > 1
- Top pair: `whole milk + other vegetables` (support **7.48%**)

| Rule | Support | Confidence | Lift |
|---|---:|---:|---:|
| `{citrus fruit, root vegetables} -> other vegetables` | 1.04% | 58.62% | 3.03 |
| `{root vegetables, tropical fruit} -> other vegetables` | 1.23% | 58.45% | 3.02 |
| `{rolls/buns, root vegetables} -> other vegetables` | 1.22% | 50.21% | 2.59 |
| `{curd, yogurt} -> whole milk` | 1.01% | 58.24% | 2.28 |

**Speaker notes**

> The strongest rule is a vegetable rule: if a basket already has citrus fruit and root vegetables, other
> vegetables are about three times more likely than baseline. The dairy rule is also interesting: curd and
> yogurt together are strongly linked to milk, but only about 1% of baskets contain the combination. High
> lift does not always mean high volume.

---

## Slide 10 — Visual insights

**On the slide**

- Show `images/03_support_confidence_lift.png` and `images/04_top_rules.png`
- Scatter: each dot is a rule; colour shows lift
- Upper-right is ideal, but common rules are not the brightest
- Top rules chart: all six strongest rules have lift above 2.2
- Support-lift trade-off: common rules vs strong niche rules

**Speaker notes**

> The scatter plot makes the trade-off visible. The rules on the far right cover more baskets, but they are
> not necessarily the brightest. The strongest rules sit in the upper-middle area: they are reliable, but
> they apply to a smaller share of baskets. A store needs both kinds of rules for different decisions.

---

## Slide 11 — What the store could do

**On the slide**

- **Placement:** group vegetables used in the same meal; keep dairy near milk
- **Bundles:** "vegetable curry pack"; "breakfast basket" with milk, yogurt, butter, eggs
- **Promotions:** discount the complementary item, not milk, which already sells
- **Recommendations:** after root vegetables and citrus fruit, suggest other vegetables (lift 3.03)
- **Caution:** association is not causation; test changes with a controlled experiment

**Speaker notes**

> These are hypotheses grounded in measured co-occurrence, not proven causal effects. For example, the
> vegetable rules may reflect recipe planning, or they may reflect the way the store already arranges
> produce. The correct next step is a small controlled test, not an immediate redesign of the whole store.

---

## Slide 12 — Limitations, future work, and conclusion

**On the slide**

**Limitations**

- Older data; no dates, prices, quantities, customer IDs, stores, or promotions
- Broad categories such as `other vegetables`, not individual SKUs
- Thresholds are choices; no significance testing
- Co-occurrence is not causation

**Future work**

- Add prices and margins; rank by expected profit
- Use newer data and study seasonality/customer segments
- Try FP-Growth for scalability
- Add bootstrap stability checks and A/B tests

**Conclusion:** Apriori found clear, interpretable patterns; the strongest rules are vegetable and dairy
combinations, and they can guide retail experiments.

**Speaker notes**

> To conclude, the project delivers a complete, reproducible workflow and a measured set of rules. The main
> finding is that grocery baskets have structure: vegetables cluster, and dairy products cluster with milk.
> The main caution is that this is descriptive, older data. Thank you; I am happy to take questions.

---

## Demo plan (3-4 minutes)

1. Open `Market_Basket_Analysis.ipynb`.
2. Show the dataset description and the top-products chart.
3. Run the Apriori cell and show 333 itemsets.
4. Run the rules cell and show the 62-rule table.
5. Show the scatter plot and the top-rules chart.
6. **Optional:** change `MIN_SUPPORT` from `0.01` to `0.02`, re-run the Apriori and rules cells, and point
   out that fewer rules remain. This demonstrates that the threshold is a choice.
7. Re-run once more with `0.01` before finishing, or use **Restart & Run All** to restore the original
   outputs.

---

## Likely viva questions and short answers

**1. What is the difference between support and confidence?**  
Support measures how common the combination is in all baskets; confidence measures how often B appears when
A is present. Support is symmetric for A and B; confidence is directional.

**2. Why use lift if confidence is already available?**  
A high confidence can simply mean B is very popular. Lift divides by B's baseline support, so it shows
whether the association is stronger than expected.

**3. Why is the minimum support 1%?**  
It requires at least 99 baskets. It is a practical balance: low enough to find useful pairs, high enough to
avoid many one-off combinations. It is not a statistical law.

**4. Why keep single-item baskets?**  
They are valid transactions and belong in the denominator for support. Removing them would inflate the
support of every pair.

**5. Why did you not remove "shopping bags" or "newspapers"?**  
They are genuine items in the dataset. Removing them would be a data-cleaning decision based on domain
assumptions. The project keeps the data as provided and notes that such non-food items exist.

**6. What does lift = 3 mean?**  
The consequent is three times more likely when the antecedent is present than it is at its normal baseline
rate. It is a measure of association, not proof of causation.

**7. Why can association not prove causation?**  
Both products may be driven by a third factor, such as a promotion, a recipe, a holiday, or store layout.
The data are observational and have no experiment or context.

**8. What are the main limitations?**  
Older snapshot, broad categories, no prices or customer IDs, arbitrary thresholds, no significance testing,
and no validation on a newer dataset.

**9. How would you improve the project?**  
Add prices and margins, use newer data with dates, study seasonality and segments, try FP-Growth, test rule
stability with bootstrap samples, and run A/B tests on a proposed bundle or layout.

**10. Why is the notebook reproducible?**  
All steps are code cells, the random seed is fixed, thresholds are explicit, and the dataset path is
documented. Re-running from top to bottom regenerates every table and chart.

---

## Backup material

- Full report: `report/Project_Report.md` / `report/Project_Report.pdf`
- Results tables: `results/association_rules.csv`, `results/frequent_itemsets.csv`
- Charts: `images/01_basket_sizes.png` to `images/04_top_rules.png`
- If the dataset is unavailable: the notebook automatically uses a 40-basket sample and raises the support
  threshold to 15%; the numbers will differ from the full-data results.
