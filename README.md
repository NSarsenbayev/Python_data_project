# Overview
This project offers a focused exploration of the data job market, with a particular emphasis on data analyst roles. Motivated by a goal to better understand career opportunities in the field, the analysis highlights the most in-demand and highest-paying skills, aiming to identify the most strategic paths for aspiring and current data analysts.

The dataset originates from [Luke Barousse's Python course](https://www.youtube.com/watch?v=wUSDVGivd-8), which provides detailed job listings including titles, salaries, locations, and required skills. Using Python, this project answers key questions about skill demand, salary patterns, and where these two factors intersect to reveal the most optimal opportunities in data analytics.
# Questions
Below are the questions I want to answer in my project:
1. What are the skills most in demand for the top 3 most popular data roles in the US on 2023?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand & High Paying)

# Tools I Used
To conduct a comprehensive analysis of the data analyst job market, I utilized several essential tools:
- **Python**: The backbone of my analysis, allowing me to analyze the data and find critical insights. I also used the following Python libraries:
- **Pandas Library**: This was used to analyze the data.
- **Matplotlib Library**: Was used to visualize the data.
- **Seaborn Library**: Helped me create more advanced visuals.
- **Anaconda**: Served as a convenient package manager and environment manager, simplifying the setup of all required libraries and dependencies for the project.
- **VS Code + Anaconda Integration**: I created and managed a dedicated Conda environment in VS Code, allowing me to keep my workspace isolated and clean for this specific project.
- **NumPy**: Used for efficient numerical operations, particularly in handling arrays and supporting some of the more data-intensive parts of the analysis.
- **Jupyter Notebooks**: The tool I used to run my Python scripts which let me easily include my notes and analysis.
- **Visual Studio Code**: My go-to for executing my Python scripts.
**Git & GitHub**: Essential for version control and sharing my Python code and analysis, ensuring collaboration and project tracking.

# Data Preparation and Cleanup
I start by importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.
```python
# Importing Libraries
import pandas as pd
from datasets import load_dataset
import seaborn as sns
import matplotlib.pyplot as plt  
import ast

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```
# The Analysis
## 1.	What are the most demanded skills for the top 3 most popular data roles?
To find the most demanded skills for the top 3 most popular data roles, I filtered out those positions by which ones were the most popular, and got the top 5 skills for these top 3 roles. This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to depending on the role I'm targeting.

View my notebook with detailed steps here: [2_Skill_Demand.ipynb](3_Project\2_Skill_Demand.ipynb) 

### Visualize Data
```python
fig, ax = plt.subplots(len(job_titles), 1)
sns.set_theme(style="ticks")

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc["job_title_short"] == job_title].head(5)

    sns.barplot(data=df_plot, x="skill_percentage", y="job_skills", ax=ax[i], hue="job_skills", palette="dark:b")

    ax[i].set_title(job_title)
    ax[i].set_xlabel("")
    ax[i].set_ylabel("")
    ax[i].set_xlim(0, 78)
    
    for n, v in enumerate(df_plot["skill_percentage"]):
        ax[i].text(v + 1, n, f"{v:.0f}%", va="center")
    
    if i != len(job_titles) - 1:
        ax[i].set_xticks([])

fig.suptitle("Likelihood of Skills Requested in US Job Postings", fontsize=15)
fig.tight_layout(h_pad=0.5)
plt.show()
```
### Results
![Visualization of Top Skills for Data Nerds](3_Project\images\skill_demand_all_data_roles.png)


### Insights
- #### General Observations
    - SQL is the most universally required skill, appearing in all roles with high demand.

    - Python is also widely valued, especially for Data Scientists and Engineers.

- ##### Data Analyst
    - SQL (51%) and Excel (41%) are the top skills — traditional data handling tools dominate.
    - Tableau (28%) and Python (27%) show growing demand for visualization and scripting.
    - SAS (19%) still appears, but with lower demand.

- ##### Data Engineer
    - SQL (68%) and Python (65%) are nearly equally dominant.
    - Cloud skills like AWS (43%) and Azure (32%) are key for engineering roles.
    - Spark (32%) shows emphasis on big data technologies.

- ##### Data Scientist
    - Python (72%) is the top skill — the highest percentage across all roles.
    - SQL (51%) and R (44%) are also in strong demand.
    - SAS (24%) and Tableau (24%) are less common but still relevant.

#### Key Takeaways
- Python and SQL are foundational across all data roles.
- Cloud and big data skills (e.g., AWS, Spark) are critical for Data Engineers.
- Visualization tools (Tableau, Excel) are more prominent for Data Analysts.
- Data Scientists show a strong preference for Python and R for statistical modeling.

## 2.	How are in-demand skills trending for Data Analysts?
### Visualize Data
``` python
df_plot = df_DA_US_percent.iloc[:, :5]
sns.lineplot(data=df_plot, dashes=False, palette="tab10")

# annotate the plot with the top 5 skills using plt.text()
from matplotlib.ticker import PercentFormatter
ax = plt.gca()
lines = ax.get_lines()
ax.yaxis.set_major_formatter(PercentFormatter(decimals=0))

for i in range(df_plot.shape[1]): # gives number of columns
    line = lines[i]
    color = line.get_color()
    y = df_plot.iloc[2, i]  # 3rd row's value of column i
    label = df_plot.columns[i]
    plt.text(x=len(df_plot)-0.5, y=y, s=label, color=color)

plt.show()
```
### Results
![Trending Top Skills for Data Analysts in the US](3_Project\images\skill_trend_DA.png)
*Bar graph visualizing the trending top skills for Data Analysts in the US in 2023.*

### Insights
- SQL remains the most in-demand skill throughout the year, consistently above 50% of job postings.
- Excel is the second most mentioned skill but shows a notable decline from mid-year, dropping from ~42% to ~34% before a slight recovery in December.
- Tableau and Python stay close in demand (~27–30%) with minor fluctuations, indicating steady relevance for data visualization and scripting.
- SAS has the lowest demand, hovering around 18–22%, with no major trend change.
- Overall, demand for top data analyst skills remained stable, but Excel's dip is the most significant shift in 2023.

#### Key Takeaway

While *SQL* remains the most consistently in-demand skill for data analysts in the US, the declining trend in *Excel* usage suggests a shift toward more advanced tools like *Python* and *Tableau*, reinforcing the growing importance of programming and visualization skills in the field.

## 3.	How well do jobs and skills pay for Data Analysts?
### Salary Analysis for Data Specialists
### Visualize Data
```python
sns.boxplot(data=df_US_top6, x="salary_year_avg", y="job_title_short", order=job_order)
sns.set_theme(style="ticks")
sns.despine()

plt.title("Salary Distributions of Data Jobs in the US")
plt.xlabel("Yearly Salary ($USD)")
plt.ylabel("")
plt.xlim(0, 600000)
ticks_x = plt.FuncFormatter(lambda x, pos: f'${int(x/1000)}K')
plt.gca().xaxis.set_major_formatter(ticks_x)
plt.show()

```
### Results
![Salary Distributions of Data Jobs in the US](3_Project\images\salary_distribution_boxplot.png
)*Box plot visualizing the salary distributions for the top 6 data job titles in the US*


### Insights
#####  1. Senior Data Scientist

- Highest median salary among all roles.
- Tight interquartile range (IQR), suggesting more consistent salaries.
- Some high-end outliers above $300K.

#####  2. Senior Data Engineer
- Slightly lower median than Senior Data Scientist but still high.
- Wider spread of data, with more visible outliers beyond $300K.

#####  3. Data Scientist
- High median salary.
- Wide distribution, with many outliers reaching toward $400K+.
- Similar range to Senior Data Engineer, showing that some Data Scientists can earn comparably.

#####  4. Data Engineer
- Median lower than Data Scientist but still solid.
- Distribution is wider, with visible outliers, though not as high as Data Scientists’.

#####  5. Senior Data Analyst
- Noticeably lower median than the above roles.
- Narrower IQR, indicating less variation.
- Fewer extreme outliers.

#####  6. Data Analyst
- Lowest median salary in the group.
- More tightly packed data.
- Still, some salaries reach beyond $200K, but those are clear outliers.

#### Key Takeaway
Seniority and job function significantly impact salary in the U.S. data job market.
Senior roles (especially in data science and engineering) command substantially higher and more variable salaries, while analyst roles show lower and more consistent pay.

### Highest Paid & Most Demanded Skills for Data Analysts
### Visualize Data
```python
fig, ax = plt.subplots(2, 1)
# Top 10 Highest Paid Skills for Data Analysts
sns.barplot(data=df_DA_top_pay, x="median", y=df_DA_top_pay.index, ax=ax[0], hue="median", palette="dark:b_r") 

# Top 10 Most In-Demand Skills for Data Analysts
sns.barplot(data=df_DA_skills, x="median", y=df_DA_skills.index, ax=ax[1], hue="median", palette="light:b")

plt.show()
```
### Results
![The highest paid and in-demand skills for Data Analysts in the US](3_Project\images\highest_paid_and_in_demand_skills_for_DA_in_US.png)
*Two bar graphs  visualizing top 10 highest paid and in-demand skills for Data Analysts in the US for year 2023.*

### Insights
##### 1. Highest Paid ≠ Most In-Demand
- Skills like `dplyr, bitbucket, gitlab,` and `solidity` are among the highest paid, but they do not appear in the top in-demand list.
- These skills are likely `niche` or `specialized`, which can drive up salary due to scarcity.

##### 2. Common Tools Pay Less
- Widely used tools like `excel, word,` and `powerpoint` are highly in demand, but they are associated with lower median salaries.
- This suggests that basic office tools are not high-paying, even if they're commonly required.

##### 3. Python and SQL Hit the Sweet Spot
- Both `Python` and `SQL` appear in the top in-demand list and offer competitive median salaries, making them optimal skills for data analysts seeking both job opportunities and decent pay.

#### Key Takeaway

Specialized or emerging tech skills command higher salaries, while foundational tools are more in demand but offer lower pay — skills like **Python** and **SQL** strike the best balance between both.


## 4.	What is the most optimal skill to learn for Data Analysts?
### Visualize Data
```python
from adjustText import adjust_text
from matplotlib.ticker import PercentFormatter

sns.scatterplot(
    data=df_DA_skills_tech_high_demand,
    x="skill_percent",
    y="median_salary",
    hue="technology",
    )
texts = []
for i, txt in enumerate(df_DA_skills_high_demand.index):
    x = df_DA_skills_high_demand["skill_percent"].iloc[i]
    y = df_DA_skills_high_demand["median_salary"].iloc[i]
    
    if txt in ["power bi"]:
        texts.append(plt.text(x, y +500, txt)) # move label up
    else:
        texts.append(plt.text(x, y, txt))

ax = plt.gca() # gca=get current access
# format Y-axis
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, pos: f"${int(y/1000)}K"))

# format X-axis
ax.xaxis.set_major_formatter(PercentFormatter())
```
### Results
![Most Optimal Skills for Data Analysts in the US 2025](3_Project\images\most_optimal_skills_for_DA_with_coloring_by_technology.png)
*A Scatter plot visualizing the most optimal skills (high paying and high demand) for Data Analysts in the US*

### Insights
- Python is both high in salary (~$98K) and moderately in demand (~35%), making it one of the most optimal skills overall.

- SQL has the highest demand (~57%) among all skills but slightly lower salary (~$91K) — a foundational must-have.

- Excel and Word are widely used but offer lower salaries (~$82K–$86K), reflecting basic skill expectations.

- Tableau has a good balance of higher demand (~32%) and above-average salary (~$93K) — valuable for analysts.
- Power BI is in moderate demand (~20%) and associated with a solid median salary (~$90K), making it a valuable business intelligence tool for analysts.
- Go and Oracle offer very high salaries (~$93K–$97K) but are low in demand (~10%), suggesting niche or specialized roles.

- PowerPoint has low demand and low salary (~$85K) — not an optimal differentiator.

- Cloud skills (like Oracle) tend to offer higher salaries but aren't as commonly required in data analyst roles.
#### Key Takeaways
Skills like **`Python`**, **`Tableau`**, and **`SQL`** strike the best balance between market demand and salary potential, making them key targets for aspiring data analysts.

# What I Learned from the Python Course

Completing this online Python course for data analytics significantly strengthened my technical foundation and prepared me to apply Python effectively in real-world data projects. Here are a few key takeaways from the course:

- **Practical Python for Data Analytics**: I gained hands-on experience using core Python libraries like Pandas, NumPy, Matplotlib, and Seaborn to manipulate, analyze, and visualize data efficiently.

- **Data Workflow Skills**: The course walked me through the complete data analysis workflow — from loading and cleaning datasets to transforming and visualizing insights — giving me a solid grasp of best practices in data projects.

- **Problem-Solving with Code**: I learned how to break down analytical problems and solve them programmatically using Python, enhancing both my logical thinking and my ability to write clear, modular code.

- **Real-World Application**s: By working with realistic datasets and scenarios, I built confidence in applying my skills to explore trends, identify patterns, and extract actionable insights.

# Insights
- **Skills and Salary Are Connected**: The more in-demand a skill is, the more it tends to pay. Tools like Python and Oracle often lead to better salaries.

- **The Job Market Is Always Changing**: Some skills become more popular over time, while others fade. Staying up to date is important for growing your career.

- **Learn the Right Skills**: Focusing on skills that are both popular and well-paid can help you get better job opportunities and earn more.

# Challenges & Learning Experiences

While this project came with its share of difficulties, each challenge offered valuable learning opportunities:

- **Cleaning and Preparing Messy Data**: Dealing with missing values and inconsistent entries taught me the importance of careful data cleaning. I learned how even small issues in the dataset can lead to misleading results if not handled properly.

- **Building Clear Visualizations**: Turning raw data into effective visuals wasn’t always straightforward. It pushed me to think more critically about how to represent complex information in a way that’s both clear and meaningful.

- **Finding the Right Level of Detail**: Striking a balance between exploring data deeply and keeping a broad perspective was a constant challenge. I learned to prioritize insights that add the most value without getting lost in less impactful details.

- **Deeper Python Understanding**: On the technical side, I gained a stronger grasp of how functions and methods work under the hood in Python. This helped me write cleaner, more efficient code and better understand how different Python libraries interact behind the scenes.
- **Using AI Tools for Support**: Throughout the project, tools like ChatGPT and Perplexity proved to be incredibly helpful when used thoughtfully. They provided quick explanations, code examples, and helped troubleshoot errors when I got stuck. Rather than replacing learning, they acted as on-demand mentors — offering guidance, clarifying concepts, and speeding up problem-solving, especially when working through complex Python logic or understanding library functions.

# Conclusion
This project gave me a clear and practical understanding of what matters most in the data analyst job market — from the skills in highest demand to the trends shaping the field. The insights gained not only deepened my knowledge but also offer useful direction for anyone looking to grow in data analytics. Since the industry is always evolving, staying curious and continuing to learn will be key. This project serves as a solid starting point for further exploration and reminds me how important it is to keep adapting in the world of data.