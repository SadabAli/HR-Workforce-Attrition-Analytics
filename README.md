# HR Analytics & Workforce Insights Dashboard

## Project Overview

**HR Analytics & Workforce Insights Dashboard** is a Power BI-based HR reporting project focused on workforce composition, employee attrition, termination analysis, salary, demographics, and employee-level reporting.

The project uses **Power Query** for data transformation, **DAX** for HR metrics, a dedicated **Date Table** for time-based analysis, and Power BI interactive features such as slicers, page navigation, and drill-through.

## Dashboard Previwe

### Overview(Page-01)
<img width="1332" height="745" alt="Screenshot 2026-09-02 151149" src="https://github.com/user-attachments/assets/a5002376-4b27-4970-b394-4586aacabee1" />


### Attrition(Page-02)
<img width="1329" height="744" alt="Screenshot 2026-09-02 151202" src="https://github.com/user-attachments/assets/ca4d6b7d-ec57-43e3-9e21-7a4e9164377e" />



### Employee Details(Page-03)
<img width="1329" height="743" alt="Screenshot 2026-09-02 151218" src="https://github.com/user-attachments/assets/aae5d5c7-8da6-4629-b679-00484b382cd2" />


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

## Page 1 - Overview

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

# Page 2 - Attrition

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

# Page 3 - Employee Details

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
  - **Another position - 20**
  - **Unhappy - 14**
  - **More money - 11**

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
