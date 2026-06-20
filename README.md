- **Fact Table:** `fact_sales` (6,711 records)
- **Dimension Tables:** `dim_account`, `dim_product`, `dim_sales_team`, `dim_date`
- `dim_date` was custom-built in Power Query (M Language) with Year, 
  Quarter, Month, and Weekday hierarchies for time intelligence.

---

## 💻 SQL Analysis Highlights

20+ exploratory queries covering:
- Multi-table JOINs across agents, accounts, and products
- Window functions (`RANK`, `LAG`) for performance comparison
- CTEs for above/below-average agent identification
- Subqueries for product win-rate benchmarking
- Date functions for monthly/quarterly trend analysis

📄 [View full SQL file](sql/maven_sales_analysis.sql)

```sql
-- Example: Identify agents performing above team average
WITH agent_stats AS (
    SELECT sales_agent, 
           ROUND(SUM(close_value), 2) AS total_revenue
    FROM sales_pipeline
    WHERE deal_stage = 'Won'
    GROUP BY sales_agent
)
SELECT sales_agent, total_revenue
FROM agent_stats
WHERE total_revenue > (SELECT AVG(total_revenue) FROM agent_stats)
ORDER BY total_revenue DESC;
```

---

## 📐 DAX Measures

20+ custom measures including:
- **Time Intelligence:** Revenue QTD, MTD, YTD, QoQ Change
- **Performance Logic:** Custom `Performance Tag` measure classifying 
  agents as Top Performer / On Track / Needs Support based on win 
  rate vs. team average
- **Core KPIs:** Win Rate %, Avg Deal Value, Avg Deal Cycle Days

```dax
Performance Tag = 
VAR AgentWinRate = [Win Rate by Agent]
VAR TeamAvg = CALCULATE([Win Rate %], ALL(dim_sales_team[sales_agent]))
RETURN
SWITCH(
    TRUE(),
    AgentWinRate >= TeamAvg + 0.1, "⭐ Top Performer",
    AgentWinRate <= TeamAvg - 0.1, "⚠️ Needs Support",
    "✓ On Track"
)
```

---

## 📈 Dashboard Pages

### 1. Executive Overview
![Executive Overview](screenshots/01_executive_overview.png)
High-level KPIs, quarterly revenue trend, and top-agent snapshot 
for quick team health checks.

### 2. Agent Performance
![Agent Performance](screenshots/02_agent_performance.png)
Individual agent breakdown with conditional formatting and a custom 
Performance Tag to instantly flag who needs coaching support.

### 3. Product Analysis
![Product Analysis](screenshots/03_product_analysis.png)
Product-level revenue and win-rate analysis, including a 
demand-vs-revenue scatter plot to identify high-volume vs 
high-margin products.

---

## 🔍 Key Business Insights

**1. Revenue Decline Post-Q2**
Revenue peaked in Q2 2017 and declined through Q3-Q4, signaling a 
need to investigate what drove Q2 success and replicate it.

**2. 24-Point Win Rate Gap Between Agents**
Top performer (65% win rate) outperforms the lowest-performing 
agents by 24+ points — suggesting a coaching/mentorship opportunity 
rather than territory or luck differences.

**3. GTX Pro Drives Highest Revenue**
Despite a moderate 49% win rate, GTX Pro generates $3.51M in revenue 
— the highest of any product — confirming its premium positioning 
is working.

**4. GTK 500 Underperforms Across the Board**
Lowest win rate (38%) and lowest deal volume (40 deals) — a 
candidate for repricing or discontinuation.

---

## 📌 Key Assumptions

- Dataset is fully historical (Oct 2016 – Dec 2017); all deals are 
  either Won or Lost, with no open pipeline
- "Current Quarter" refers to Q4 2017, the most recent period in the data
- Dashboard designed primarily for sales managers tracking their own 
  team's performance

---

## 🚀 What I Learned

This project took me through the complete analyst workflow — not 
just building charts, but understanding data end-to-end: writing 
SQL to explore raw CRM data, designing a proper star schema, 
debugging real data quality issues (a product name mismatch that 
was hiding $3.5M in revenue from every visual), and translating 
numbers into business recommendations.

---

## 🔗 Connect

- LinkedIn: [Vaishnavi Patil](https://www.linkedin.com/in/vaishnavi-patil-964273319/)
- GitHub: [@vaishnavipatil6933-web](https://github.com/vaishnavipatil6933-web)
