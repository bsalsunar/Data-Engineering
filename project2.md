# 🎬 Scalable Movie Ratings Analytics Pipeline Using PySpark
A complete End-to-End PySpark Data Engineering Project that simulates a real-world movie streaming analytics platform.

This project demonstrates how to build a scalable ETL and analytics pipeline using Apache Spark (PySpark) for processing movie ratings data.

## 🚀 Project Overview

A movie streaming company collects millions of user ratings daily.
The analytics team needs a scalable pipeline to:  

Analyze user behavior  
Identify trending/popular movies  
Calculate average movie ratings  
Detect corrupt or low-quality records  
Generate reporting datasets  
Handle scalable big data processing  

This project solves the above business problem using PySpark DataFrames and distributed processing techniques.

## 🛠️ Technologies Used

Python   
PySpark  
Apache Spark  
Databricks  
Spark SQL  
DataFrames API  

## 📂 Project Structure

movie-ratings-pyspark/  
│  
├── Project 2 pyspark.ipynb  
├── README.md  
└── sample_data/  

## 📊 Dataset Used

### Ratings Dataset

Contains:

user_id  
movie_id  
rating  
timestamp  

### Example

ratings_data = [  
   (1, 101, 5, "2026-01-01 10:00:00"),  
   (2, 101, 4, "2026-01-01 10:05:00"),  
   (3, 102, 3, "2026-01-01 11:00:00"),  
]  

### Movies Dataset

Contains:

movie_id  
movie_name  
genre  
release_year  

### Example

movies_data = [  
   (101, "Avengers", "Action", 2012),  
   (102, "Titanic", "Romance", 1997),  
]  

## ⚙️ ETL Pipeline Steps

### 1️⃣ Data Ingestion

Load raw ratings and movie datasets into Spark DataFrames.

ratings_df = spark.createDataFrame(ratings_data, ratings_columns)  
movies_df = spark.createDataFrame(movies_data, movies_columns)  

### 2️⃣ Data Cleaning & Validation

✅ Remove NULL Values  
ratings_clean = ratings_df.dropna(subset=["rating", "timestamp"])  

✅ Remove Invalid Ratings  

Only ratings between 1–5 are allowed.  

from pyspark.sql.functions import col  

ratings_clean = ratings_clean.filter(  
   (col("rating") >= 1) & (col("rating") <= 5)  
)  

### ✅ Remove Duplicate Records

ratings_clean = ratings_clean.dropDuplicates(  
   ["user_id", "movie_id", "timestamp"]  
)  

### 3️⃣ Data Enrichment

Join ratings data with movie information.  
enriched_df = ratings_clean.join(  
   movies_df,  
   "movie_id",  
   "left"  
)  

### 4️⃣ Analytics & Aggregation

#### ⭐ Average Rating per Movie

from pyspark.sql.functions import avg, count

avg_rating = enriched_df.groupBy(  
   "movie_id",  
   "movie_name"  
).agg(  
   avg("rating").alias("avg_rating"),  
   count("rating").alias("total_ratings")  
)  

## 📈 Features Implemented

✅ Data Cleaning  
✅ Null Handling  
✅ Duplicate Removal  
✅ Invalid Data Detection  
✅ Data Enrichment using Joins  
✅ Aggregation & Analytics  
✅ Scalable Spark Processing  
✅ ETL Pipeline Architecture  

## 📷 Sample Output

Movie Name  Avg Rating  Total Ratings  
Avengers  4.5          2  
Titanic  3.0           1  


## ▶️ How to Run the Project

### Step 1 — Install PySpark  
pip install pyspark  

### Step 2 — Start Spark Session  
from pyspark.sql import SparkSession  

spark = SparkSession.builder \  
   .appName("Movie Ratings Analytics") \  
   .getOrCreate()  

### Step 3 — Run the Notebook  
Open the notebook in:  
Databricks  
Jupyter Notebook  
VS Code  
Run all cells sequentially.  

## 💡 Real-World Use Cases

Netflix Analytics  
Amazon Prime Recommendations  
User Behavior Analysis  
Trending Content Detection  
Recommendation Systems  
Big Data ETL Pipelines  

## 📚 Learning Outcomes

Through this project, you will learn:  
PySpark DataFrames  
ETL Pipeline Design  
Data Cleaning Techniques  
Spark Transformations & Actions  
Aggregations in Spark  
Real-world Data Engineering Concepts  

## 🔥 Future Enhancements

Kafka Streaming Integration  
AWS S3 Data Lake  
Delta Lake Support  
Airflow Scheduling  
Recommendation Engine  
Dashboard Visualization using Power BI/Tableau  

## 👨‍💻 Author

### Bishal Sunar  
Master’s Student in Information Technology Systems  

### Interested In  
Data Engineering  
Cloud Computing  
Big Data Analytics  
Business Intelligence  
