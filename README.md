# HR Analytics & Workforce Insights Dashboard

## Project Overview

**HR Analytics & Workforce Insights Dashboard** is a Power BI-based HR reporting project focused on workforce composition, employee attrition, termination analysis, salary, demographics, and employee-level reporting.

The project uses **Power Query** for data transformation, **DAX** for HR metrics, a dedicated **Date Table** for time-based analysis, and Power BI interactive features such as slicers, page navigation, and drill-through.

## Target Domain

- HR Analytics
- People Analytics
- Workforce Analytics
- HR Reporting
- HR Business Intelligence

## Target Roles

- HR Data Analyst
- HR Analytics Analyst
- HR Reporting Analyst
- People Analytics Analyst
- Workforce Analytics Analyst
- Data Analyst - HR / People Analytics
- BI Analyst - HR

---

## Business Objective

The dashboard is designed to help HR stakeholders understand:

- Current workforce composition
- Active vs terminated employees
- Employee attrition patterns
- Termination reasons
- Demographic composition
- Employee-level information
- Changes in workforce metrics over time

The project is structured as a three-page HR reporting solution.

---

## Data Preparation

The project starts with the HR dataset and uses Power Query to retain the required fields:

```powerquery
= Table.SelectColumns(
    #"Changed Type",
    {
        "Employee_Name",
        "EmpID",
        "Salary",
        "Position",
        "State",
        "DOB",
        "Sex",
        "MaritalDesc",
        "CitizenDesc",
        "RaceDesc",
        "DateofHire",
        "DateofTermination",
        "TermReason",
        "EmploymentStatus",
        "Department",
        "ManagerName"
    }
)
```

The selected columns support workforce, compensation, demographic, employment-date, department, and termination analysis.

---

## Data Model

### Main tables

- `HRDataset_v14`
- `Date Table`
- `_measure`

### Date Table

```DAX
Date Table = CALENDAR(DATE(2006,01,01), DATE(2018,12,31))
```

Calculated columns:

```DAX
Month Name = FORMAT('Date Table'[Date], "mmm")
```

```DAX
Month Number = MONTH('Date Table'[Date])
```

```DAX
Year = YEAR('Date Table'[Date])
```

### Date Relationships

The model uses two date relationships between the Date Table and HR dataset:

```text
Date Table[Date] → HRDataset_v14[DateofHire]
```

This is the active relationship.

```text
Date Table[Date] → HRDataset_v14[DateofTermination]
```

This relationship is inactive and is activated inside the termination measure using `USERELATIONSHIP()`.

This setup allows the same Date Table to support both hire-date analysis and termination-date analysis.

---

## Calculated Columns

### Employee Age

```DAX
Employee Age =
DATEDIFF(
    HRDataset_v14[DOB],
    TODAY(),
    YEAR
)
```

### Employee Age Band

```DAX
Employee Age Band =
SWITCH(
    TRUE(),
    HRDataset_v14[Employee Age] >= 33 &&
        HRDataset_v14[Employee Age] < 40, "33-39",

    HRDataset_v14[Employee Age] >= 40 &&
        HRDataset_v14[Employee Age] < 50, "40-49",

    HRDataset_v14[Employee Age] >= 50 &&
        HRDataset_v14[Employee Age] < 60, "50-59",

    HRDataset_v14[Employee Age] >= 60 &&
        HRDataset_v14[Employee Age] <= 74, "60-74",

    "Other"
)
```

---

# DAX Measures

Measures are stored in the `_measure` table.

### Total Employees

```DAX
Total Employess =
DISTINCTCOUNT(HRDataset_v14[EmpID])
```

Counts unique employees using `EmpID`.

### Active Employees

```DAX
Active Employe =
CALCULATE(
    [Total Employess],
    FILTER(
        HRDataset_v14,
        HRDataset_v14[EmploymentStatus] = "Active"
    )
)
```

Counts employees whose employment status is Active.

### Terminated Employees

```DAX
Terminated Employee =
CALCULATE(
    [Total Employess],
    HRDataset_v14[EmploymentStatus] <> "Active",
    USERELATIONSHIP(
        'Date Table'[Date],
        HRDataset_v14[DateofTermination]
    )
)
```

Counts non-active employees while using the termination-date relationship for time-based termination analysis.

### Average Salary

```DAX
Avg Salary =
AVERAGE(HRDataset_v14[Salary])
```

Calculates average employee salary.

### Attrition Rate

```DAX
Attrition Rate =
DIVIDE(
    [Terminated Employee],
    [Active Employe]
)
```

The project uses this tutorial-defined attrition formula for the dashboard.

---

# Dashboard Structure

## Page 1 — Overview

The Overview page provides a high-level view of the workforce.

### KPIs

- Total Employees
- Average Salary
- Attrition Rate

### Visual analysis

- Total Employees by Year
- Average Salary by Year
- Attrition Rate by Year
- Total Employees by Sex
- Total Employees by Citizenship
- Total Employees by Marital Status
- Total Employees by Department
- Total Employees by Race
- Total Employees by Employee Age Band

### Interactivity

- Year slicer
- Page navigation buttons

### Business purpose

This page gives HR stakeholders a quick overview of workforce composition and headline HR metrics before moving to deeper analysis.

---

# Page 2 — Attrition

The Attrition page focuses specifically on employee exits.

### KPIs

- Terminated Employees
- Active Employees
- Attrition Rate

### Visual analysis

- Attrition Rate by Year
- Terminated Employees by Sex
- Terminated Employees by Termination Reason

### Interactivity

- Year filter
- Navigation buttons

### Business purpose

This page helps HR investigate employee turnover over time and understand the recorded reasons and demographic distribution of terminations.

---

# Page 3 — Employee Details

The Employee Details page provides employee-level reporting.

### Slicers

- Position
- Department
- Year

### Employee table

Includes employee-level information such as:

- EmpID
- Employee Name
- Sex
- Citizenship
- Department
- Date of Hire
- Employee Age Band
- Employment Status
- Marital Status
- Position
- Race
- Average Salary

### Drill-through / filtering

The page supports detailed investigation using filter context including:

- Citizenship
- Department
- Employee Age Band
- Marital Status
- Race
- Sex
- Termination Reason

### Business purpose

This page allows users to move from summary-level HR reporting to employee-level investigation.

---

# Key Project Findings

Current dashboard values include:

- **104 terminated employees**
- **207 active employees**
- **50.24% attrition rate using the project's defined formula**
- **83 of 104 terminations occurred in the Production department**
- Top recorded termination reasons include:
  - **Another position — 20**
  - **Unhappy — 14**
  - **More money — 11**

These figures are portfolio-project findings from the provided HR dataset and should be presented as analysis of the dataset rather than as benchmarks for a real company's HR population.

---

# Technical Skills Demonstrated

### Power BI
- Report development
- Interactive dashboards
- Slicers
- Drill-through
- Page navigation
- KPI reporting

### Power Query
- Column selection
- Data preparation
- Data transformation

### DAX
- `CALCULATE`
- `FILTER`
- `DIVIDE`
- `DISTINCTCOUNT`
- `AVERAGE`
- `USERELATIONSHIP`
- `SWITCH`
- `DATEDIFF`
- Date functions

### Data Modeling
- Date dimension
- Active/inactive relationships
- Separate measure table
- Time-based analysis

---

# Resume Description

## HR Analytics & Workforce Insights Dashboard

**Power BI, Power Query, DAX, Data Modeling**

- Identified **104 terminated employees and 207 active employees**, resulting in a **50.24% attrition rate**.
- Analyzed attrition trends by **year, gender, and termination reason** to identify workforce turnover patterns.
- Built a **3-page Power BI report** with workforce KPIs, demographic analysis, and employee-level drill-through reporting using DAX and date relationships.

---

# Interview Explanation — 60 Seconds

“I built an HR Analytics and Workforce Insights Dashboard in Power BI to analyze workforce composition and employee attrition. I first used Power Query to select and prepare the required HR fields. Then I created a dedicated Date Table and modeled both hire and termination dates, keeping the termination relationship inactive and activating it with USERELATIONSHIP when calculating terminated employees. I created DAX measures for total employees, active employees, terminated employees, average salary, and attrition rate. The final report has three pages: Overview, Attrition, and Employee Details. The analysis identified 104 terminated and 207 active employees, and showed that 83 of the 104 terminations were from the Production department. The report also breaks down termination reasons and employee demographics using interactive filters and drill-through.”

---

# Important Interview Notes

## Do not say

- “This dashboard proves the reasons employees leave.”
- “The dashboard proves Production causes attrition.”
- “The 50.24% rate is a real-world HR benchmark.”
- “The project uses Workday.”
- “I have Qlik Sense experience.”

## Prefer saying

- “The analysis showed a concentration of terminations in Production.”
- “The dashboard helps HR investigate termination patterns.”
- “The attrition calculation follows the definition used in the project.”
- “I used Power BI, Power Query, DAX, and data modeling.”
- “Workday and Qlik Sense are gaps in my current hands-on experience.”

---

# Project Limitations

1. The dataset is a portfolio/practice HR dataset rather than live organizational HR data.
2. The dashboard identifies patterns but does not establish causation.
3. The tutorial-defined attrition formula is `Terminated Employees / Active Employees`; this should not automatically be treated as a standard annual HR attrition formula.
4. A relationship to termination date is inactive and must be explicitly activated with `USERELATIONSHIP()` for termination analysis.
5. Employee Age uses `TODAY()`, so the calculated age changes as time passes.

---

# Suggested Future Enhancements

After the tutorial version is complete, the project can be extended with additional analysis supported by the dataset, such as:

- Attrition by department
- Attrition by recruitment source
- Attrition by employee satisfaction
- Attrition by engagement
- Attrition by performance score
- Absence analysis
- Salary analysis by department or position

These should only be added if they are actually implemented and validated.
