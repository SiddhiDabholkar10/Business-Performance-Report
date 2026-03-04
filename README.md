#  Business Performance Dashboard

An interactive **Power BI dashboard** analyzing employee performance, project budgets, and departmental cost distribution across business units.

---

##  Dashboard Preview

The dashboard provides an end-to-end view of organizational performance including employee profiles, project budgets, revenue tracking, and 2-year budget planning across departments.

---

##  Project Structure

```
Business Performance Dashboard/
│
├── Business Performance Power BI Report.pbix
│
└── Data Analysis Project Files/
    ├── employees.csv
    ├── departments.csv
    ├── projects.csv
    ├── completed_projects.csv
    ├── upcoming projects.csv
    ├── project_assignments.csv
    └── Head_Shots.csv
```

---

##  Features

- **KPI Cards** : Revenue (Capital), Total Budget, Project Cost, Salary Cost at a glance
- **Donut Charts** : Project cost distribution by department and by project status (completed vs upcoming)
- **Bar Charts** : Project budget distribution by project name and by department
- **Data Table** : Department goals with total budget, revenue, project cost, salary cost, and 2Y budget
- **Employee Panel** : Dynamic employee profile card with photo, name, department, current project, and salary
- **Slicers** : Filter by `project_status` and `Department_Name` with "All" option

---

##  Data Sources

| File | Description |
|---|---|
| `employees.csv` | Employee ID, name, department, salary, photo reference |
| `departments.csv` | Department names and goals |
| `projects.csv` | Project names, budgets, and assignments |
| `completed_projects.csv` | Historical completed project data |
| `upcoming projects.csv` | Pipeline project data |
| `project_assignments.csv` | Employee-to-project mapping |
| `Head_Shots.csv` | Employee headshot image URLs |

---

##  Tools & Technologies

- **SQL** - Data extraction, joins, aggregations, and filtering pre-import
- **Power BI Desktop** - Dashboard development
- **Power Query (M Language)** - Data transformation and cleaning
- **DAX** - Calculated columns and measures
- **CSV** - Raw data source files
- **Git & GitHub** - Version control

---

##  SQL Pre-Processing

Before importing data into Power BI, SQL was used to prepare and shape the data.

###  JOINs — Combining Tables
```sql
-- Join employees with departments and project assignments
SELECT 
    e.employee_id,
    e.first_name,
    e.last_name,
    e.salary,
    d.department_name,
    d.department_goals,
    p.project_name,
    p.project_budget,
    p.project_status
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
LEFT JOIN project_assignments pa ON e.employee_id = pa.employee_id
LEFT JOIN projects p ON pa.project_id = p.project_id;
```

###  Aggregations - Summarizing Data
```sql
-- Total budget, project cost, and salary cost per department
SELECT 
    d.department_name,
    SUM(p.project_budget)   AS total_project_cost,
    SUM(e.salary)           AS total_salary_cost,
    COUNT(p.project_id)     AS total_projects
FROM departments d
INNER JOIN employees e ON d.department_id = e.department_id
LEFT JOIN project_assignments pa ON e.employee_id = pa.employee_id
LEFT JOIN projects p ON pa.project_id = p.project_id
GROUP BY d.department_name;
```

###  Filtering - Focused Data Extraction
```sql
-- Filter only active/upcoming projects with budget > 0
SELECT *
FROM projects
WHERE project_status IN ('completed', 'upcoming')
  AND project_budget > 0;

-- Filter employees by department for salary analysis
SELECT employee_id, first_name, last_name, salary
FROM employees
WHERE department_id IS NOT NULL
  AND salary > 0;
```

###  Temp Tables - Staging Intermediate Results
```sql
-- Create a temp table for department-level cost summary
CREATE TEMPORARY TABLE temp_dept_summary AS
SELECT 
    d.department_name,
    SUM(p.project_budget)  AS total_project_cost,
    SUM(e.salary)          AS total_salary_cost
FROM departments d
JOIN employees e ON d.department_id = e.department_id
LEFT JOIN project_assignments pa ON e.employee_id = pa.employee_id
LEFT JOIN projects p ON pa.project_id = p.project_id
GROUP BY d.department_name;

-- Use temp table in final query
SELECT * FROM temp_dept_summary
ORDER BY total_project_cost DESC;
```

---

##  Data Transformations (Power Query)

Key steps applied in Power Query:

- **Grouped Rows** - Aggregated project data by department
- **Added Custom Columns** - Computed budget and 2Y budget fields
- **Changed Types** - Enforced correct data types (Currency, Text, Number)
- **Merged Queries** - Joined employee, department, and project tables

---

## 📊 Key Metrics

| Metric | Value |
|---|---|
| Total Revenue (Capital) | $1.288M |
| Total Budget | $6.9M |
| Total 2Y Budget | $3.45M |
| Total Project Cost | $570K |
| Total Salary Cost | $796K |

---

##  How to Run

1. Clone this repository:
   ```bash
   git clone https://github.com/SiddhiDabholkar10/Business-Performance-Report.git
   ```
2. Open **Power BI Desktop** (free download from Microsoft)
3. Open the file: `Business Performance Power BI Report.pbix`
4. If data source paths break, go to **Transform Data → Data Source Settings** and re-point to the CSV files in the `Data Analysis Project Files/` folder
5. Click **Refresh** to reload data

---

##  Insights Highlighted

- **Sales** department has the highest project cost at **$150K (26.32%)**
- **Upcoming projects** account for **64.04%** of total project cost
- **Human Resources** - "Enhance employee engagement" goal shows a revenue loss of **($25,000)**
- **Product Launch** has the highest individual project budget at **~$80K**

---

