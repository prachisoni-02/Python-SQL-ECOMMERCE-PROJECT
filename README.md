# Python-SQL-ECOMMERCE-PROJECT

Project Overview

​This project focuses on analyzing e-commerce sales data to derive actionable insights regarding profitability, customer behavior, and sales trends. By integrating SQL for data querying and Python for processing and automation, this analysis helps in understanding the financial health of the business.

​🛠️ Tech Stack

​Database: SQL (PostgreSQL/MySQL/SQL Server)
​Language: Python 3.x
​Libraries: pandas, pyodbc (or sqlalchemy/mysql-connector), matplotlib/seaborn (for visualization)

​🔗 Database Connection (Python to SQL)

​To connect Python with the SQL database, I used the following approach:
import pandas as pd
import mysql.connector
import os

# List of CSV files and their corresponding table names
csv_files = [
    ('customers.csv', 'customers'),
    ('orders.csv', 'orders'),
    ('sellers.csv', 'sellers'),
    ('products.csv', 'products'),
    ('geolocation.csv', 'geolocation'),
    ('payments.csv', 'payments'),  # Added payments.csv for specific handling
('order_items.csv','order_items')]

# Connect to the MySQL database
conn = mysql.connector.connect(
    host='',
    user='',
    password='',
    database=''
)
cursor = conn.cursor()

# Folder containing the CSV files
folder_path = ""

def get_sql_type(dtype):
    if pd.api.types.is_integer_dtype(dtype):
        return 'INT'
    elif pd.api.types.is_float_dtype(dtype):
        return 'FLOAT'
    elif pd.api.types.is_bool_dtype(dtype):
        return 'BOOLEAN'
    elif pd.api.types.is_datetime64_any_dtype(dtype):
        return 'DATETIME'
    else:
        return 'TEXT'

for csv_file, table_name in csv_files:
    file_path = os.path.join(folder_path, csv_file)
    
    # Read the CSV file into a pandas DataFrame
    df = pd.read_csv(file_path)
    
    # Replace NaN with None to handle SQL NULL
    df = df.where(pd.notnull(df), None)
    
    # Debugging: Check for NaN values
    print(f"Processing {csv_file}")
    print(f"NaN values before replacement:\n{df.isnull().sum()}\n")

    # Clean column names
    df.columns = [col.replace(' ', '_').replace('-', '_').replace('.', '_') for col in df.columns]

    # Generate the CREATE TABLE statement with appropriate data types
    columns = ', '.join([f'`{col}` {get_sql_type(df[col].dtype)}' for col in df.columns])
    create_table_query = f'CREATE TABLE IF NOT EXISTS `{table_name}` ({columns})'
    cursor.execute(create_table_query)

    # Insert DataFrame data into the MySQL table
    for _, row in df.iterrows():
        # Convert row to tuple and handle NaN/None explicitly
        values = tuple(None if pd.isna(x) else x for x in row)
        sql = f"INSERT INTO `{table_name}` ({', '.join(['`' + col + '`' for col in df.columns])}) VALUES ({', '.join(['%s'] * len(row))})"
        cursor.execute(sql, values)

    # Commit the transaction for the current CSV file
    conn.commit()

# Close the connection
conn.close()

import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import mysql.connector

db = mysql.connector.connect( host = "localhost",
username ="",
password ="",
database =""

)
cur = db.cursor ()


📊 Business Questions Addressed

​The analysis is divided into Basic and Intermediate levels to cover all aspects of the dataset:
​Level 1: Basic Analysis
​Total Orders: What is the total number of orders placed?
​Customer Count: How many unique customers are in the database?
​Customer Geography: Where are our customers located? (City/State-wise distribution).
​Level 2: Intermediate Analysis
​Profit & Loss: Calculating total profit and identifying loss-making categories.
​Sales Percentage: Contribution of different product categories to total revenue.
​Order Percentage: Calculating the percentage of orders based on customer location.

​🚀 Key Insights

​Profitability: Identified which products have the highest Profit Margin = \frac{Net Profit}{Revenue} \times 100.
​Customer Retention: Analyzed regions with the highest order density.
​Sales Performance: Spotted seasonal trends in sales growth.

​📂 Project Structure

​data/: Contains raw or sample CSV files (like your Walmart.csv).
​sql_queries/: Includes .sql files with all the analysis queries.
​notebooks/: Jupyter Notebook (.ipynb) containing the Python-SQL integration and visualizations.
​README.md: Project documentation.

​⚙️ How to Run

​Clone the repository.
​Set up your SQL database and import the e-commerce dataset.
​Update the connection string in the Python script.
​Run the Jupyter Notebook to see the analysis and visualizations.



















