- # Introduction
Dive into the Data job market! focusing on Data Analyst roles, This project explores top paying jobs, in demand skills, and where high demand meets high salary in Data Analytics.

SQL queries? Check them out here: [project_sql folder](/project_sql/)

- # Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

The question I wanted to answer through my SQL queries were:

1. What are the top-paying Data Analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for Data Analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

- # Tools I Used
- **SQL**: The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL**: The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code**: My go-to for database management and executing SQL querirs.
- **Git & Github**: Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

- # The Analysis
Each query for this projeect aimed at investigating specific aspects of the data analyst job market.

Here's how I approached each question:

### 1. Top paying Data Analyst jobs
To identify the highest-paying roles I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title_short,
    job_title,
    name AS company_name,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date
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
```
Here's the breakdown of the Top Data Analyst jobs in 2023:

- **Wide Salary Range:** Top 10 paying Data Analyst roles span from $184,000 to $650,000, indicating significant salary potential in the field.

- **Diverse Employers**: Companies Like SmartAssets, Meta, and AT&T are among those offereing high salaries, showing a broad intrest across different industries.

- **Job Title Variety**: There is high diversity in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specializations within Data Analytics.

### 2. Skills for Top paying jobs
To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title_short,
        job_title,
        name AS company_name,
        salary_year_avg
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
FROM
    top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id   
ORDER BY
    salary_year_avg DESC
```
Here's the breakdown of most demanded skills for the Top 10 highest paying Data Analyst jobs in 2023:

- **SQL** - 8 occurrences
- **Python** - 7 occurrences
- **Tableau** - 6 occurrences
- **R** - 4 occurrences
- **Snowflake** - 3 occurrences
- **Pandas** - 3 occurrences
- **Excel** - 3 occurrences
- **Azure** - 2 occurrences
- **Confluence** - 2 occurrences
- **Go** - 2 occurrences

These skills highlight the importance of data manipulation tools (**SQL**, **Python**, **Pandas**), 
data visualization (**Tableau**), and cloud/data management platforms (**Snowflake**, **Azure**) in data analyst roles.

### 3. In-Demand Skills For Data Analysts

This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id  
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5
```
Here's the breakdown of the most demanded skills for Data Analyst in 2023:

- **SQL** and **Excel** remain fundamental, emphasizing the need for strong foundational skills in data processing and spreadsheet manipulation.

- **Programming** and **Visualization Tools** like **Python** , **Tableau** , and **Power BI** are essential, pointing towards the increasing importance of technical skills in data storytelling and decision support.

| Skill     | Demand Count |
|-----------|--------------|
| SQL       | 7,291        |
| Excel     | 4,611        |
| Python    | 4,330        |
| Tableau   | 3,745        |
| Power BI  | 2,609        |

*Table of the demanded for the Top 5 skills in  Data Analyst job postings.*

### 4. Skills Based on Salary

Exploring the average salaries associated with different skills revealed which skills are the highest paying.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id  
WHERE
    job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL AND
    job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25    
```
Here are some key insights based on the top-paying skills for Data Analysts in 2023:
- **Big Data:** Skills like **PySpark** and **Couchbase** are highly valued for managing large datasets.
- **AI/ML:** Tools like **Watson** and **DataRobot** show strong demand for AI expertise.
- **Programming:** Languages like **Swift** and **Python** libraries (Pandas, NumPy) are essential for data work.
- **DevOps/Cloud:** **Kubernetes**, **GCP**, and **Jenkins** reflect the importance of cloud infrastructure.
- **Collaboration:** Tools like Bitbucket and GitLab highlight the need for effective version control and teamwork.

| Skill          | Average Salary (USD) |
|----------------|----------------------|
| PySpark        | 208,172              |
| Bitbucket      | 189,155              |
| Couchbase      | 160,515              |
| Watson         | 160,515              |
| DataRobot      | 155,486              |
| GitLab         | 154,500              |
| Swift          | 153,750              |
| Jupyter        | 152,777              |
| Pandas         | 151,821              |
| Elasticsearch  | 145,000              |

*Table of the average salary for the top 10 paying skills for data analysts.*

### 5. Most Optimal Skills to Learn

Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skills developement.

```sql

SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(salary_year_avg),0) AS avg_salary
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id   
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE AND
    salary_year_avg IS NOT NULL
GROUP BY
    skills_dim.skill_id,
    skills_dim.skills
HAVING
    COUNT(skills_job_dim.job_id) > 10    
ORDER BY
    avg_salary DESC,
    demand_count DESC   
LIMIT 25;
```
| Skill ID | Skill      | Demand Count | Average Salary (USD) |
|----------|------------|--------------|----------------------|
| 8        | Go         | 27           | 115,320              |
| 234      | Confluence | 11           | 114,210              |
| 97       | Hadoop     | 22           | 113,193              |
| 80       | Snowflake  | 37           | 112,948              |
| 74       | Azure      | 34           | 111,225              |
| 77       | BigQuery   | 13           | 109,654              |
| 76       | AWS        | 32           | 108,317              |
| 4        | Java       | 17           | 106,906              |
| 194      | SSIS       | 12           | 106,683              |
| 233      | Jira       | 20           | 104,918              |

*Table of the most optimal skills for data analysts in 2023.*

Here are the trends from the most optimal (high-demand and high-paying) skills for Data Analysts in 2023:

- **Cloud and Data Platforms:** Skills like Snowflake ($112,948), Azure ($111,225), and AWS ($108,317) are in high demand, reflecting the shift to cloud-based data solutions and infrastructure.

- **Big Data and Data Engineering**: Tools like Hadoop ($113,193) and Spark ($99,077) are crucial for big data processing, indicating strong demand for professionals skilled in handling large-scale data.

- **Programming Languages:** Go ($115,320), Java ($106,906), and Python ($101,397) continue to be foundational in data analytics, with Python having the highest demand across roles.

- **Business Intelligence and Data Visualization:** Tools like Looker ($103,795), Tableau ($99,288), and Qlik ($99,631) are essential for turning raw data into actionable insights, driving their high demand.

- **Collaboration and Project Management:** Skills like Confluence ($114,210) and Jira ($104,918) highlight the importance of project management and team collaboration tools in complex data-driven environments.

- # What I Learned

- **Complex Query Crafting:** Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.

- **Data Aggregation:** Got  cozy with GROUP BY and turned aggregate functions like COUNT() and AVG() into my data-summarizing sidekicks.

- **Analytical Wizardry:** Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

- # Conclusion

### Insights
From the analysis, several general insights emerged:

1. **Top-Paying Data Analyst Jobs:** The highest-paying jobs for data analysts that allow remote work offer a wide range of salaries, the highest at $650,000.

2. **Skills for Top-Paying Jobs:** High-Paying data analyst jobs require advanced proficiency in SQL, suggesting it's a critical skill for earning a top salary.

3. **Most In-Demand Skills:** SQL is also the most demanded skills in the data analysts job market, thus making it essential for job seekers.

4. **Skills with Higher Salaries:** specialized skills, such as PySpark and Bitbucket, associated with the highest average salaries, indicating a premium on niche expertise.

5. **Optimal Skills for Job Market Value:** SQL leads in demand and offers for a high average salary, positioning it as one of the most optimal their market value.

### Closing Thoughts

This project enhanced my SQL skills and provided valuable insights into data analyst job market. The findings from the analysts serve as a guide to prioritizing skills developement and job search efforts. Aspiring data analysts can better position themselves in a competitive job market by focusing on high-demand, high-salary skills. This exploration highlights the importance of continuous learning and adaptation to emerging trends in the field of data analytics
