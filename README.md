# Healthcare Financial and Service Analysis

A Power BI project analysing healthcare billing, treatment costs, insurance coverage, and service-level financial patterns.

## Project Overview

This project presents an interactive dashboard for exploring healthcare financial performance across departments, diagnoses, procedures, service types, and locations.

The report combines visit-level financial data with supporting lookup tables to examine billing patterns and patient out-of-pocket amounts.

The Power BI template contains two report pages. The screenshot below documents the visible financial overview dashboard.

## Dashboard Preview

![Healthcare Analysis Dashboard](screenshort%201.jpeg)

## Business Objectives

- Monitor treatment, medication, and room charges.
- Compare total billing with insurance coverage.
- Examine calculated patient out-of-pocket amounts.
- Compare billing contributions across departments and procedures.
- Explore service-type billing composition within diagnoses.
- Support financial reporting and further operational analysis.

## Tools and Technologies

| Tool | Application |
|---|---|
| Power BI Desktop | Data modelling and interactive reporting |
| Power Query | CSV import, data type conversion, and date table creation |
| DAX | Financial measures and calculated columns |
| CSV Files | Source data for visits and supporting tables |

## Data Model

| Table | Description |
|---|---|
| `Fact_visits` | Visit records, costs, coverage, service details, and admission dates |
| `patients Table` | Patient demographics and city identifiers |
| `providers Table` | Healthcare provider details |
| `department Table` | Department identifiers and names |
| `diagnose Table` | Diagnosis identifiers and descriptions |
| `procedures Table` | Procedure identifiers and names |
| `insurance Table` | Insurance provider details |
| `cities Table` | City and state information |
| `Date Table` | Calendar attributes derived from visit dates |

The visit table includes patient and provider identifiers, department, diagnosis, procedure, insurance, service type, costs, payment status, satisfaction score, and admission/discharge dates.

The source dataset's provenance and reporting period are not documented in the repository.

## Financial Overview

| Metric | Displayed Total |
|---|---:|
| Medication Cost | $546.0K |
| Treatment Cost | $2.6M |
| Insurance Coverage | $2.2M |
| Out-of-Pocket Amount | $1.1M |
| Room Charge | $179.6K |
| Billing Amount | $3.4M |

> Values are rounded figures from the dashboard screenshot. They have not been independently recalculated from the source CSV files.

### Metric Definitions

- **Billing Amount:** Medication cost + treatment cost + room charge.
- **Room Charge:** Length of stay × daily room rate.
- **Out-of-Pocket Amount:** Billing amount − insurance coverage.

Billing amount represents a calculated charge. It does not establish collected revenue or profit.

## Key Findings

### 1. Billing by Department

| Department | Displayed Billing Amount |
|---|---:|
| Cardiology | $0.85M |
| Orthopedics | $0.81M |
| General Surgery | $0.78M |
| Neurology | $0.48M |
| Pediatrics | $0.43M |

Cardiology has the highest displayed billing total, followed by Orthopedics and General Surgery.

These differences may reflect visit volume, service mix, or charge levels. They do not independently establish department profitability or efficiency.

### 2. Billing by Procedure

| Procedure | Displayed Billing Amount | Billing Share |
|---|---:|---:|
| X-Ray | $1.05M | 31.39% |
| CT Scan | $0.81M | 24.00% |
| MRI Scan | $0.60M | 17.90% |
| Ultrasound | $0.48M | 14.34% |
| Blood Test | $0.41M | 12.36% |

X-Ray-associated records contribute the largest share of billing.

The chart groups total visit billing by procedure; it should not be interpreted as standalone procedure fees.

### 3. Service-Type Composition by Diagnosis

The displayed chart presents billing composition within each diagnosis:

| Diagnosis | Emergency | Inpatient | Outpatient |
|---|---:|---:|---:|
| Hypertension | 22.26% | 23.83% | 53.92% |
| Appendicitis | 21.66% | 22.21% | 56.13% |
| Asthma | 29.71% | 29.62% | 40.66% |
| Fracture | 29.62% | 28.54% | 41.84% |
| Migraine | 27.51% | 27.17% | 45.32% |

Outpatient services have the largest displayed billing share within each listed diagnosis.

These percentages describe billing composition, not patient counts or clinical outcomes. Rounded percentages may not sum to exactly 100%.

### 4. Insurance and Patient Financial Exposure

The dashboard displays approximately **$2.2M in insurance coverage** and **$1.1M in calculated out-of-pocket amounts**.

The underlying calculation should be validated against coverage rules, adjustments, and payment records before being used for financial reconciliation.

## Core DAX Measures

The following measures are included in the template:

```dax
Total Treatment Cost =
SUM(Fact_visits[Treatment Cost])

Total Medication Cost =
SUM(Fact_visits[Medication Cost])

Total Insurance Coverage =
SUM(Fact_visits[Insurance Coverage])

Total Room Charge =
SUM(Fact_visits[Room Charge])

Total  Billing Amount =
[Total Medication Cost] +
[Total Treatment Cost] +
[Total Room Charge]

Out Of Pocket =
[Total  Billing Amount] - [Total Insurance Coverage]
```

### Average Measures

```dax
Avg Medication Cost =
AVERAGE(Fact_visits[Medication Cost])

Avg Treatment Cost =
AVERAGE(Fact_visits[Treatment Cost])

Avg Insurance Coverage =
AVERAGE(Fact_visits[Insurance Coverage])

Avg Room Charge =
AVERAGE(Fact_visits[Room Charge])

Avg Billing Amount =
AVERAGE(Fact_visits[Billing Amount])

Avg Out of Pocket =
AVERAGE(Fact_visits[Out Of Pocket])
```

`AVERAGE()` excludes blank values. Consequently, different measures may use different record counts.

### Procedure Billing Share

```dax
%Procedure =
DIVIDE(
    [Total  Billing Amount],
    CALCULATE(
        [Total  Billing Amount],
        ALL('procedures Table'[Procedure])
    )
)
```

### Department Billing Share

```dax
% Department =
DIVIDE(
    [Total  Billing Amount],
    CALCULATE(
        [Total  Billing Amount],
        ALL('department Table'[Department])
    )
)
```

## Calculated Columns

### Length of Stay

```dax
Length of stay =
DATEDIFF(
    Fact_visits[Admitted Date],
    Fact_visits[Discharge Date],
    DAY
)
```

### Room Charge

```dax
Room Charge =
Fact_visits[Length of stay] *
Fact_visits[Room Charges(daily rate)]
```

### Out-of-Pocket Amount

```dax
Out Of Pocket =
Fact_visits[Billing Amount] -
Fact_visits[Insurance Coverage]
```

Length-of-stay calculations require valid admission and discharge dates. Same-day stays return zero days under the current formula.

## Analysis Workflow

1. Import visit and lookup CSV files through Power Query.
2. Promote headers and assign field data types.
3. Generate a date table from the visit-date range.
4. Calculate length of stay, room charges, and billing-related fields.
5. Create financial totals, averages, and contribution measures.
6. Build department, procedure, diagnosis, and location visuals.
7. Add filters for department, diagnosis, procedure, year, and month.

## Business Recommendations

- Examine visit volume and service mix alongside department billing.
- Review the drivers of higher billing in X-Ray-associated records.
- Validate insurance coverage and out-of-pocket calculations.
- Compare inpatient room charges with complete admission records.
- Extend reporting to payment status and collected amounts.
- Add visit counts and comparable per-visit metrics before evaluating operational performance.

These recommendations are proposed analytical actions. Their operational or financial impact has not been measured.

## Repository Contents

| File | Description |
|---|---|
| `Healthcare Analysis project .pbit` | Power BI report template |
| `screenshort 1.jpeg` | Healthcare financial dashboard screenshot |
| `README.md` | Project documentation |

## How to Open the Project

1. Download or clone this repository.
2. Open `Healthcare Analysis project .pbit` in Power BI Desktop.
3. Obtain the compatible source CSV files:
   - `cities.csv`
   - `department.csv`
   - `diagnose.csv`
   - `insurance.csv`
   - `patients.csv`
   - `procedures.csv`
   - `providers.csv`
   - `visits.csv`
4. Update the folder paths and file-selection steps in Power Query.
5. Apply changes and refresh the report.

> The template references a folder on the author's local computer. Source CSV files are not included in this repository.

## Limitations and Future Improvements

- Document the dataset source, reporting period, and visit-level definition.
- Parameterise source folder paths.
- Validate relationships and lookup-key uniqueness.
- Check missing or invalid admission and discharge dates.
- Confirm the billing treatment of same-day stays.
- Validate insurance coverage against calculated billing.
- Clarify the denominator of each average metric.
- Rename the diagnosis chart to “Billing Share by Service Type within Diagnosis.”
- Remove technical labels such as “Blank Measure” from visible chart titles.
- Verify geographic categories and map locations.
- Add a screenshot documenting the second report page.
- Distinguish billed amounts, payments received, and outstanding balances.
- Include visit counts, length-of-stay trends, and satisfaction analysis.

## Author

**Junaed Bogdadi**

[GitHub Profile](https://github.com/junaed-bogdadi)
