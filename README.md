# ETL process using Wikipedia data, Kaggle metadata, and the MovieLens rating data

## Overview

Amazing Prime is seeking a dataset for movies anaylsis and wants to keep it updated on a daily basis. We created an automated pipeline that takes in new data, performs the appropriate transformations, and loads the data into existing tables. We used Extract, Transform, Load (ETL) process to create data pipelines. A data pipeline moves data from a source to a destination, and the ETL process creates data pipelines that also transform the data along the way. Analysis is impossible without access to good data, so creating data pipelines is often the first step before any analysis can be performed. 

We created a code and refactored the code to create one function that takes in the three files—Wikipedia data, Kaggle metadata, and the MovieLens rating data. Additionally, the code performs the ETL process by adding the data to a PostgreSQL database to be able to be updated daily as needed. Below are the steps and findings taken throught the ETL process. 

In the Extract phase, data is pulled from external or internal sources, possibly disparate. The sources could be flat files, scraped webpages in HTML or JavaScript Object Notation (JSON) format, SQL tables, or even streams of sensor data. The extracted data is held in a staging area in between the data sources and data targets.

After data is extracted, there are many transformations it may need to go through. The data may need to be filtered, parsed, translated, sorted, interpolated, pivoted, summarized, aggregated, merged, or more. The goal is to create a consistent structure in the data. 

The transformation phase can be accomplished with Python and Pandas, pure SQL, or specialized ETL tools like Apache Airflow or Microsoft SQL Server Integrated Services (SSIS). Python and Pandas are especially good for prototyping an ETL transformation because they provide flexibility and interactivity (especially in a Jupyter Notebook), without enforcing any complicated frameworks. We will use Python and Pandas to explore, document, and perform our data transformation.

Finally, after the data is transformed into a consistent structure, it's loaded into the data target. The data target can be a relational database like PostgreSQL. We'll be loading our data into a PostgreSQL table. SQL databases are often the targets of ETL processes, and because SQL is so ubiquitous, even databases that don't use SQL often have SQL-like interfaces.

Lastly, we created an automated pipeline, by converting the Jupyter Notebooks to a Python script. 


## Resources
- Data Source: movies_metadata.csv, ratings.csv, wikipedia-movies.json
- Software: Jupyter Notebook 6.4.8, Python 3.7.13, PostgreSQL 14.5, pgAdmin 4 version 6.12 

## Process
### Data Cleaning
We used a data-cleaning strategy and iterative process based on three key steps: plan, inspect, execute. 
- Inspect our data and identify a problem.
- Make a plan and decide whether it is worth the time and effort to fix it
- Execute the repair

Early iterations focused on the following to make the data easier to investigate: 
- Deleting obviously bad data
- Removing superfluous columns (e.g., columns with only one value or missing an overwhelming amount of data) 
- Removing duplicate rows 
- Consolidating columns
- Reshaping the data if necessary

The Wikipedia scraped data wass especially messy. As much as editors try to be consistent, each page can be edited by a different person. Additionally, because the movie data comes from the sidebar, different movies can have different columns. We performed the following data-cleaning tasks:
- Created a process to turn our JSON data into a reasonable DataFrame
- Removed duplicate rows
- Ensured that every movie contained an unique IMDb ID to merge with the Kaggle data
- Extract the IMDb ID from the IMDb link

### ETL
We extracted scraped Wikipedia data stored as a JSON, and Kaggle data stored in CSVs, transformed the datasets by cleaning them up and joining them together and load the cleaned dataset into a SQL database.

In the extraction process, we used regular expressions (Regex) to test for search patterns. We found competing columns between the Wikipedia and Kaggle data. In the Transform step, we fixed the issues below:

<img src="https://github.com/laneyberm/Movies-ETL/blob/main/Resources/competing_columns.png" width="450">

We added raw ratings data from MovieLens, a dataset containing 26-million rows, so we can complete in-depth analysis and calculate statistics without having to open the 26 million dataset. We moved the data from Pandas into a PostgreSQL database and load it into a SQL database. We created a new database and used the built-in to_sql() method in Pandas to create a table for our merged movie data. We also imported the raw ratings data into its own table. While importing the Movie Data, ratings data is too large to import in one statement, so it was to be divided into "chunks" of data. We re import the CSV using the chunksize= parameter in read_csv(). This creates an iterable object, so we can make a for loop and append the chunks of data to the new rows to the target SQL table. 

<img src="https://github.com/laneyberm/Movies-ETL/blob/main/Resources/importing_rows.png" width="500">

We verified that the data contained 6051 movies and 26,0024,289 ratings to ensure all the data was loaded to the tables in SQL. 
<img src="https://github.com/laneyberm/Movies-ETL/blob/main/Resources/movies_query.png" width="150">
<img src="https://github.com/laneyberm/Movies-ETL/blob/main/Resources/ratings_query.png" width="150">

## Conclusion
We extracted really messy and almost unusable data, combed through it carefully to transform it, and then loaded it into a SQL database. Now, Amazing Prime has a reliable, clean dataset just begging to be analyzed.

