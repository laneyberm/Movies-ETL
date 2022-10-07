# ETL process using Wikipedia data, Kaggle metadata, and the MovieLens rating data

## Overview

Amazing Prime is seeking a dataset for movies anaylsis and wants to keep it updated on a daily basis. We created an automated pipeline that takes in new data, performs the appropriate transformations, and loads the data into existing tables. We used Extract, Transform, Load (ETL) process to create data pipelines. A data pipeline moves data from a source to a destination, and the ETL process creates data pipelines that also transform the data along the way. Analysis is impossible without access to good data, so creating data pipelines is often the first step before any analysis can be performed. 

We created a code and refactored the code to create one function that takes in the three files—Wikipedia data, Kaggle metadata, and the MovieLens rating data. Additionally, the code performs the ETL process by adding the data to a PostgreSQL database to be able to be updated daily as needed. Below are the steps and findings taken throught the ETL process. 

In the Extract phase, data is pulled from external or internal sources, possibly disparate. The sources could be flat files, scraped webpages in HTML or JavaScript Object Notation (JSON) format, SQL tables, or even streams of sensor data. The extracted data is held in a staging area in between the data sources and data targets.

After data is extracted, there are many transformations it may need to go through. The data may need to be filtered, parsed, translated, sorted, interpolated, pivoted, summarized, aggregated, merged, or more. The goal is to create a consistent structure in the data. Without a consistent structure in our data, it's almost impossible to perform any meaningful analysis.

The transformation phase can be accomplished with Python and Pandas, pure SQL, or specialized ETL tools like Apache Airflow or Microsoft SQL Server Integrated Services (SSIS). Python and Pandas are especially good for prototyping an ETL transformation because they provide flexibility and interactivity (especially in a Jupyter Notebook), without enforcing any complicated frameworks. We will use Python and Pandas to explore, document, and perform our data transformation.

Finally, after the data is transformed into a consistent structure, it's loaded into the data target. The data target can be a relational database like PostgreSQL. We'll be loading our data into a PostgreSQL table. SQL databases are often the targets of ETL processes, and because SQL is so ubiquitous, even databases that don't use SQL often have SQL-like interfaces.

Lastly, we created an automated pipeline, by converting the Jupyter Notebooks to a Python script. 


## Resources
- Data Source: movies_metadata.csv, ratings.csv, wikipedia-movies.json
- Software: Jupyter Notebook 6.4.8, Python 3.7.13, PostgreSQL 14.5, pgAdmin 4 version 6.12 

## Process
### Data Cleaning
data-cleaning strategy. You're going to follow an iterative process based on three key steps: plan, inspect, execute. The iterative process for cleaning data can be broken down as follows:

First, we need to inspect our data and identify a problem.
Once we've identified the problem, we need to make a plan and decide whether it is worth the time and effort to fix it.
Finally, we execute the repair.

Early iterations focus on making the data easier to investigate: deleting obviously bad data, removing superfluous columns (e.g., columns with only one value or missing an overwhelming amount of data), removing duplicate rows, consolidating columns, and reshaping the data if necessary.

Our Wikipedia data is especially messy. As much as editors try to be consistent, each page can be edited by a different person. Besides, because the movie data comes from the sidebar, different movies can have different columns. However, after cleaning the data, the result will be a nice, organized table of data, where every row is a single movie. You'll start by investigating the data for errors.

One of the easiest ways to find glaring errors is to just pretend as if there aren't any, and try to jump straight to the finish line. Eventually, we want to clean up the Wikipedia data into tabular data with rows and columns, so let's see what happens if we create a DataFrame from our raw data.

Now that you've filtered out bad data, you need to clean up each movie entry so it's in a standard format. If you can make one process broad enough to handle every movie entry, you can apply that process repeatedly for every movie entry. For this task you will create a function.

There are some data-cleaning tasks that are easier to perform on a DataFrame, such as removing duplicate rows. Luckily, we just created a process to turn our JSON data into a reasonable DataFrame. In fact, we'll start by removing duplicate rows. Since we're going to be using the IMDb ID to merge with the Kaggle data, we want to make sure that we don't have any duplicate rows, according to the IMDb ID. First, we need to extract the IMDb ID from the IMDb link.

### ETL
In the Extract phase, data is pulled from the Wikipedia and Kaggle data from their respective files. The extracted data is held in a staging area in between the data sources and data targets. We extracted scraped Wikipedia data stored as a JSON, and Kaggle data stored in CSVs. Transform the datasets by cleaning them up and joining them together. load the cleaned dataset into a SQL database

To extract the ID, we need to learn regular expressions. Regular expressions are just strings of characters that are used as a search pattern. They are used to test if strings are in a specific format or contain a substring in a specific format, to extract pertinent information from strings while discarding unnecessary information, and to perform complicated replacements of substrings. You knew this day would come: the day when you conquered regular expressions. While they may sound a bit underwhelming, regular expressions are actually quite powerful. (You could almost say the outsized impact they will allow you to have on your dataset is irregular.) Puns aside, you know a big reason Britta trusted you with this dataset is because you wouldn't shy away from learning how to use and apply regular expressions. Even though there's still more to regular expressions, we now have enough for us to parse the information in our dataset, so let's get back to it. Remember, there are two main forms the box office data is written in: "$123.4 million" (or billion), and "$123,456,789." We're going to build a regular expression for each form, and then see what forms are left over.

Luckily, you already did a lot of the heavy lifting for parsing the budget data when you parsed the box office data. You'll use the same pattern matches and see how many budget values are in a different form. Luckily, we've already done a lot of the heavy lifting for parsing the budget data when we parsed the box office data. We'll use the same pattern matches and see how many budget values are in a different form. First, we need to preprocess the budget data, just like we did for the box office data.

The Kaggle data that Britta found is much more structured, but it still requires some cleaning, including converting strings to correct data types. Therefore, your next task is to clean the Kaggle data.

Now that the Wikipedia data and Kaggle data are cleaned up and in tabular formats with the right data types for each column, Britta can join them together. However, after they're joined, the data still needs to be cleaned up a bit, especially where Kaggle and Wikipedia data overlap. One of the things we always want to look out for after we've merged data is redundant columns. There are seven pairs of columns that have redundant information. We'll look at each pair of columns and decide how to handle the data.

There are a few options when dealing with redundant data. We'll consider two. The simplest is to just drop one of the competing columns, but sometimes that means a loss of good information. Sometimes, one column will have data where the other has missing data, and vice versa. In that case, we'd want the other option: fill in the gaps using both columns.

Below is the list of competing columns. We'll fill in the resolution to each pair as we go along. We'll hold off on implementing the resolutions until we make a decision for each pair because if we did, we might inadvertently remove data that could be helpful in making a later decision.

Britta wants to include the rating data with the movie data, but it's a very large dataset. She wants to reduce the ratings data to a useful summary of rating information for each movie, and then make the full dataset available to the hackathon participants if they decide they need more granular rating information.

For each movie, Britta wants to include the rating data, but the rating dataset has so much information that it's too unwieldy to use all of it. We could calculate some basic statistics like the mean and median rating for each movie, but a more useful summary is just to count how many times a movie received a given rating. This way, someone who wants to calculate statistics for the dataset would have all the information they need. 

We'll include the raw ratings data if the hackathon participants want to do more in-depth analysis, such as comparing across users, but having the rating counts for each movie is easy enough to do. Plus, it will enable the hackathon participants to calculate statistics on their own without having to work with a dataset containing 26-million rows. And we're done—we just finished the Transform step in ETL! Now all that's left is loading our tables into SQL.


Amazing Prime has decided the easiest way to make the data accessible for the hackathon is to provide a SQL database to the participants. Britta needs to move the data from Pandas into a PostgreSQL database.

Now that we've extracted and transformed our data, it's time to load it into a SQL database. We're going to create a new database and use the built-in to_sql() method in Pandas to create a table for our merged movie data. We'll also import the raw ratings data into its own table.

Start pgAdmin and expand your local servers in the left-hand pane so you can see the Databases section. Right-click on Databases and select Create followed by Database. We need to import create_engine from the sqlalchemy module. Don't forget to add this import to the first cell so that all your imports are in one spot. Now we can create the database engine that will allow Pandas to communicate with our SQL server.

This is all the information that SQLAlchemy needs to create a database engine.SQLAlchemy handles connections to different SQL databases and manages the conversion between data types. The way it handles all the communication and conversion is by creating a database engine. Import the Movie Data. The ratings data is too large to import in one statement, so it has to be divided into "chunks" of data. To do so, we'll need to reimport the CSV using the chunksize= parameter in read_csv(). This creates an iterable object, so we can make a for loop and append the chunks of data to the new rows to the target SQL table. 

If everything looks good, you are done. You just extracted really messy and almost unusable data, combed through it carefully to transform it, and then loaded it into a SQL database. Now the hackathon has a reliable, clean dataset just begging to be analyzed. Britta will definitely appreciate the hard work you put in.

Congrats on performing your first ETL process. By the way, ETL isn't the only way to create a data pipeline (even though it's the most common). There is also the Extract, Load, and Transform (ELT) paradigm.

With ELT, data is stored as unstructured data in a data lake and transformed when analyses are performed. This requires very powerful analytical tools to perform the transformation tasks quickly, where ETL frontloads the transformation to make analyses easier to perform.

## Results


### Movies Query
<img src="https://github.com/laneyberm/Movies-ETL/blob/main/movies_query.png" width="150">

- 
- 

### Ratings Query
<img src="https://github.com/laneyberm/Movies-ETL/blob/main/ratings_query.png" width="150">

- 
- 

## Summary
 
