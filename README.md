# E-commerce Funnel & Revenue Intelligence Analysis

## Executive Summary
This project evaluates the end-to-end e-commerce funnel and commercial performance across sessions, events, orders, products, and customer feedback. The business objective is to identify conversion leakages, quantify revenue and margin drivers, and surface actionable growth opportunities across acquisition, merchandising, and retention. The analysis integrates 1.01M+ records from seven connected datasets covering customer profiles, browsing events, purchase transactions, product economics, and reviews over 2020-01 to 2025-10.

The business impact focus is threefold: (1) improve funnel efficiency from traffic to purchase, (2) optimize category and product mix for both revenue and gross margin, and (3) strengthen customer value concentration through segment-specific actions. Current performance shows $4.49M total revenue from 33,580 orders and a 27.98% session-to-purchase conversion. While checkout-to-purchase conversion is relatively strong (74.77%), the largest drop occurs from add-to-cart to checkout (55.09%), indicating a major opportunity in checkout initiation.

Commercially, performance is concentrated: VIP customers (top quartile by monetary value) contribute 57.62% of revenue, and 62.93% of products generate ~80% of sales, suggesting a long-tail assortment effect. Category leaders are Home & Kitchen (17.39% share), Sports (17.21%), and Fashion (17.06%). Mobile is the largest order-revenue device. Customer sentiment is healthy (average rating 3.93/5; 70.69% ratings >=4), but operational optimization is still needed to reduce funnel abandonment and improve channel productivity. The project delivers a business KPI framework, data quality audit, analytical deep-dive, and prioritized recommendations ready for portfolio and stakeholder use.

## Business Problem Statement
### Current Situation
The business has high traffic and meaningful transaction volume, but leadership lacks a unified KPI view connecting session behavior, funnel progression, order economics, and product/category contribution.

### Business Challenges
- Conversion leakages are not quantified by stage.
- Revenue and margin concentration risks are unclear.
- Channel and device performance is not fully benchmarked.
- Customer value distribution and repeat behavior need stronger evidence.

### Business Questions
- Where does the funnel lose the highest proportion of users?
- Which categories, products, channels, and devices drive the strongest business outcomes?
- How concentrated is revenue across customers and products?
- Which KPIs are healthy vs. at risk?

### Expected Outcomes
- Clear KPI framework with current performance status.
- Funnel and commercial root-cause insights.
- Prioritized actions to increase conversion, revenue quality, and customer lifetime value.

## Project Scope
### In Scope
- Multi-table integration across customers, sessions, events, orders, order items, products, and reviews.
- Data quality assessment and business-safe cleaning.
- Funnel analytics, KPI tracking, trend and contribution analysis.
- Customer and product segmentation views.

### Out of Scope
- Causal experimentation design (A/B test execution).
- Predictive ML deployment.
- Real-time production dashboard implementation.

### Assumptions
- Event sequence and order data represent the same business process lifecycle.
- Monetary fields are in USD and consistently comparable.
- Missing values in certain event fields are expected by event type context.

### Limitations
- No explicit marketing spend data, limiting ROI/CAC analyses.
- No granular logistics timestamps for delivery SLA analysis.
- No historical inventory stock-out signals.

## Dataset Understanding
### Dataset Overview
Seven CSV datasets were analyzed as an integrated transactional + behavioral e-commerce model.

### Number of Records
- customers: 20,000
- sessions: 120,000
- events: 760,958
- orders: 33,580
- order_items: 59,163
- products: 1,197
- reviews: 10,780

### Number of Columns
- customers: 7
- sessions: 6
- events: 10
- orders: 10
- order_items: 5
- products: 6
- reviews: 6

### Data Types
Mix of integer IDs, categorical/text fields, boolean, floating-point metrics, and timestamp fields.

### Features Description (Data Dictionary)
| Column | Description | Data Type |
|---|---|---|
| customer_id | Unique customer identifier | int |
| name | Customer full name | string |
| email | Customer email address | string |
| country | Customer country / market | string |
| age | Customer age | int |
| signup_date | Customer account creation date | datetime |
| marketing_opt_in | Consent for marketing communication | bool |
| session_id | Unique browsing session identifier | int |
| start_time | Session start timestamp | datetime |
| device | Device type used | string |
| source | Traffic acquisition channel | string |
| event_id | Unique event record identifier | int |
| timestamp | Event timestamp | datetime |
| event_type | Funnel step (page_view, add_to_cart, checkout, purchase) | string |
| product_id | Product identifier tied to event/order line | int/float |
| qty | Quantity in event context | float |
| cart_size | Number of items in cart snapshot | float |
| payment | Payment method used in event | string |
| discount_pct | Discount percentage applied | int/float |
| amount_usd | Monetary amount in event context | float |
| order_id | Unique order identifier | int |
| order_time | Order placement timestamp | datetime |
| payment_method | Order payment method | string |
| subtotal_usd | Order subtotal before discount | float |
| total_usd | Final paid order amount | float |
| unit_price_usd | Product unit price at transaction | float |
| quantity | Units purchased per line item | int |
| line_total_usd | Line-item total revenue | float |
| category | Product category | string |
| price_usd | Product list price | float |
| cost_usd | Product cost basis | float |
| margin_usd | Unit margin estimate | float |
| review_id | Unique review identifier | int |
| rating | Product/order rating (1-5) | int |
| review_text | Free-text review comment | string |
| review_time | Review timestamp | datetime |

## Business KPI Framework
### KPI Definition Table
| KPI | Formula | Business Meaning |
|---|---|---|
| Total Revenue | SUM(total_usd) | Overall commercial scale |
| Total Orders | COUNT(DISTINCT order_id) | Transaction volume |
| Unique Purchasing Customers | COUNT(DISTINCT customer_id in orders) | Active customer base |
| Average Order Value (AOV) | Total Revenue / Total Orders | Revenue efficiency per order |
| Repeat Purchase Rate | Customers with >1 orders / Purchasing customers | Retention and loyalty signal |
| Average Items per Order | Mean(sum(quantity) by order_id) | Basket depth |
| Avg Discount % | AVG(discount_pct) | Pricing/promo intensity |
| Estimated Gross Margin % | SUM(quantity*margin_usd) / Total Revenue | Profitability quality |
| Session-to-Purchase Conversion | Purchase sessions / Total sessions | Funnel effectiveness |
| View-to-Cart Conversion | add_to_cart sessions / page_view sessions | Product-page intent conversion |
| Cart-to-Checkout Conversion | checkout sessions / add_to_cart sessions | Checkout initiation efficiency |
| Checkout-to-Purchase Conversion | purchase sessions / checkout sessions | Payment and completion efficiency |
| Average Rating | AVG(rating) | Customer satisfaction proxy |

## Data Quality Assessment
### Audit Findings
- Missing values are concentrated in events table columns not applicable to all event types (contextual missingness).
- `order_items` contains 73 duplicate rows.
- No duplicate records in other primary tables.
- No negative order totals; no subtotal-total consistency violations.
- Customer age values are within plausible operational range.

| Issue | Impact | Resolution |
|---|---|---|
| 73 duplicate records in order_items | Inflated units/revenue if untreated | Remove exact duplicate rows before aggregations |
| Event-level nulls in product/payment/amount fields | Potential misinterpretation as data loss | Keep nulls where event context does not require field; use event-type-aware analysis |
| Datetime fields stored as string | Time trend and cohort analysis blocked | Parse into datetime during loading |
| Mixed semantic nulls in funnel events | Biased KPI if naively filled | Apply rule-based validation by event type |

### Potential Business Risks
- If duplicates are not removed, product and margin decisions may be skewed.
- If contextual nulls are misclassified as errors, teams may over-invest in unnecessary data engineering.

## Data Cleaning
1. Parsed all timestamp columns (`signup_date`, `start_time`, `timestamp`, `order_time`, `review_time`).
2. Removed 73 duplicated rows from `order_items`.
3. Standardized text categories (trim/case normalization) for device/source/category columns.
4. Validated numeric constraints (`total_usd >= 0`, `subtotal_usd >= total_usd`, positive quantities).
5. Preserved event contextual nulls with documented event-wise rules.

### Before-After Snapshot
- `order_items` rows: 59,163 -> 59,090
- Duplicate count: 73 -> 0
- Datetime parse status: string -> datetime-enabled analytical columns

## Feature Engineering
| Feature | Logic | Business Purpose |
|---|---|---|
| order_month | order_time truncated to month | Monthly trend and growth tracking |
| order_quarter | quarter from order_time | Quarterly planning and seasonality |
| order_weekday | weekday from order_time | Day-pattern demand planning |
| margin_total | quantity * margin_usd | Profitability by product/category |
| revenue_share_pct | group revenue / total revenue | Contribution analysis |
| customer_value_segment | quartile on customer monetary value | Segment-based targeting |
| abc_class | cumulative product revenue buckets (A/B/C) | Portfolio rationalization |
| funnel_stage_flags | session-wise event stage presence | Drop-off and conversion diagnostics |

## Exploratory Data Analysis
### Univariate Analysis
- Revenue per order is right-skewed with median below mean, indicating occasional high-value baskets.
- Ratings cluster around 4-5, consistent with a generally positive product experience.
- Category unit distributions show stronger volume concentration in Toys/Books/Beauty, while Electronics is higher-value with lower unit volume.

### Bivariate Analysis
- Discount percentage shows non-linear relationship with order value; high discounts do not consistently yield higher totals.
- Device vs AOV differences are moderate, while device vs order volume differences are material (mobile leads volume).
- Source-level comparisons indicate referral has relatively high AOV but lower scale than organic/direct.

### Multivariate Analysis
- Correlation heatmaps indicate strong positive alignment between quantity, line totals, and margin totals.
- Country-source-device groupings reveal pockets of high value that are masked in overall averages.
- Category-margin-revenue matrix highlights balanced leaders (Home & Kitchen, Sports, Fashion).

## Advanced Business Analysis
### Funnel Analysis
- Total sessions: 120,000
- Page-view sessions: 120,000
- Add-to-cart sessions: 81,518 (67.93% of sessions)
- Checkout sessions: 44,909 (37.42% of sessions)
- Purchase sessions: 33,580 (27.98% of sessions)

Biggest leakage occurs at cart -> checkout (55.09% stage conversion), signaling friction before payment.

### Revenue and Trend Analysis
- Total revenue: $4,493,217.47
- Best month: 2021-07 ($76,474.39)
- Lowest month: 2023-02 ($52,539.32)
- Mean monthly growth: 0.75% with high volatility (std 12.11%).

### Category and Product Analysis
Top revenue categories:
1. Home & Kitchen (17.39%)
2. Sports (17.21%)
3. Fashion (17.06%)

Pareto pattern is broad-tail: 62.93% of products contribute 80% of revenue, indicating less extreme concentration than typical 20/80.

### Customer Analysis
- Purchasing customers: 16,268
- Repeat purchase rate: 61.75%
- VIP quartile contributes 57.62% of total revenue.

### Channel/Device Analysis
- Source leaders by revenue: Organic > Direct > Paid.
- Mobile contributes highest revenue and order volume.

### Satisfaction Analysis
- Average rating: 3.93/5
- Positive reviews (4-5): 70.69%

## KPI Performance Review
| KPI | Current Value | Insight | Status |
|---|---:|---|---|
| Total Revenue | $4,493,217 | Strong scale but volatile monthly trend | Moderate |
| Total Orders | 33,580 | Healthy transactional volume | Good |
| Unique Purchasing Customers | 16,268 | Broad active base | Good |
| AOV | $133.81 | Stable mid-range basket value | Moderate |
| Repeat Purchase Rate | 61.75% | Strong retention signal | Good |
| Avg Items per Order | 2.29 | Moderate cross-sell depth | Moderate |
| Avg Discount % | 7.16% | Controlled promo intensity | Good |
| Estimated Gross Margin % | 38.11% | Healthy profitability profile | Good |
| Session-to-Purchase Conversion | 27.98% | Improvement opportunity in upper funnel | Moderate |
| View-to-Cart Conversion | 67.93% | Product consideration is strong | Good |
| Cart-to-Checkout Conversion | 55.09% | Largest funnel friction point | Critical |
| Checkout-to-Purchase Conversion | 74.77% | Completion is relatively efficient | Good |
| Average Rating | 3.93/5 | Good sentiment with room to improve | Moderate |

## Visualization Section
1. Revenue trend line (monthly):
   - Why it matters: tracks growth stability and seasonal patterns.
   - Key takeaway: stable long-run scale with short-term volatility.
2. Funnel bar chart:
   - Why it matters: quantifies stage-wise drop-off.
   - Key takeaway: cart-to-checkout is the biggest leakage stage.
3. Category revenue + margin chart:
   - Why it matters: balances top-line and profitability quality.
   - Key takeaway: Home & Kitchen, Sports, Fashion are balanced leaders.
4. Source and device contribution charts:
   - Why it matters: guides channel budgeting and UX prioritization.
   - Key takeaway: organic/direct scale strongest; mobile is dominant device.
5. Product Pareto curve:
   - Why it matters: supports assortment and inventory strategy.
   - Key takeaway: revenue concentration exists but spread across many SKUs.
6. Correlation heatmap:
   - Why it matters: identifies metric relationships and redundancy.
   - Key takeaway: order line value and margin metrics are tightly coupled.

## Key Insights
1. Cart-to-checkout is the primary conversion bottleneck (55.09% stage conversion).  
Business implication: checkout start UX and trust signals should be top priority.

2. Checkout-to-purchase conversion is relatively high (74.77%).  
Business implication: once users begin checkout, completion mechanics are effective.

3. Mobile is the highest revenue and volume device channel.  
Business implication: mobile-first optimization will provide outsized returns.

4. Organic and direct traffic drive most revenue.  
Business implication: brand and SEO performance are strategic growth anchors.

5. Referral channel has comparatively high AOV but lower order volume.  
Business implication: scaling referral traffic can improve revenue quality.

6. VIP customers contribute 57.62% of revenue.  
Business implication: retention and win-back for top-tier users is critical risk control.

7. Repeat purchase rate is robust (61.75%).  
Business implication: lifecycle marketing can efficiently compound LTV.

8. Estimated gross margin remains healthy (38.11%).  
Business implication: room exists for selective promotions without severe margin erosion.

9. Category leadership is distributed across Home & Kitchen, Sports, and Fashion.  
Business implication: growth strategy should avoid over-reliance on a single category.

10. Product concentration is moderate (62.93% SKUs for 80% revenue).  
Business implication: assortment pruning should be selective, not aggressively broad.

11. Monthly revenue growth has high volatility (std 12.11%).  
Business implication: planning and inventory should include volatility buffers.

12. Positive review share is strong (70.69%).  
Business implication: social proof can be leveraged further in conversion journeys.

## Business Recommendations
### Recommendation 1
**Problem:** High drop-off from cart to checkout.  
**Suggested Action:** Simplify checkout entry, add persistent cart reminders, and surface trust/returns messaging on cart page.  
**Expected Impact:** Higher checkout starts and improved overall conversion.  
**Priority:** High

### Recommendation 2
**Problem:** Revenue concentration in VIP segment creates dependency risk.  
**Suggested Action:** Launch VIP retention program (early access, concierge support, loyalty multipliers) and proactive churn flags.  
**Expected Impact:** Reduced revenue volatility and stronger LTV retention.  
**Priority:** High

### Recommendation 3
**Problem:** Referral has strong AOV but limited scale.  
**Suggested Action:** Expand referral incentives and partner placements while preserving qualification quality.  
**Expected Impact:** Higher-quality order mix and incremental revenue.  
**Priority:** Medium

### Recommendation 4
**Problem:** Monthly revenue volatility complicates planning.  
**Suggested Action:** Implement rolling demand forecasting and category-level inventory triggers linked to trend shifts.  
**Expected Impact:** Better stock allocation, fewer over/under-stock events.  
**Priority:** Medium

### Recommendation 5
**Problem:** Not all categories contribute equally to revenue and margin quality.  
**Suggested Action:** Use category scorecards (revenue share, margin share, conversion, returns proxy) to steer merchandising and promotions.  
**Expected Impact:** Better gross-profit mix and campaign efficiency.  
**Priority:** High

### Recommendation 6
**Problem:** Review sentiment is positive but not fully monetized.  
**Suggested Action:** Promote high-rated products in landing pages, PDP widgets, and paid retargeting creatives.  
**Expected Impact:** Higher trust-driven conversion and improved ad efficiency.  
**Priority:** Medium

## Conclusion
The analysis shows a commercially healthy e-commerce business with strong retention indicators, balanced category leadership, and robust margin quality. The main performance constraint is pre-checkout friction, not final payment completion. Revenue concentration in high-value customers creates both opportunity and risk, while traffic/channel dynamics indicate mobile and organic/direct ecosystems should remain strategic priorities. Operationally, the business can gain measurable growth by improving cart-to-checkout progression, scaling high-quality channels, and formalizing segment-driven CRM and merchandising actions.

## Future Enhancements
- Build an executive BI dashboard with real-time KPI tracking and funnel alerts.
- Add forecasting models for monthly revenue, demand, and category seasonality.
- Develop churn propensity and next-best-action models for customer lifecycle targeting.
- Implement recommendation system experiments for cart/pdp personalization.
- Automate anomaly detection for conversion, margin, and channel performance deviations.
