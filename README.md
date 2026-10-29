
📊 Project 1: Enterprise E-Commerce Data Warehouse & Executive Dashboard
p
• The Business Challenge: A multi-regional e-commerce marketplace was suffering from fragmented, siloed data assets. Executives spent 10+ manual hours a week downloading and merging CSV extracts from different regional databases, leading to stale operational metrics and an undetected 14.2% profit margin leak in their global logistics chain.
• The Project Manager Approach (Agile Sprints): Acted as the Analytics Project Manager to execute stakeholder requirement elicitation sessions, draft a unified data dictionary, build a centralized staging schema using SQL, and model a clean Star Schema data architecture.

🔗 Project Assets

• 🌐 [Click Here to Access the Live Interactive Power BI Dashboard Report]([Insert Your Public Power BI Web Link Here])
• 💻 [Click Here to Access the SQL Queries & Data Schema on GitHub]([Insert Your GitHub Repository URL Here])

🔽 Click to Expand Database Engineering Code (SQL Queries)

sql
-- Query 1: Cohort Lifecycle Analysis - Segmenting Customer Lifetime Value & Order Density
WITH CustomerSalesCohort AS (
    SELECT 
        customer_id,
        COUNT(order_id) AS total_orders,
        SUM(quantity * unit_price) AS total_gross_revenue
    FROM sales_database
    WHERE order_status = 'Delivered' -- Data Cleaning Layer: Eliminating canceled/returned transactions
    GROUP BY customer_id
)
SELECT 
    customer_id,
    total_orders,
    ROUND(total_gross_revenue, 2) AS total_revenue,
    ROUND(total_gross_revenue / total_orders, 2) AS average_order_value_aov,
    CASE 
        WHEN total_gross_revenue >= 5000 THEN 'Tier 1: Enterprise VIP'
        WHEN total_gross_revenue >= 1500 THEN 'Tier 2: Mid-Market Corporate'
        ELSE 'Tier 3: Standard SMB'
    END AS strategic_customer_segment
FROM CustomerSalesCohort
ORDER BY total_revenue DESC;

-- Query 2: Granular Category Profitability & Net Margin Performance Metrics
SELECT 
    p.product_category,
    SUM(s.quantity) AS total_units_sold,
    ROUND(SUM(s.quantity * s.unit_price), 2) AS gross_revenue,
    ROUND(SUM((s.unit_price - p.unit_cost) * s.quantity), 2) AS net_profit,
    ROUND((SUM((s.unit_price - p.unit_cost) * s.quantity) / SUM(s.quantity * s.unit_price)) * 100, 2) AS net_margin_percentage
FROM sales_database s
INNER JOIN product_catalog p ON s.product_id = p.product_id
GROUP BY p.product_category
ORDER BY net_profit DESC
LIMIT 5;
Use code with caution.

🔽 Click to Expand Business Intelligence Logic (PowerBI / DAX Measures)

dax
// Measure 1: Dynamic Robust Revenue Metric Accounting for Matrix Filters
Total Revenue = SUMX(Sales, Sales[Quantity] * Sales[Unit Price])

// Measure 2: Defensive Profit Margin Computation (Engineered to prevent Division-by-Zero Exceptions)
Total Cost = SUMX(Sales, Sales[Quantity] * RELATED(Product[Unit Cost]))
Total Profit = [Total Revenue] - [Total Cost]
Profit Margin % = DIVIDE([Total Profit], [Total Revenue], 0)

// Measure 3: Time-Intelligence Analytics for C-Suite MoM Growth Tracking
Revenue Previous Month = CALCULATE([Total Revenue], PARALLELPERIOD('Calendar'[Date], -1, MONTH))
MoM Revenue Growth % = DIVIDE([Total Revenue] - [Revenue Previous Month], [Revenue Previous Month], 0)
Use code with caution.
• Quantifiable Business Impact:
	• Successfully isolated and flagged a 14.2% margin degradation in international freight calculations.
	• Completely automated corporate data reporting pipelines, reducing executive data-gathering overhead from 10 weekly hours to 0