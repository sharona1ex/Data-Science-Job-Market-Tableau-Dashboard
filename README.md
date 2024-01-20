# Data Science Job Market Tableau Dashboard (2017 - 2021)
## Objective: Analyzing the data science job market from a job seekers perspective.

(Before Diving in, have a glimpse or you can have a look at my [tableau story]([https://public.tableau.com/shared/?:display_count=n&:origin=viz_share_link](https://public.tableau.com/views/DataScienceJobMarket2017-2021/Story1?:language=en-US&:display_count=n&:origin=viz_share_link)) )

**Dashboard 1**<br>
<img src="https://github.com/StarRider/Data-Science-Job-Market-Tableau-Dashboard/assets/30108439/bcbb611c-5280-48cb-897e-829482659a55" alt="Your Image" width="400" height="290">


**Dashboard 2**<br>
<img src="https://github.com/StarRider/Data-Science-Job-Market-Tableau-Dashboard/assets/30108439/c0accc0f-0c6b-4ece-9ca5-2c7ba4307525" alt="Your Image" width="400" height="290">

**This is a data analysis project that followed a five-step process in the data science workflow, whose parts you can see below:**

### 1. Identify business questions
  1.1 Out of Data Analyst, Data Engineer, Data Scientist and Machine Learning Engineer who has the most demand in the market?<br>
  1.2 Which are the top industries hiring for those roles?<br>
  1.3 What is trend in demand for these roles from 2017 - 2021?<br>
  1.4 What skills are being sought for these roles?<br>
  1.5 What is the median salary for these roles?<br>
  1.6 Is my present skill-set in demand for any of the roles? If yes, then which roles align with my skills the most?

### 2. Collect and store data
  For this project, I have downloaded data from [Datacamp's Case Study](https://s3.amazonaws.com/assets.datacamp.com/production/repositories/6121/datasets/Workbooks+and+Datasources/case-study-analyzing-job-market-data-in-tableau.zip). This data is representing the IT market in general and is fictitious in general, so the conclusions and analysis made in this project should not be applied in real life.  (This project is solely for learning purpose of data analysis workflow)
    
    
### 3. Clean and prepare data
  The dataset name is **job_postings.csv** with 25,114 rows. Firstly, glance through the columns and data in each column so that you have a preliminary idea of what is present in the dataset. Second, get some stats on how much null values are present in the dataset using Python's pandas. 
````python
import pandas as pd

def missing(df):
    # Calculate the column-wise percentage of rows with missing values
    columnwise_percentage_missing = (df.isna().mean(axis=0) * 100).round(2)
    return columnwise_percentage_missing
    
jobs_path = r"path\to\job_postings.csv"
jobs_df = pd.read_csv(jobs_path)

# jobs missing values
missing(jobs_df)
````

```
Columns                       Missing values %
Job Posting ID                0.00
Job Posting Date              0.00
Job Title                     0.00
Job Title Full                0.00
Job Title Additional Info    67.40
Job Position Type             0.00
Job Position Level            0.00
Years of Experience           0.00
Job Skills                    8.80
Job Location                  0.00
Minimum Pay                  92.78
Maximum Pay                  92.78
Pay Rate                     92.78
Number of Applicants         30.20
Company Name                  0.24
Company Industry              0.87
Company Size                  0.88
```

How will you know which columns to treat? The best way is to take a look at the business questions and select the columns that can potentially answer our question. Those are columns that you need to deal with.

As you can see that columns 'Company Industry', 'Job Skill' those are our point of our interest has null values. We can simply drop those rows as there is only 0.87% and 8.8% of rows affected (I am okay with that). Other columns of concern with null values are 'Minimum Pay' and 'Maximum Pay' are also crucial for us to understand the pay for relevant roles. However, we can't drop those rows as there are about 93% of rows affected. We are going to ignore the null values in the analysis of salary for these columns and we will limit our analysis to it using box plots.

While dealing with null values, the 'Job Skills' column caught my attention as it was crucial for one of our business question (Refer 1.4, 1.6).<br> 
*The column has a list of skills for each row. One more issue was that there were values same to each other but written differently (e.g. power_bi and powerbi both are the same skill)*. 


```
Multi-valued column issue
 Job Posting ID                                         Job Skills
1       2719108338  data_lake, cloud, python, spark, github, wareh...
5       2697854773  data_lake, cloud, data_lakes, python, hadoop, ...
7       2628572485  data_lake, database, python, hadoop, ibm, orac...
8       2748225724  python, programming, etl, postgresql, linux, s...
13      2698913189  cloud, power_bi, python, gcp, programming, aws...
```

To address this, I extracted the 'Job Posting ID' and 'Job Skills' to form a table with column 'Job Posting ID' and 'Job Skills' making the second column single valued.

```
Single-valued column after cleaning
   Job Posting ID   Job Skills
0      2701524240     database
1      2701524240   javascript
2      2701524240        agile
3      2701524240        linux
4      2701524240       server
```
Below is the code for that:
````python
# get the columns of interest
jobs_df_skills = jobs_df[['Job Posting ID', 'Job Skills']]
# now explode the multi-valued columns into single valued columns
jobs_df_skills = jobs_df_skills.explode("Job Skills")
````

Now, the for resolving the similar word issue (power_bi and powerbi e.t.c.), I used fuzzywuzzy library in python to get a list of all similar looking words.
````python
from fuzzywuzzy import fuzz


def find_similar_words(word, word_list, threshold=80):
    """
    Find similar words in a list based on fuzzy matching.

    Parameters:
    - word: str, the word to find similarities for
    - word_list: list of str, the list of words to compare against
    - threshold: int, the similarity threshold (default is 80)

    Returns:
    - list of similar words
    """
    similar_words = [w for w in word_list if fuzz.ratio(word, w) >= threshold]
    return similar_words

````
  Using this function I got a list that words and there similar looking words. Using that list I replaced all the similar looking words into one consistent word.
```
This are some of words that look similar but are written differently:
['ai/ml', 'ml'], ['powerbi', 'power_bi'], ['backend', 'back-end']....
```
  After the cleaning and data preparation, we have two tables **job_postings** and **job_skill**.

### 4. Analyze data
  After analyzing the data, the following observations where made:
  1. Data engineers are the most in-demand roles.
     ![image](https://github.com/StarRider/Data-Science-Job-Market-Tableau-Dashboard/assets/30108439/cc85a6d8-8b8d-4f57-b37d-562227f44d4e)

  2. Despite decline in demand for data science roles due to Covid-19, these roles show a positive outlook towards the future.
     ![image](https://github.com/StarRider/Data-Science-Job-Market-Tableau-Dashboard/assets/30108439/4368babe-7b5b-49a5-a4b6-fe4e33cbd638)

  3. Python is the most popular among recruiters for data science roles.
     ![image](https://github.com/StarRider/Data-Science-Job-Market-Tableau-Dashboard/assets/30108439/0ba36682-efe9-4881-bf9c-154325fc2fc8)

  4. ML engineers are the once with higher median salaries.
     ![image](https://github.com/StarRider/Data-Science-Job-Market-Tableau-Dashboard/assets/30108439/8197e238-54c7-41ca-a987-575c0ae7192f)
  
  These observations helps us to get a top level idea about the data science job market.
  
### 5. Visualize and communicate data
  I have created two dashboards.
  #### 5.1 Dashboard 1 | Data Science Job Market Overview (2017 - 2021)
  This dashboard helps a user to explore each job role in detail. For example the user will be able to look at role level demand trend, industries that hire the most for this role and industry level demand trend and salaries related to a selected role.
  #### 5.2 Dashboard 2 | Where do you stand in the data science market?
  This dashboard asks users about their skills and desired type of job levels (entry, intern, senior e.t.c). Once the user gives these inputs, the interface will provide the user with insights about his/her own skill demand in the market and which roles align mostly with their existing skill set.

  [Link to the dashboards](https://public.tableau.com/views/DataScienceJobMarket2017-2021/Story1?:language=en-US&publish=yes&:display_count=n&:origin=viz_share_link)

  Enjoy!!
   
   
   

