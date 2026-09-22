# <p align="center">Hospital Readmission Risk Analysis</p>

## Business Problem: 
Hospitals face financial penalties when patients are readmitted within 30 days of leaving the hospital. This project looks at 10 years of data from 130 U.S. hospitals to find which patients, diagnoses, and care patterns are linked to a higher risk of readmission. The goal is to help care teams identify high-risk patients, focus follow-up resources, and prevent avoidable readmissions.


## Data & Tools:
- **Dataset**: Diabetes 130-US Hospitals for Years 1999-2008 (UCI Machine Learning Repository, CC BY 4.0). ~100,000 patient encounters across 130 hospitals, 1999-2008
- **Database**: MySQL Workbench
- **Techniques Used**: CTEs, Window Functions (RANK, NTILE), CASE-based risk tiering, correlated subqueries, and multi-table joins

## Approach/Methodology
- **Cleaning**: Replaced placeholder missing values with NULLS, removed a column with about 97% missing data, kept only one encounter per patient to avoid duplicate bias, and removed patients who were inactive or were discharged to hospice
- **Exploratory analysis**: Established baseline readmission rates by age, admission type, and diagnosis category.
- **Advanced analysis**: Built a reusable high-risk patient group with a CTE, applied window function to rank patients by risk, grouped them by medication use and number of diagnoses, and found diagnosis categories with above average readmission rates.
- **Findings**: Summarized the results into four key findings that connect back to the main business problem.

## Key SQL Techniques Used
- CTEs to stage a clean, reusable risk cohort across multiple queries
- Window functions: RANK() OVER (PARTITION BY) to rank medication burden within age groups, NTILE(4) to build risk quartiles
- CASE-based tiering to convert continuous variables into business-readable risk categories
- Correlated subqueries with HAVING to isolate diagnosis categories performing above the population-wide average
- Multi-table joins against ID-mapping reference tables to convert numeric codes into readable labels

## Findings
1. Among the six diagnosis categories analyzed, injury had the highest 30-day readmission rate at 11.0% (4,713 encounters), followed by circulatory conditions at 9.7% (21,365 encounters) and diabetes at 9.2% (5,756 encounters). Notably, while injury has the highest rate, circulatory's much larger patient volume — roughly 4.5x that of the other two categories — means it likely accounts for the greatest total number of readmissions, even at a slightly lower rate. This distinction matters for prioritization: rate-based targeting points to Injury, while volume-based (total impact) targeting points to Circulatory.
2. Patients with **more prior inpatient visits** in the prior year showed a substantially elevated readmission risk, supporting prior-utilization as a strong predictive signal. Patients who had a medication change at discharge showed a **higher** readmission rate (9.4%, 31,159 encounters) than those with no change (8.6%, 38,039 encounters). This 0.8 percentage point gap, suggests that a discharge medication change — while often clinically necessary — may signal a more complex or unstable case, and/or introduce an adjustment period (new dosing, side effects, patient confusion) that carries its own readmission risk.
3. Discharge disposition showed the widest variation in readmission rate of any factor examined. Patients discharged to rehab facilities had a readmission rate of 27.7% (1,418 encounters) — four times higher than patients discharged home (6.9%, 43,986 encounters, the largest group by far). Other transfer types also carried elevated risk: transfers to other inpatient care institutions (20.7%), skilled nursing facilities (13.5%, 8,744 encounters), short-term hospitals (12.0%), and intermediate care facilities (10.4%) all outpaced home discharge. This pattern suggests that discharge destination is not just a proxy for how sick a patient is — care transitions themselves may be a point of breakdown that contributes to readmission risk.
4. Findings around **medication changes at discharge and A1C testing** during the stay both align with the original clinical research question this dataset was collected to investigate. Patients who were not tested for A1C during their hospital stay came back within 30 days more often (9.1%, 56,406 patients) than patients who were tested — regardless of what the test showed. Even patients who tested in the worst range (>8, indicating poorly controlled diabetes) had a lower readmission rate (8.2%) than patients who weren't tested at all.

## Recommendations
- Set up an automatic follow-up when a patient's medication changes at discharge. Patients whose medications changed had a higher readmission rate than those whose didn't (9.4% vs. 8.6%). Whenever a discharge includes a medication change, automatically schedule a pharmacist check-in and a follow-up call within 2-3 days.
- Give extra care coordination to patients with circulatory conditions, diabetes, and injuries since all three show above average readmission rates. Coordination should include dedicated case management, medication reconciliation, and structured follow-up calls after discharge.
- Review discharge processes at facilities with high readmission rates to find areas that could be improved. Potentially combine all risk factors into one discharge risk checklist. This ties everything else together (prior utilization, diagnosis, medication changes, discharge disposition, and A1C status) into a single tool care teams can actually use at the bedside, rather than five disconnected findings that require separate review.
