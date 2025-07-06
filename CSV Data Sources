Market Data Snippet:
 Date,Symbol,AdjClose,Volume
2023-01-02,AAPL,134.47,27735830
2023-01-03,AAPL,135.39,94410762
2023-01-04,AAPL,136.21,89358551
2023-01-05,AAPL,139.04,89409749
2023-01-06,AAPL,139.33,5521373
2023-01-09,AA

Fundamentals Data:
 Symbol,PERatio,EPS,MarketCapB
AAPL,28.5,5.9,2800
GOOGL,25.2,5.1,1700
MSFT,35.8,9.7,2500
AMZN,58.1,2.3,1300


Sentiment Data:
 Date,Symbol,SentimentScore,Headline
2023-10-25,AAPL,-0.5,"Apple faces antitrust probe in Europe"
2023-10-26,MSFT,0.8,"Microsoft smashes earnings expectations on cloud strength"
2023-10-27,GOOGL,0.6,"Google announces breakthroughs in generative AI"
2023-10-28,AMZN,-0.3,"Amazon reports sluggish growth in e-commerce division"
2023-11-15,AAPL,0.9,"New iPhone receives stellar reviews"
2023-11-16,MSFT,0.7,"Success of GitHub Copilot integration boosts stock"


--- Step 1: Ingesting and processing CSV data ---
Market data loaded and daily returns calculated.
        Date Symbol  AdjClose    Volume  DailyReturn
0 2023-01-02   AAPL    134.47  27735830     0.000000
1 2023-01-03   AAPL    135.39  94410762     0.006842
2 2023-01-04   AAPL    136.21  89358551     0.006057
3 2023-01-05   AAPL    139.04  89409749     0.020777
4 2023-01-06   AAPL    139.33   5521373     0.002086

--- Step 2: Building the Star Schema Data Model ---
Data model created successfully.
D_Stock Dimension:
   Symbol            CompanyName             Sector  PERatio  EPS  MarketCapB  \
0   AAPL             Apple Inc.         Technology     28.5  5.9      2800.0   
1  GOOGL          Alphabet Inc.         Technology     25.2  5.1      1700.0   
2   AMZN       Amazon.com, Inc.  Consumer Cyclical     58.1  2.3      1300.0   
3   MSFT  Microsoft Corporation         Technology     35.8  9.7      2500.0   
4    SPY       SPDR S&P 500 ETF              Index      NaN  NaN         NaN   

   StockKey  
0         1  
1         2  
2         3  
3         4  
4         5  

F_DailyPerformance Fact Table:
    DateKey  StockKey  ClosePrice    Volume    Return  SentimentScore
0        1         1      134.47  27735830  0.000000             NaN
1        2         1      135.39  94410762  0.006842             NaN
2        3         1      136.21  89358551  0.006057             NaN
3        4         1      139.04  89409749  0.020777             NaN
4        5         1      139.33   5521373  0.002086             NaN

--- Step 3: Loading to Warehouse and calculating advanced metrics ---
Advanced Risk/Return Metrics:
   Symbol      Beta  AnnualizedSharpeRatio
0   AAPL -0.075966               0.825366
1   AMZN  0.115583               1.184759
2  GOOGL  0.011697               1.059072
3   MSFT  0.035050               0.097854

--- Step 4: Creating dashboard visualizations ---

(Snapshot Ploty.png)

--- Step 5: Airflow DAG Definition (for orchestration) ---

from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.sensors.filesystem import FileSensor
from datetime import datetime, timedelta

# In a real project, these functions would be in separate scripts.
# def process_financial_csvs(): ...
# def load_model_to_warehouse(): ...

default_args = {
    'owner': 'BI_Team',
    'depends_on_past': False,
    'start_date': datetime(2024, 1, 1),
    'email_on_failure': False,
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

with DAG(
    'financial_csv_ingestion_pipeline',
    default_args=default_args,
    description='Daily ETL pipeline for ingesting financial CSV exports.',
    schedule_interval='@daily',
    catchup=False,
    tags=['finance', 'etl', 'csv'],
) as dag:

    # Task 1: Wait for the daily market data CSV to arrive.
    # The path would point to a shared network drive or S3 bucket.
    wait_for_market_data_file = FileSensor(
        task_id='wait_for_market_data_file',
        filepath='/path/to/source/files/market_data_{{ ds_nodash }}.csv',
        poke_interval=300, # Check every 5 minutes
        timeout=60*60*4 # Timeout after 4 hours
    )

    # Task 2: Process all available CSVs, build the model.
    process_files_task = PythonOperator(
        task_id='process_csv_files_and_model',
        python_callable=lambda: print("Processing CSVs and building star schema..."),
        # python_callable=process_financial_csvs
    )
    
    # Task 3: Load the final model into the data warehouse.
    load_warehouse_task = PythonOperator(
        task_id='load_model_to_warehouse',
        python_callable=lambda: print("Loading data into SQLite warehouse..."),
        # python_callable=load_model_to_warehouse
    )
    
    wait_for_market_data_file >> process_files_task >> load_warehouse_task
