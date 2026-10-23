Customer Lifetime Value (CLV) Prediction & Behavioral SegmentationAn-
end-to-end predictive customer analytics pipeline that ingests raw retail transaction logs, extracts behavioral RFM metrics, trains probabilistic and unsupervised machine learning models to forecast 90-day customer lifetime value and churn risk, and exports an enriched analytical table for Power BI dashboard reporting.
Business Problem -
Retail marketing teams often allocate acquisition and retention budgets uniformly across their customer base using broad promotional campaigns. This approach leads to inefficient capital allocation:Over-investing in low-margin, high-churn shoppers who generate negative net ROI.Under-protecting high-value repeat shoppers who are quietly slipping into churn.This project delivers an automated risk-scoring and lifetime value projection engine that separates high-risk, high-value accounts from self-sustaining champions, enabling targeted win-back campaigns and automated retention triggers.
Architecture & WorkflowPlaintext[Raw Retail Transactions (Online Retail II)]
                   │
                   ▼
       [Data Cleaning & Sanitation]
     - Filter cancelled orders & missing IDs
     - Parse multi-format transaction timestamps
                   │
                   ▼
     [Feature Engineering: Daily RFM]
     - Recency, Frequency, Tenure (T), Monetary Value
                   │
                   ▼
       [Machine Learning Models]
     ├── BG/NBD Model: P(Alive), Churn Risk, 90-Day Purchase Counts
     ├── Gamma-Gamma Model: Expected Basket Spend & 3-Month CLV
     └── K-Means + Median Matrix: Behavioral Segmentation
                   │
                   ▼
   [customer_clv_segments.csv (Enriched)]
                   │
                   ▼
        [Power BI Desktop Dashboard]
     - Executive KPI Cards & Portfolio Health
     - 2x2 Risk vs. Value Quadrant Scatter Plot
     - Dynamic Retention ROI / What-If Slicers
Machine Learning Pipeline -
1. Data Cleaning & Feature Extraction -
    Stripped unassigned guest checkouts and cancelled invoice records (C*).Transformed line-item receipt records into a customer-level RFM representation via the lifetimes framework:Frequency (F): Count of repeat transaction dates (F > 0).Recency (R): Days elapsed between customer acquisition and their most recent order.Tenure (T): Total lifespan in days from acquisition to the observation period cutoff.Monetary Value (M): Average order spend across repeat purchases.
2. Purchase Cadence & Churn Modeling (BG/NBD)
   Implemented the Beta-Geometric / Negative Binomial Distribution (BG/NBD) model to capture latent transaction pacing and drop-out rates.Outputs:prob_alive: Probability that the account remains active.churn_risk: $1 - P(\text{Alive})$.predicted_purchases_90d: Projected transaction counts over the next 90 days.
3. Monetary Value & CLV Projection (Gamma-Gamma)
   Validated the independence assumption between purchase frequency and average basket spend.Trained a Gamma-Gamma Submodel with Bayesian shrinkage to adjust observed spending against overall population distributions.Outputs:expected_avg_spend: Normalized future transaction value.clv_3_months: Discounted expected cash flow across the subsequent quarter.
4. Unsupervised Customer Segmentation (K-Means & Strategic Quadrants)
   Log-transformed skewed monetary figures ($\ln(1 + \text{CLV})$) and normalized variables using StandardScaler.Clustered accounts into 4 distinct operating tiers via K-Means Clustering and mapped them into an executive $2 \times 2$ matrix:Champions: Low churn risk, above-median CLV. Primary drivers of baseline recurring revenue.At-Risk High Value: High churn risk, above-median CLV. Priority focus for immediate personalized re-engagement and win-back offers.Steady Low-Spenders: Low churn risk, below-median CLV. Consistent low-touch purchasers suitable for upsell/cross-sell campaigns.Hibernating / Lost: High churn risk, below-median CLV. Disengaged accounts deprioritized from paid retention campaigns.
Tech StackLanguage:
Python 3.10+Core Libraries:pandas & numpy: Data sanitation, vector operations, and transformationslifetimes: Probabilistic modeling (BG/NBD and Gamma-Gamma)scikit-learn: Feature scaling (StandardScaler) and clustering (KMeans)Visualization & BI: Microsoft Power BI Desktop
