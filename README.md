Large-Scale Medicare Reimbursement Analysis

Does Medicare pay significantly different amounts depending on the drug, the medical service, or the state where care is delivered?

This project analyzes six public CMS Medicare datasets to find out what actually drives Medicare reimbursement. I built a SQL/DuckDB data pipeline, feature-engineered state-level payment tables, and applied linear regression and K-means clustering to separate the factors that matter from the ones that don't.


Key Findings
- Service type drives reimbursement, not location. Medical service type (HCPCS code) alone explains 96.0% of the variation in Medicare payment per service in 2022 and 78.6% in 2023. Adding provider state and service volume adds only about 1 percentage point.
- Reimbursement splits into clear tiers. K-means clustering found 2 payment clusters in 2022 and 3 in 2023 (chosen by silhouette score over k = 2–20):
  - Lowest-paid services are routine diagnostics and labs, such as urinalysis, standard blood draws, EKG interpretation, and abdominal X-rays.
  - Highest-paid services are complex, physician-managed care, such as critical care, emergency department visits, and initial hospital inpatient care.
- Part B drugs generally show higher Medicare spending than Part D drugs, consistent with Part B drugs being administered in clinical settings under medical supervision.
- Drug waste plays a small role. Discarded Part B drug units show a slight positive relationship with Medicare payment, but waste alone does not explain the variation in spending. Drug price and utilization also matter.


Dataset Source: data.CMS.gov

Methodology
1. Data Pipeline
- Queried and filtered the six CMS datasets with SQL and DuckDB
- Converted outputs to Parquet for efficient downstream processing

2. Feature Engineering

Built two new tables, Medicare Weighted Average Payment by State (2022 and 2023), covering the 50 most common medical services in each state. Variables and their descriptions are below: 
  - HCPCS_cd: Code identifying the medical service
  - Rndrng_Prvdr_State_Abrvtn: State where the service was provided
  - est_wght_avg_Mdcr_paid: Weighted average Medicare payment per service in a state, weighting provider-level payments by service volume

3. Linear Regression
   Three models were fit for each year to isolate which factors explain payment differences:
   - Model 1: Service Type + State + Service Volume
       - R Squared (2022): 0.972
       - R Squared (2023): 0.800
    - Model 2: Service Type + State
        - R Squared (2022): 0.972
        - R Squared (2023): 0.799
    - Model 3: Service Type Only
        - R Squared (2022): 0.960
        - R Squared (2023): 0.786

4. K-Means Clustering
- Computed the mean weighted Medicare payment for each HCPCS service
- Tested k = 2 to 20 and selected k using silhouette scores
- Visualized the clusters with boxplots and ranked the highest- and lowest-reimbursed services in each cluster

5. Part B vs. Part D and Drug Waste
- Compared spending per dosage unit and per claim between Part B and Part D drugs
- Plotted the share of discarded drug units against Medicare payment for those units to measure the effect of drug waste


Tools: 
- Python
- SQL
- DuckDB
- Parquet
- pandas
- scikit-learn
- seaborn / matplotlib    
