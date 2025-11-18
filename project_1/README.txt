# Introduction
📊 Dive into the data job market! Focusing on data analyst roles, this project explores 💰 top-paying jobs, 🔥 in-demand skills, and 📈 where high demand meets high salary in data analytics.


# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was created to identify top-paid and in-demand skills, helping others streamline their job search and career development.

The dataset contains job titles, salaries, locations, and essential skills related to data analytics positions.

### The questions I wanted to answer through my SQL queries were:

1. What are the top-paying data analyst jobs?  
2. What skills are required for these top-paying jobs?  
3. What skills are most in demand for data analysts?  
4. Which skills are associated with higher salaries?  
5. What are the most optimal skills to learn?

# Tools I Used
For this analysis of the data analyst job market, I used the following tools:

- **SQL:** The backbone of my analysis, enabling me to query and extract key insights from the database.  
- **PostgreSQL:** The database management system used to handle and store job posting data.  
- **Visual Studio Code:** Used for executing SQL queries and managing scripts.  
- **Git & GitHub:** For version control, project organization, and sharing code.

# The Analysis
Each query in this project focused on different aspects of the data analyst job market.  

---

### 1. Top Paying Data Analyst Jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs.

```sql
SELECT	
	job_id,
	job_title,
	job_location,
	job_schedule_type,
	salary_year_avg,
	job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND 
    job_location = 'Anywhere' AND 
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;
````

**Insights:**

* **Wide Salary Range:** Top 10 paying data analyst roles span from $184,000 to $650,000.
* **Diverse Employers:** Companies across multiple industries offer high salaries for analyst positions.
* **Varied Job Titles:** Ranging from Data Analyst to Director of Analytics, showing diversity in specialization.


---

### 2. Skills for Top Paying Jobs

To understand what skills are required for the top-paying jobs, I joined job postings with the skills data.

```sql
WITH top_paying_jobs AS (
    SELECT	
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND 
        job_location = 'Anywhere' AND 
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```

**Findings:**

* **SQL** appears most frequently (8 out of 10).
* **Python** and **Tableau** follow closely.
* Other valuable skills include **R**, **Snowflake**, and **Excel**.


---

### 3. In-Demand Skills for Data Analysts

This query identified the most frequently requested skills in job postings.

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;
```

| Skills   | Demand Count |
| -------- | -----------: |
| SQL      |         7291 |
| Excel    |         4611 |
| Python   |         4330 |
| Tableau  |         3745 |
| Power BI |         2609 |

**Key Takeaway:** SQL and Excel are foundational, while Python, Tableau, and Power BI reflect the increasing technical depth expected of analysts.

---

### 4. Skills Based on Salary

This query analyzed which skills are linked to the highest average salaries.

```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25;
```

| Skills    | Average Salary ($) |
| --------- | -----------------: |
| pyspark   |            208,172 |
| bitbucket |            189,155 |
| couchbase |            160,515 |
| watson    |            160,515 |
| datarobot |            155,486 |

**Observation:** High salaries are associated with big data, cloud, and machine learning tools, such as PySpark, Databricks, and Airflow.

---

### 5. Most Optimal Skills to Learn

This combined demand and salary data to highlight skills that offer strong market value.

```sql
SELECT 
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True 
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```

| Skills     | Demand Count | Average Salary ($) |
| ---------- | -----------: | -----------------: |
| go         |           27 |            115,320 |
| confluence |           11 |            114,210 |
| hadoop     |           22 |            113,193 |
| snowflake  |           37 |            112,948 |
| azure      |           34 |            111,225 |

**Conclusion:** Programming (Python, R), cloud platforms (Snowflake, AWS, Azure), and visualization tools (Tableau, Looker) stand out as the best investment areas for aspiring analysts.

---

# What I Learned

Through this project, I developed a stronger understanding of SQL and analytical techniques, including:

* **Complex Query Building** – mastering joins, CTEs, and subqueries.
* **Aggregation** – effectively summarizing data with functions like COUNT() and AVG().
* **Analytical Thinking** – translating real-world questions into data-driven insights.

---

# Conclusions

### Key Insights

1. **Top-Paying Jobs:** Remote data analyst jobs offer salaries up to $650,000.
2. **Most Critical Skills:** SQL remains essential for both pay and demand.
3. **High-Value Skills:** Specialized tools (e.g., Snowflake, Solidity) command premium salaries.
4. **Optimal Skills to Learn:** SQL, Python, Tableau, and cloud technologies consistently offer strong returns in salary and demand.

### Final Thoughts

This project provided valuable experience in using SQL for market-oriented data analysis. The results can guide skill development for aspiring data analysts and highlight areas of focus to enhance employability and earning potential.

```


