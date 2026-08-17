# COVID-19 SQL Data Exploration

## Project Overview

This project explores global COVID-19 data using **SQL Server** to analyze the spread and impact of the pandemic across countries and continents.

The analysis examines COVID-19 cases, deaths, infection rates, population exposure, and vaccination progress. It also demonstrates the use of intermediate and advanced SQL techniques including **joins, Common Table Expressions (CTEs), temporary tables, window functions, aggregate functions, data type conversion, and views**.

The project was developed as part of my practical SQL learning journey and demonstrates my ability to use SQL to transform raw data into structured analytical insights.

---

## Business / Analytical Questions

The analysis was designed to answer questions including:

* What percentage of confirmed COVID-19 cases resulted in reported deaths?
* What percentage of each country's population was infected?
* Which countries recorded the highest infection rates relative to population?
* Which countries recorded the highest reported death counts?
* Which continents recorded the highest death counts?
* What was the overall global relationship between reported cases and deaths?
* How did vaccinations accumulate over time within individual countries?
* What percentage of a country's population was represented by cumulative vaccinations?

---

## Dataset

The analysis uses two primary tables:

### CovidDeaths

Contains information including:

* Location
* Continent
* Date
* Population
* Total cases
* New cases
* Total deaths
* New deaths

### CovidVaccinations

Contains vaccination-related information including:

* Location
* Date
* New vaccinations

The two datasets are joined using **location and date** to analyze vaccination progress alongside population data.

**Data Source:** Our World in Data COVID-19 dataset.

---

# SQL Skills Demonstrated

## 1. Data Exploration

Initial queries were used to understand the structure and contents of the COVID-19 deaths dataset before conducting further analysis.

```sql
SELECT Location, date, total_cases, new_cases,
       total_deaths, population
FROM PortfolioProject..CovidDeaths
WHERE continent IS NOT NULL
ORDER BY Location, date;
```

This establishes the primary variables required for subsequent analysis.

---

## 2. Calculated Fields

SQL calculations were used to derive analytical metrics directly from the underlying data.

For example, the reported death percentage was calculated as:

```sql
(total_deaths / total_cases) * 100
```

Similarly, population infection exposure was calculated using:

```sql
(total_cases / population) * 100
```

These calculations make it possible to compare locations using relative measures rather than absolute numbers alone.

---

## 3. Aggregate Functions

Aggregate functions such as `SUM()` and `MAX()` were used to summarize COVID-19 data.

For example:

```sql
SELECT
    Location,
    Population,
    MAX(total_cases) AS HighestInfectionCount,
    MAX((total_cases / population)) * 100
        AS PercentPopulationInfected
FROM PortfolioProject..CovidDeaths
GROUP BY Location, Population
ORDER BY PercentPopulationInfected DESC;
```

This identifies locations with the highest reported infection rates relative to population.

---

## 4. Death Analysis

Maximum reported death counts were aggregated by location:

```sql
SELECT
    Location,
    MAX(CAST(total_deaths AS int)) AS TotalDeathCount
FROM PortfolioProject..CovidDeaths
WHERE continent IS NOT NULL
GROUP BY Location
ORDER BY TotalDeathCount DESC;
```

The same analytical approach was extended to continents to compare the geographical distribution of reported deaths.

---

## 5. Global Analysis

Global cases and deaths were aggregated using `SUM()`.

```sql
SELECT
    SUM(new_cases) AS total_cases,
    SUM(CAST(new_deaths AS int)) AS total_deaths,
    SUM(CAST(new_deaths AS int))
        / SUM(new_cases) * 100 AS DeathPercentage
FROM PortfolioProject..CovidDeaths
WHERE continent IS NOT NULL;
```

This provides a global-level view rather than focusing on individual countries.

---

## 6. SQL Joins

The `CovidDeaths` and `CovidVaccinations` tables were joined using location and date:

```sql
FROM PortfolioProject..CovidDeaths dea
JOIN PortfolioProject..CovidVaccinations vac
    ON dea.location = vac.location
    AND dea.date = vac.date
```

This combines population information with daily vaccination figures and enables vaccination progress to be analyzed relative to population.

---

## 7. Window Functions

A SQL window function was used to calculate cumulative vaccinations for each location:

```sql
SUM(CONVERT(int, vac.new_vaccinations))
OVER (
    PARTITION BY dea.Location
    ORDER BY dea.Location, dea.Date
) AS RollingPeopleVaccinated
```

`PARTITION BY` resets the cumulative calculation for each location, while `ORDER BY` allows vaccination values to accumulate chronologically.

This demonstrates how SQL can calculate running totals without collapsing the underlying daily observations.

---

## 8. Common Table Expressions (CTEs)

A CTE named `PopvsVac` was created to make the rolling vaccination calculation available for further analysis.

```sql
WITH PopvsVac
(
    Continent,
    Location,
    Date,
    Population,
    New_Vaccinations,
    RollingPeopleVaccinated
)
AS
(
    -- vaccination and population query
)

SELECT *,
       (RollingPeopleVaccinated / Population) * 100
FROM PopvsVac;
```

The CTE enables a second-level calculation to estimate cumulative vaccinations relative to population.

---

## 9. Temporary Tables

A temporary table called:

```sql
#PercentPopulationVaccinated
```

was created to store intermediate vaccination calculations.

This demonstrates another method for handling multi-stage analysis where calculated results need to be reused.

The temporary table stores:

* Continent
* Location
* Date
* Population
* New vaccinations
* Rolling vaccination count

The stored results can then be used to calculate vaccination levels relative to population.

---

## 10. Data Type Conversion

The dataset required explicit conversion of some fields before mathematical operations could be performed.

For example:

```sql
CAST(total_deaths AS int)
```

and:

```sql
CONVERT(int, vac.new_vaccinations)
```

This demonstrates handling data-type issues commonly encountered when working with real-world datasets.

---

## 11. Creating Views

A SQL view called:

```sql
PercentPopulationVaccinated
```

was created to store the vaccination analysis for later use.

The view provides reusable data containing:

* Continent
* Location
* Date
* Population
* New vaccinations
* Rolling vaccination figures

Creating a view makes the transformed dataset easier to access for future analysis and visualization in tools such as Power BI.

---

# Key SQL Concepts Used

| SQL Technique   | Application                                   |
| --------------- | --------------------------------------------- |
| `SELECT`        | Data retrieval                                |
| `WHERE`         | Filtering records                             |
| `GROUP BY`      | Aggregating geographical data                 |
| `ORDER BY`      | Ranking and chronological analysis            |
| `SUM()`         | Global totals and cumulative vaccinations     |
| `MAX()`         | Highest infection/death values                |
| `CAST()`        | Data type conversion                          |
| `CONVERT()`     | Preparing vaccination values for calculations |
| `JOIN`          | Combining deaths and vaccination datasets     |
| `PARTITION BY`  | Country-level window calculations             |
| `SUM() OVER()`  | Rolling vaccination totals                    |
| CTE             | Multi-stage vaccination analysis              |
| Temporary Table | Storing intermediate calculations             |
| View            | Preparing reusable analytical data            |

---

# Repository Structure

```text
COVID-19-SQL-Data-Exploration/
│
├── README.md
│
├── sql/
│   └── covid19_data_exploration.sql
│
├── data/
│   └── README.md
│
└── images/
    └── query-results/
```

---

# Key Takeaways

This project demonstrates my ability to use SQL not only for data retrieval but also for analytical problem-solving.

Through the project, I practiced:

* Exploring structured datasets
* Translating analytical questions into SQL queries
* Calculating business-style KPIs and percentages
* Aggregating data across countries and continents
* Combining datasets using joins
* Building cumulative metrics with window functions
* Structuring complex calculations with CTEs
* Working with temporary tables
* Handling data-type conversion
* Creating reusable views for downstream reporting and visualization

The same SQL concepts demonstrated here are applicable beyond public-health data to areas such as **financial reporting, business intelligence, customer analytics, operational reporting, and performance analysis**.

---

## Future Improvements

Future development of this project could include:

* Building a Power BI dashboard from the SQL outputs
* Creating additional time-series analysis
* Comparing vaccination progress across countries
* Developing regional dashboards
* Adding SQL-based data cleaning and validation
* Building reusable SQL views specifically for BI reporting

---

## Acknowledgement

This project was developed as part of my SQL learning journey using a guided COVID-19 data exploration project. The repository documents the SQL concepts and analytical techniques I practiced and demonstrates my understanding of their application.

---

## About Me

I am a finance and analytics professional with an MSc in Strategic Finance and Analytics and a background in accounting, financial reporting, and data analysis.

My technical toolkit includes **SQL, Power BI, Excel, R, financial modelling, and ERP systems**, with a particular interest in applying analytics to financial and business decision-making.
