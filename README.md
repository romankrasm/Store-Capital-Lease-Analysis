# STORE Capital — Net Lease Portfolio Analysis

## Project Overview
This project simulates a real-world data analyst workflow for a net lease 
REIT, modeled after STORE Capital's portfolio management approach. It 
demonstrates SQL querying in BigQuery, REIT-specific financial metrics, 
and Excel-based portfolio analysis.

## Business Question
*Who are our top 10 tenants by Annual Base Rent, what is our weighted 
average cap rate per tenant, and how many leases are expiring within 
3 years?*

## Tools Used
- Google BigQuery (SQL)
- Microsoft Excel
- Google Cloud Platform

## Dataset
Two tables created to simulate a net lease REIT portfolio:
- `leases.csv` — 50 property-level records with rent, cap rate, 
   acquisition cost, and lease expiry dates
- `tenants.csv` — 15 tenant records with industry, credit rating, 
   and revenue data

## SQL Query
The core query joins both tables to calculate:
- Total Annual Base Rent (ABR) per tenant
- Weighted Average Cap Rate (weighted by acquisition cost, not a 
  simple average)
- Count of leases expiring within 3 years

```sql
SELECT
  l.tenant_name,
  t.industry,
  t.credit_rating,
  ROUND(SUM(l.annual_base_rent), 0) AS Total_ABR,
  SUM(l.cap_rate * l.acquisition_cost) / SUM(l.acquisition_cost) AS WACR,
  COUNT(CASE WHEN l.lease_expiry_date <= DATE '2028-04-27' 
        THEN 1 END) AS Expiring_in_3yr,
  COUNT(*) AS Total_Locations
FROM `store-interview-prep.101.Leases` l
JOIN `store-interview-prep.101.Tenants` t ON l.tenant_id = t.tenant_id
GROUP BY l.tenant_name, t.industry, t.credit_rating
ORDER BY Total_ABR DESC
LIMIT 10
```

## Key Findings
1. **Portfolio Concentration Risk:** The top 3 tenants represent 39.4% 
   of total ABR — a significant concentration for a net lease portfolio 
   where best practice targets no single tenant above ~3%.

2. **Lease Expiry Risk:** 50% of all near-term expiring leases belong 
   to the top 3 tenants by ABR, representing meaningful renewal risk 
   that requires active asset management attention.

3. **Cap Rate Pricing Anomaly:** The three weakest tenants by credit 
   rating (BB- and BB) are not commanding the highest cap rates in the 
   portfolio. Suggesting the portfolio may not be adequately compensating 
   for credit risk on its weakest relationships.

## Skills Demonstrated
- Multi-table SQL joins in BigQuery
- Weighted average calculations in SQL
- Conditional aggregation with CASE WHEN
- REIT financial metrics: ABR, WACR, lease maturity analysis
- Excel portfolio analysis with INDEX-MATCH, VLOOKUP, conditional 
  formatting
- Business interpretation of quantitative findings
