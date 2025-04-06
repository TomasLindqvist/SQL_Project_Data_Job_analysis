# Introduction
Diving inte data jobs in Sweden 2023, focusing on Data Analyst roles as part of course work/certification for Like Barousse SQL course. Looking at top paying jobs, in demand skills and connected salary.

SQL Queries used and worked on can be found in folder: [project_sql folder](/project_sql/)

# Background
Working on developing my SQL skills I've decided to also do some course work, to make sure my knowledge has less gaps. Course used can be found here: [SQL Course](https://www.lukebarousse.com/sql)

### Main questions being tackled in this course where:
1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn for a data analyst looking to maximize job market value?


### Data used:
A collection of jobs data from 2023. Built out in a set of four interconnected tables a "job postings list", and connected tables of "skills for jobs", and "company information" as well as an extra table for "skills information" connected to the "skills for jobs" table

# Tools Used
- SQL
- PostgreSQL
- VSC
- Git & GitHub

# The Analysis
ach query for this project aimed at investigating specific aspects of the data analyst job market in Sweden. Here’s how I approached each question:

## 1. Top Paying Data Analyst Jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    job_work_from_home,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE job_title_short = 'Data Analyst'
    AND job_location LIKE '%Sweden%'
    AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10
```

## 2. Skills for Top Paying Jobs
To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        job_work_from_home,
        salary_year_avg,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE job_title_short = 'Data Analyst'
        AND job_location LIKE '%Sweden%'
        AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    top_paying_jobs.salary_year_avg DESC
```


## 3. In-Demand Skills for Data Analysts
This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM
    job_postings_fact
    INNER JOIN
        skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN
        skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_postings_fact.job_title_short = 'Data Analyst'
    AND job_postings_fact.job_location LIKE '%Sweden%'
GROUP BY
  skills_dim.skills
ORDER BY
  demand_count DESC
LIMIT 5;
```

| Skill     | Demand Count |
|-----------|--------------|
| SQL       | 509          |
| Python    | 344          |
| Tableau   | 205          |
| Power BI  | 190          |
| R         | 169          |


## 4. Skills Based on Salary
Exploring the average salaries associated with different skills revealed which skills are the highest paying.

```sql
SELECT
  skills_dim.skills AS skill, 
  ROUND(AVG(job_postings_fact.salary_year_avg),0) AS avg_salary
FROM
  job_postings_fact
	INNER JOIN
	  skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
	INNER JOIN
	  skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
  job_postings_fact.job_title_short = 'Data Analyst'
  AND job_postings_fact.job_location LIKE '%Sweden%' 
  AND job_postings_fact.salary_year_avg IS NOT NULL 
  
GROUP BY
  skills_dim.skills 
ORDER BY
  avg_salary DESC; 
```

| Skill       | Avg Salary |
|-------------|------------|
| SharePoint  | 118140     |
| Word        | 118140     |
| Hadoop      | 118140     |
| VBA         | 118140     |
| PowerPoint  | 118140     |
| Excel       | 118140     |
| Power BI    | 114658     |
| R           | 111895     |
| Tableau     | 111189     |
| Databricks  | 111175     |
| Python      | 109938     |
| SQL         | 109640     |
| Azure       | 108413     |
| Qlik        | 105650     |
| Jira        | 105650     |
| GDPR        | 94800      |

## 5. Most Optimal Skills to Learn
Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.

```sql
SELECT
    skills_job_dim.skill_id,
    skills_dim.skills, 
    COUNT(skills_job_dim.job_id) as demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg),0) AS avg_salary
FROM
    job_postings_fact
    INNER JOIN
        skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN
        skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_postings_fact.job_title_short = 'Data Analyst'
    AND job_postings_fact.job_location LIKE '%Sweden%'
	AND job_postings_fact.salary_year_avg IS NOT NULL
GROUP BY
    skills_job_dim.skill_id, skills_dim.skills
--HAVING
--	COUNT(skills_job_dim.job_id) > 10
ORDER BY
    demand_count DESC,
    avg_salary DESC
LIMIT 25;
```

| Skill ID | Skills      | Demand Count | Avg Salary |
|----------|-------------|--------------|------------|
| 0        | SQL         | 6            | 109640     |
| 1        | Python      | 3            | 109938     |
| 183      | Power BI    | 2            | 114658     |
| 5        | R           | 2            | 111895     |
| 182      | Tableau     | 2            | 111189     |
| 74       | Azure       | 2            | 108413     |
| 105      | GDPR        | 2            | 94800      |
| 181      | Excel       | 1            | 118140     |
| 22       | VBA         | 1            | 118140     |
| 97       | Hadoop      | 1            | 118140     |
| 188      | Word        | 1            | 118140     |
| 195      | SharePoint  | 1            | 118140     |
| 196      | PowerPoint  | 1            | 118140     |
| 75       | Databricks  | 1            | 111175     |
| 187      | Qlik        | 1            | 105650     |
| 233      | Jira        | 1            | 105650     |

Each query has been part of the course to work on and do simple modifications, and is the culmination of the SQL content worked on in around 80 different problems to solve on different SQL functions. In combination with learning about the Data Analyst job market in Sweden based on the dataset.

# What I Learned In The Course
Working on this project I've improved my SQL knowledge in areas like:

## Key Learnings:

- **Complex Query Construction**: 
  Built sophisticated SQL queries using **`WITH`** clauses (CTEs) to structure temporary tables, and employed **`JOIN`** operators for combining multiple data sources. This included mastering **`UNION ALL`** to merge results and designing queries with multiple subqueries for deeper analysis.

- **Data Aggregation and Transformation**: 
  Applied **`GROUP BY`** and aggregate functions like **`COUNT()`**, **`AVG()`**, **`MAX()`**, and **`DISTINCT()`** to summarize and transform raw data into insightful metrics. This allowed for categorization (e.g., salary comparison), ranking, and filtering based on conditions like average salary or required skills.

- **Conditional Logic and Comparison**: 
  Used **`CASE`** statements for dynamic categorization based on comparative logic, such as salary ranges or skill sets. Also implemented logical filters to compare individual data points (e.g., job salaries) against group averages, enabling more nuanced reporting.

- **Analytical Problem-Solving**: 
  Transformed real-world business questions into actionable SQL queries by identifying key variables (e.g., job titles, company locations, salaries) and applying filtering, aggregation, and comparison techniques to deliver actionable insights, such as categorizing companies by job skill requirements or determining the highest-paying roles.

- **Working with Dates and Time**: 
  Leveraged date functions like **`EXTRACT(MONTH FROM date)`** to perform time-based analysis, such as determining the month a job was posted or filtering based on specific time frames.

# Conclusions
This course and project has both given me insights into the Data Analyst job market in Sweden 2023, and helped me flesh out the basics of SQL and making sure I have less knowledge gaps.