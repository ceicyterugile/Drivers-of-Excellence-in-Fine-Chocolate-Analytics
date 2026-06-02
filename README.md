# Capstone Project: Drivers of Excellence in Fine Chocolate Analytics

##  Executive Summary
This Capstone Project executes an end-to-end data science analysis of the global chocolate bar ratings dataset (2006–2021) to scientifically isolate the structural and composition-driven parameters that determine superior product quality. 

By translating subjective rating matrices into quantitative, actionable market intelligence, this pipeline provides the Product Development Team and the CEO with data-backed blueprints to optimize international ingredient sourcing, formulation chemistry, and strategic supplier partnerships.

##  Project Context & Business Problem
- **Data Source:** Clickstream expert evaluations from Kaggle (`2,530 rows` across `11 key features`).
- **Target Audience:** Product Development (Formulation Metrics) and the CEO (Strategic Capital & Sourcing Allocation).
- **Core Challenge:** The fine chocolate ecosystem is highly saturated; quality perception dictates market differentiation. The goal is to move past anecdotal tasting logs and build statistical validation models defining what criteria guarantee a premier product rating (defined as `Rating >= 3.75`).

##  Data Architecture & Core Schema Map
The data validation framework focuses on target variables mapped against specific feature engineering proxies:


| Feature Name | Data Type | Analytical Role & Business Proxy |
| :--- | :--- | :--- |
| **rating** | REAL (0.0 - 4.0) | Target Variable (The objective measure of quality perception). |
| **bean_origin** | TEXT | Categorical Variable (Geographic parameter / Terroir impact). |
| **cocoa_percent** | REAL (%) | Numerical Variable (Intensity and cocoa concentration formulation impact). |
| **num_ingredients**| INTEGER | Feature Engineering Proxy (Product complexity / pure-recipe validation). |

---

##  Multi-Layered Data Methodology & Techniques

### Technique 1: Longitudinal Cohort Analysis (Temporal Trends)
* **Goal:** Group data inputs into annual review cohorts to monitor shifts in ingredient complexity metrics and measure global preference variances across a 15-year timeline.
* **Outcome:** Determines whether specific product score variances are driven by real quality evolution or macro-reviewer grading stringency over time.

### Technique 2: A/B Testing Simulation (Causal Inference)
* **Goal:** Formulate a statistical hypothesis experiment via a two-sample t-test ($H_0: \mu_1 = \mu_2$) evaluating the categorical presence of specialized additives (Vanilla) versus unadulterated base lines.
* **Execution Boundary:** Calculated at an alpha threshold ($\alpha = 0.05$) to measure true causal influence on end-user ratings.

### Technique 3: Simplified RFM Sourcing Segmentation Matrix
* **Goal:** Classify international manufacturers by mapping their Quality indexes (Average Rating) against Scale/Consistency metrics (Total Review Count).
* **Strategic Target:** Segment suppliers into distinct tiers (e.g., *"VIP Partners"*, *"High Potential"*) to optimize long-term manufacturing agreements.

---

##  Technical Implementation & Production SQL Queries

### 1. Granular Geographic Terroir (Bean Origin) Analytics
Isolates the top 10 international cocoa bean locations by tracking long-term average ratings filtered for statistical significance (minimum 10 historic reviews globally and 3 reviews per year):

```sql
SELECT
    bean_origin,
    year_reviewed,
    COUNT(id) AS Total_Reviews_in_Year,
    ROUND(AVG(rating), 3) AS Average_Rating_in_Year
FROM
    chocolate_bars
WHERE
    bean_origin IS NOT NULL
    AND year_reviewed IS NOT NULL
    AND rating IS NOT NULL
GROUP BY
    bean_origin,
    year_reviewed
HAVING
    COUNT(id) >= 3
    AND bean_origin IN (
        SELECT bean_origin 
        FROM chocolate_bars 
        GROUP BY bean_origin 
        HAVING COUNT(id) >= 10
        ORDER BY AVG(rating) DESC 
        LIMIT 10
    )
ORDER BY
    bean_origin ASC,
    year_reviewed ASC;
```
* **Top Strategic Sourcing Insights:** Uganda leads global data sets with an average tier score of `3.417`, followed closely by Indonesia (`3.350`) and Grenada (`3.333`).

### 2. High-Performance Manufacturer Scaling Verification
Filters out low-volume suppliers to isolate high-quality, high-consistency manufacturers holding over 10 active product market placements:

```sql
SELECT
    manufacturer,
    COUNT(id) AS Total_Bars_Reviewed,
    ROUND(AVG(rating), 3) AS Average_Rating 
FROM
    chocolate_bars
WHERE
    rating IS NOT NULL
GROUP BY
    manufacturer
HAVING
    COUNT(id) >= 10
ORDER BY
    Average_Rating DESC
LIMIT 10;
```
* **Top Strategic Manufacturing Insights:** With scale controls applied, "Patric" emerges as the premium global supplier (`3.792` average score), followed closely by "Idilio (Felchlin)" at `3.775`.

### 3. Company Location & Manufacturing Base Infrastructure Evaluation
Analyzes geographic country performance across locations with 5 or more distinct reviews to determine the absolute strongest international market hubs:

```sql
SELECT
    company_location,
    COUNT(id) AS Total_Bars_Reviewed,
    ROUND(AVG(rating), 3) AS Average_Rating 
FROM
    chocolate_bars
WHERE
    rating IS NOT NULL
GROUP BY
    company_location
HAVING
    COUNT(id) >= 5
ORDER BY
    Average_Rating DESC
LIMIT 10;
```
* **Top Regional Insights:** The United Arab Emirates (`3.400`), Poland (`3.375`), and Vietnam (`3.359`) lead production density for premier quality consistency.

---

##  Visualization Canvas & Deliverables
- **Dashboard Stack:** Built comprehensive, interactive geographical and categorical scatter matrices within **Tableau**.
- **Deliverable Target:** Standardized distribution colors and layout alignments to allow C-level executives to dynamically map ingredient metrics instantly.

a link to the edited Tableau visualization: https://public.tableau.com/app/profile/rugile.ceicyte/viz/DriversofExcellenceinFineChocolate/Dashboard2?publish=yes
