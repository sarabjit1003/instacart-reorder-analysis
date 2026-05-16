# Instacart Reorder Behaviour Analysis
### Identifying what drives customer loyalty across 32 million grocery orders
## The Business Problem

Instacart's growth depends not just on acquiring new customers but on
keeping existing ones coming back. Reorder behaviour is the clearest
signal of customer loyalty — when someone buys the same product again,
it means the last experience was good enough to repeat.


> **What drives a customer to reorder  and what gets in the way?**


## Dataset

| File | Rows | Description |
|---|---|---|
| orders.csv | 3,421,083 | Every order placed — time, day, user |
| order_products_prior.csv | 32,434,489 | Every product in every order |
| products.csv | 49,688 | Product names and categories |
| departments.csv | 21 | Department names |
| aisles.csv | 134 | Aisle categories |

**Source:** [Instacart Market Basket Analysis — Kaggle](https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis)  
**Period:** 2017 Instacart transaction data


## Tools and Approach

- **Python + pandas** — data loading, multi-table joins, aggregation
- **Kaggle Notebooks** — cloud compute, no local setup required
- **GitHub** — version control and portfolio documentation

### Q1 — What is the baseline reorder rate?

**SQL equivalent:** `SELECT AVG(reordered) FROM order_products`

**Finding:**

| Metric | Value |
|---|---|
| Overall reorder rate | **58.97%** |
| Total orders analyzed | 32,434,489 |
| Total reorders | 19,126,536 |

Roughly **6 in every 10 products** a customer adds to cart is something
they have bought before. This becomes the baseline every subsequent
finding is measured against.


### Q2 — Which departments drive the most loyalty?

**SQL equivalent:** `SELECT department, AVG(reordered) FROM ... GROUP BY department ORDER BY 2 DESC`

**Finding:**

| Department | Reorder Rate | vs Baseline |
|---|---|---|
| Dairy & Eggs | 67.00% | +8.0 pts |
| Beverages | 65.35% | +6.4 pts |
| Produce | 64.99% | +6.0 pts |
| Bakery | 62.81% | +3.8 pts |
| Snacks | 57.42% | -1.6 pts |
| Pantry | 34.67% | -24.3 pts |
| Personal Care | 32.11% | -26.9 pts |

Departments split into two distinct behavioural categories:

**Habit departments** — dairy, beverages, produce. Customers reorder
these on autopilot. Weekly staples they do not think twice about.
Reorder rates 6–8 points above baseline.

**Discovery departments** — personal care, pantry, international.
Customers try once and rarely return to the exact same product.
Reorder rates 25–27 points below baseline.

**Business implication:** The recommendation engine should not treat
all departments the same. Habit departments need stock reliability
and subscription nudges. Discovery departments need "similar products
you might like" recommendations to convert one-time buyers.


### Q3 — When do customers make reorder decisions?

**SQL equivalent:** `SELECT order_hour_of_day, AVG(reordered) FROM ... GROUP BY 1`

**Finding:**

| Time Window | Reorder Rate |
|---|---|
| 7:00am — peak hour | 64.45% |
| 6:00am–8:00am window | 63–64% |
| 2:00am — lowest hour | 55.45% |
| Sunday — peak day | 60.38% |
| Friday — lowest day | 57.44% |

Reorder rate peaks sharply between 6am and 8am and is highest on
Sundays. Morning orders are habitual customers planning their week
and restocking familiar products. Late night browsing is exploratory
and skews toward first-time purchases.

The swing from 2am (55.45%) to 7am (64.45%) is a **9 percentage point**
difference purely based on time of day.

**Business implication:** Push notifications and reorder reminder
emails should be sent Sunday mornings between 6am and 8am.
That is the single highest-conversion re-engagement window
in the entire dataset.

### Q4 — Do frequent shoppers reorder more?

**SQL equivalent:** `SELECT frequency_segment, AVG(reordered) FROM ... GROUP BY 1`

**Finding:**

| Customer Segment | Orders | Reorder Rate |
|---|---|---|
| Low — 1 to 5 orders | 1,441,523 | 23.46% |
| Medium — 6 to 15 orders | 7,794,701 | 42.21% |
| High — 16 to 30 orders | 8,523,249 | 58.26% |
| Very High — 31+ orders | 14,675,016 | **71.77%** |

There is a clean, linear relationship between how often a customer
shops and how likely they are to reorder. Very high frequency
customers reorder at **3x the rate** of low frequency customers —
a 48 percentage point gap.

Critically, very high frequency customers also place the most orders
(14.7M of 32M total). They are not just more loyal, they are the
majority of the business.

**Business implication:** Retention is more valuable than acquisition
in this dataset. Keeping one very high frequency customer is worth
roughly 3x acquiring a new occasional shopper. An intervention at
the medium frequency stage or a loyalty reward, a subscription nudge
at order 10 or 12 — could accelerate the shift to high frequency
behaviour and meaningfully increase lifetime value.


## Final Recommendations

Three specific actions follow directly from this analysis:

**1. Protect dairy, beverages, and produce above everything else.**
These three departments drive 65–67% reorder rates across nearly
18 million orders. Any stock reliability issue here breaks customer
habits and risks losing your highest-value users.

**2. Send reorder reminders on Sunday mornings between 6 and 8am.**
This is the single highest-conversion re-engagement window identified
in the data. Reorder rate at 7am on a Sunday is 64.45% which is nearly
9 points above the 2am low. Notification timing is free to change
and the impact is immediate.

**3. Build a loyalty intervention at the medium frequency stage.**
The jump from medium (42%) to high (58%) to very high (71%) reorder
rate is not random, it reflects a behavioural threshold. Customers
who cross it become habitual. A small incentive at the 10–12 order
mark could accelerate that shift and significantly increase
customer lifetime value.



## Key Technical Concepts Demonstrated

| Concept | pandas function | SQL equivalent |
|---|---|---|
| Load relational data | pd.read_csv() | — |
| Join tables | .merge(on=, how='left') | LEFT JOIN ON |
| Aggregate by group | .groupby().agg() | GROUP BY + AVG / COUNT |
| Rate from binary column | .mean() on 0/1 column | AVG() on boolean |
| Bucket continuous values | pd.cut() | CASE WHEN ranges |
| Map codes to labels | .map(dictionary) | CASE WHEN lookup |


## Live Notebook

Full code with comments, all outputs, and markdown analysis:  
[View on Kaggle](https://www.kaggle.com/code/sarabjitmehta/instacart-market-basket-analysis) 

[LinkedIn](https://www.linkedin.com/in/sarabjitmehta/)
