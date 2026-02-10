# Introduction 
📊 This project explores the Data Analyst job market using real job posting data to answer 3 big questions:

1. Which Data Analyst roles pay the most?
→ Identify top-paying job titles and salary ranges.
2. What skills are employers asking for the most?
-> Find the most common tools/skills (e.g., SQL, Excel, Python, Power BI).
3. Which skills are both high-demand and high-paying?
→ Discover the “sweet spot” skills that maximize both salary potential and job opportunities.


🔍 SQL queries: [project_sql_foder](/project_sql/)

# Background
🎯 To navigate the Data Analyst job market more effectively, I built this project to identify top-paying roles and in-demand skills—helping streamline the job search and highlight what to learn to land better opportunities.

The dataset comes from my SQL course and includes key information such as: [SQL Course](https://drive.google.com/drive/folders/1moeWYoUtUklJO6NJdWo9OV8zWjRn0rjN)
- Job titles
- Salary
- Locations
- Essential skills

### ❓ Key Questions

Using SQL queries, I aimed to answer:
1. What are the top-paying Data Analyst jobs?
2. What skills are required for these top-paying jobs?
3. Which skills are most in demand for Data Analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn? 

# Tools I Used
For this project, I used a focused set of tools to query, manage, and share the analysis:
- **SQL:** Core language for querying the dataset and extracting insights.
- **PostgreSQL:** Database system used to store and manage job posting data.
- **Visual Studio Code:** Development environment for running SQL queries and organizing scripts.
- **Git & GitHub:** Version control and project sharing to track changes and publish the work.

# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here’s how I approached each question:

### 1. Top Paying Data Analyst Jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

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
LEFT JOIN company_dim 
    ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short = 'Data Analyst'
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
ORDER BY 
    salary_year_avg DESC 
LIMIT 10;
```


![Top paying Roles](/assets/Top%2010%20Paying%20Data%20Analyst%20Jobs%20(Remote:Anywhere).png)

**Here's the breakdown of the top data analyst jobs in 2023:**

- **Wide Salary Range:** The to.  -paying remote/“Anywhere” data analyst roles in this extract range from $184,000 to $255,829.5, showing a meaningful pay spread even within a small top list.
- **Diverse Employers:** High salaries come from multiple employers across different types of organizations, not just one company—indicating broad demand for analytics talent at higher pay levels.
- **Job Title Variety:** The titles vary widely—from Data Analyst roles to Principal/Director/Associate Director positions—suggesting compensation increases with seniority and scope (strategy, ownership, leadership)

### 2. Skills for Top Paying Jobs

To understand what skills are required for the top-paying jobs, i joined the job posting with the skills data, providing insights into what employers value for high-compensation roles


```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM 
        job_postings_fact
    LEFT JOIN company_dim 
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE 
        job_title_short = 'Data Analyst'
        AND job_location = 'Anywhere'
        AND salary_year_avg IS NOT NULL
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



![Top_skills_query](/assets/top_skills_query2.png)
**Here's the breakdown of the most demanded skills for the top 10 highest paying data analyst jobs in 2023:**

- Core Technical Stack: SQL + Python dominate across the top-paying roles, implying they’re baseline requirements for high-compensation remote DA tracks.
- Analytics & BI Layer: Tableau (and BI tools like Power BI) show up frequently, indicating strong demand for turning analysis into stakeholder-ready reporting/decision support.
- Data Handling & Platforms: Skills like Pandas, Excel, Snowflake appear repeatedly, suggesting value in both hands-on data manipulation and modern warehouse ecosystems.
- Team/Workflow Tools: Tools like Git/GitLab, Atlassian/Confluence/Bitbucket appear, signaling that high-paying roles often expect collaboration, documentation, and version-control maturity.



### 3. In-Demand Skill for Data Analyst

This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

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
Here's the breakdown of the most demanded skills for data analysts in 2023

- Most In-Demand Skills (Remote DA): SQL leads by a wide margin, followed by Excel and Python, with BI tools (Tableau, Power BI) consistently required—highlighting a core stack of querying + analysis + visualization for remote roles.


| Rank | Skill    | Demand Count |
|-----:|:---------|-------------:|
| 1    | SQL      | 7,291        |
| 2    | Excel    | 4,611        |
| 3    | Python   | 4,330        |
| 4    | Tableau  | 3,745        |
| 5    | Power BI | 2,609        |

table of demand for the top 5 skills in data analyst job postings

## 4 Skills Based on Salary

Exploring the average salaries associated with different skills revealed wich skills are the highest paying.

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
Here's a break down of the reults for top paying skills for Data Analysts:
- Premium skews toward data engineering / big data: Top skills like PySpark, Databricks, Airflow, Kubernetes, Linux suggest that higher-paying remote DA roles often expect big data handling + pipelines + platform knowledge, not just reporting.
- Programming-heavy stack shows up repeatedly: Skills such as pandas, numpy, scikit-learn, Jupyter indicate that higher-paying roles lean toward Python-based analytics workflows.
- Cloud & modern ecosystems are rewarded: GCP (and typically AWS/Azure in similar cuts) signals a pay premium for working within cloud data ecosystems.
- Workflow tools are likely proxies for mature orgs (not direct salary drivers): Bitbucket, GitLab, Jenkins, Atlassian, Notion often reflect enterprise-grade team practices (CI/CD, documentation, collaboration). These tools correlate with higher-paying environments but don’t necessarily “cause” higher pay.
- Specialized/vendor tools point to niche high-paying roles: Couchbase, Watson, DataRobot, Twilio, MicroStrategy suggest niche roles tied to specific stacks—often fewer postings but higher pay.

| Skill         | Avg Salary ($) |
|:--------------|-----------------:|
| PySpark       | 208,172          |
| Bitbucket     | 189,155          |
| Couchbase     | 160,515          |
| Watson        | 160,515          |
| DataRobot     | 155,486          |
| GitLab        | 154,500          |
| Swift         | 153,750          |
| Jupyter       | 152,777          |
| Pandas        | 151,821          |
| Elasticsearch | 145,000          |
| Golang        | 145,000          |
| NumPy         | 143,513          |
| Databricks    | 141,907          |
| Linux         | 136,508          |
| Kubernetes    | 132,500          |
| Atlassian     | 131,162          |
| Twilio        | 127,000          |
| Airflow       | 126,103          |
| scikit-learn  | 125,781          |
| Jenkins       | 125,436          |
| Notion        | 125,000          |
| Scala         | 124,903          |
| PostgreSQL    | 123,879          |
| GCP           | 122,500          |
| MicroStrategy | 121,619          |

Table of the average salary for the top 10 paying skills for data analysts

## 5. Most Optimal Skill to Learn

Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.

```sql
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst' 
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = True 
    GROUP BY
        skills_dim.skill_id
), 
average_salary AS (
    SELECT 
        skills_job_dim.skill_id,
        ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = True 
    GROUP BY
        skills_job_dim.skill_id
)
```

| Skill      | Demand Count | Avg Salary (USD) |
|:-----------|-------------:|-----------------:|
| Go         | 27           | 115,320          |
| Confluence | 11           | 114,210          |
| Hadoop     | 22           | 113,193          |
| Snowflake  | 37           | 112,948          |
| Azure      | 34           | 111,225          |
| BigQuery   | 13           | 109,654          |
| AWS        | 32           | 108,317          |
| Java       | 17           | 106,906          |
| SSIS       | 12           | 106,683          |
| Jira       | 20           | 104,918          |
| Oracle     | 37           | 104,534          |
| Looker     | 49           | 103,795          |
| NoSQL      | 13           | 101,414          |
| Python     | 236          | 101,397          |
| R          | 148          | 100,499          |
| Redshift   | 16           | 99,936           |
| Qlik       | 13           | 99,631           |
| Tableau    | 230          | 99,288           |
| SSRS       | 14           | 99,171           |
| Spark      | 13           | 99,077           |
| C++        | 11           | 98,958           |
| SAS        | 63           | 98,902           |
| SQL Server | 35           | 97,786           |
| JavaScript | 20           | 97,587           |

Table of the most optimal skills for data analyst sorted by salary

Here's a breakdown of the most optimal skill for Data Analysts in 2023:
- “Optimal” definition (your query): Skills that are not rare (demand_count > 10), remote-friendly, and show a higher average salary—so they balance market demand + pay premium.
- Tier 1: High-demand core (best ROI to learn first):
    - Python (236 demand | ~$101k)
    - Tableau (230 demand | ~$99k)
    - R (148 demand | ~$100k)
    
-> These are the most scalable “core analyst stack” in your output: strong demand + solid pay.

- Tier 2: Platform/warehouse premium (raises salary ceiling):
    - Snowflake (37 | ~$113k), Azure (34 | ~$111k), AWS (32 | ~$108k)
    - BigQuery (13 | ~$110k), Redshift (16 | ~$100k)

-> These signal roles where analysts operate closer to data infrastructure and modern cloud ecosystems.

- Tier 3: Big data & pipeline signals (for advanced/technical roles):
	- Hadoop (22 | ~$113k), Spark (13 | $99k), SSIS/SSRS ($99k–$107k)

-> Common in orgs with larger-scale data systems and more technical analytics expectations.

- Tier 4: Enterprise BI & governance tooling (mature org environments):
	- Looker (49 | ~$104k), Oracle (37 | ~$105k), Qlik (13 | ~$100k)
	- Jira/Confluence (~$105k–$114k) often act as proxies for enterprise process maturity.




# What I Learned

Throughout this adventure, I've turbocharged my SQL toolkit with some serious firepower:

- 🧩 Complex Query Crafting: Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.
- 📊 Data Aggregation: Got cozy with GROUP BY and turned aggregate functions like COUNT() and AVG() into my data-summarizing sidekicks.
- 💡 Analytical Wizardry: Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusions 

### Insight
- Core skills remain non-negotiable. Across the remote market, SQL, Excel, Python, and BI tools (Tableau/Power BI) consistently dominate demand. These skills form the baseline for getting interviews because they map directly to day-to-day analyst work: querying, cleaning, analyzing, and communicating results.
- Higher salaries correlate with “analytics + platform” roles. Skills associated with higher average pay—such as Snowflake, AWS/Azure, BigQuery, Spark/Hadoop, Databricks/Airflow—suggest that better-compensated positions often sit closer to data engineering and cloud ecosystems, where analysts work with larger data volumes, pipelines, and warehouses.
- The most “optimal” path is a balance of demand and salary. When combining demand and compensation (Query 5), the best ROI skills are not the rare niche tools, but the ones that are frequently requested and still pay well—notably Python, Tableau, and R, supported by one cloud/warehouse skill to raise the salary ceiling.

### Closign Thoughts

This project enhanced my SQL skills and provided valuable insights into the data analyst job market. The findings from the analysis serve as a guide to prioritizing skill development and job search efforts. Aspiring data analysts can better position themselves in a competitive job market by focusing on high-demand, high-salary skills. This exploration highlights the importance of continuous learning and adaptation to emerfing trends in the field of data analytics.