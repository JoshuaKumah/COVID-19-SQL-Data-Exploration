# COVID-19 SQL Data Exploration

## Project Overview

This project explores global COVID-19 data using **SQL Server** to analyze infection rates, deaths, population impact, and vaccination progress across countries and continents.

The project demonstrates my ability to use SQL to explore real-world datasets, combine multiple tables, calculate analytical metrics, and prepare data for further reporting and visualization.

## Objectives

The analysis explores:

* COVID-19 cases compared with reported deaths
* Infection rates relative to population
* Countries with the highest infection rates
* Countries and continents with the highest death counts
* Global COVID-19 cases and deaths
* Vaccination progress relative to population

## Dataset

The project uses COVID-19 deaths and vaccination data, including population, cases, deaths, and vaccination figures.

The `CovidDeaths` and `CovidVaccinations` tables are joined using **location and date**.

**Data Source:** Our World in Data COVID-19 dataset.

## SQL Skills Demonstrated

* Joins
* Common Table Expressions (CTEs)
* Temporary Tables
* Window Functions
* `PARTITION BY`
* Aggregate Functions (`SUM`, `MAX`)
* `GROUP BY`
* Data Type Conversion (`CAST`, `CONVERT`)
* Calculated Fields
* Creating Views

## Key Analysis

### Infection Rate

Calculated the percentage of each country's population represented by confirmed COVID-19 cases:

```sql
(total_cases / population) * 100
```

### Death Percentage

Compared reported COVID-19 deaths with confirmed cases:

```sql
(total_deaths / total_cases) * 100
```

### Global Analysis

Aggregated new cases and deaths to examine the overall global impact of COVID-19.

### Vaccination Progress

Joined deaths/population data with vaccination data and used a window function to calculate cumulative vaccinations by location:

```sql
SUM(CONVERT(int, vac.new_vaccinations))
OVER (
    PARTITION BY dea.Location
    ORDER BY dea.Location, dea.Date
)
```

A CTE and temporary table were then used to calculate cumulative vaccinations relative to population.

## View for Visualization

Created the `PercentPopulationVaccinated` view to store transformed vaccination data for future analysis and visualization in tools such as **Power BI**.

## Key Takeaway

This project strengthened my ability to use SQL for data exploration and analytical problem-solving, particularly in combining datasets, calculating KPIs, creating running totals, and preparing structured data for reporting.
