# Finance - Investment-Portfolio-Risk-Opportunity-Dashboard
## This project builds an institutional-grade data pipeline and a dashboard prototype for stock portfolio analysis. The pipeline integrates price, fundamental, and market sentiment data, modeling it into a star schema for optimal analytical performance. The resulting analysis calculates key metrics like Beta and Sharpe Ratio, allowing portfolio managers to make faster, more informed investment decisions by visualizing the risk-return trade-off across their entire portfolio

**Live Demo:** [https://colab.research.google.com/drive/1hloi9T6IEWTsDlds8iVB9jDuLp_HdMbR?usp=sharing]

## Project Overview

This project simulates a real-world Business Intelligence scenario at an investment fund. It involves building an end-to-end ETL pipeline that ingests daily data from multiple CSV exports, models it into a robust Star Schema, and generates a dashboard prototype for portfolio managers to analyze risk and performance. The primary goal is to transform raw, disconnected data files into a unified, actionable source of truth for strategic decision-making.

## Business Problem

The portfolio management team at an investment fund receives daily and quarterly data exports from different systems:
1.  **Market Data Provider:** A daily CSV with stock prices and volume.
2.  **Fundamental Analysis Team:** A quarterly CSV with metrics like P/E Ratio and Market Cap.
3.  **News Monitoring Service:** A daily CSV with sentiment scores for relevant news headlines.

Currently, analysts spend hours manually combining these files in Excel, a process that is slow, error-prone, and lacks scalability. They need an automated pipeline to create a single, reliable view of their tech stock portfolio, allowing them to quickly assess the risk-return profile of each asset.

## Solution Architecture

The solution is a Python-based ETL pipeline designed to be orchestrated by a tool like Apache Airflow. The data flows as follows:

## Data Model (Star Schema)

To ensure analytical performance and clarity, the data is modeled into a Star Schema, a core concept in Business Intelligence and Data Warehousing.

*   **`F_DailyPerformance` (Fact Table):** Contains the core quantitative measurements or "facts" of our business process. Each row represents a stock's performance on a given day.
    *   `DateKey` (FK)
    *   `StockKey` (FK)
    *   `ClosePrice`
    *   `Volume`
    *   `Return`
    *   `SentimentScore`

*   **`D_Stock` (Dimension Table):** Describes the stocks. It contains attributes used for filtering and grouping.
    *   `StockKey` (PK)
    *   `Symbol`
    *   `CompanyName`
    *   `Sector`
    *   `PERatio`
    *   `MarketCapB`

*   **`D_Date` (Dimension Table):** Describes the time dimension, allowing for easy time-based analysis (e.g., by quarter, month).
    *   `DateKey` (PK)
    *   `FullDate`
    *   `Year`
    *   `Quarter`
    *   `Month`

This model separates what happened (facts) from who/what/when (dimensions), making queries faster and more intuitive.

## Key Skills & Technologies Demonstrated

*   **Data Ingestion & ETL:** Processing, cleaning, and integrating multiple CSV files using **Pandas**.
*   **Data Modeling:** Designing and implementing a Star Schema for BI analytics.
*   **Database Management:** Loading data into and querying a **SQLite** database.
*   **Advanced SQL:** Using JOINs to query across the data model.
*   **Advanced Analytics:** Calculating key financial metrics like **Beta** (market risk) and **Sharpe Ratio** (risk-adjusted return) using **Numpy** and **Pandas**.
*   **Data Visualization:** Creating an interactive, insightful dashboard prototype with **Plotly Express**.
*   **Pipeline Orchestration:** Designing a conceptual **Apache Airflow** DAG to automate the entire file-based workflow.

## Key Insights & Visualizations

### Risk-Return Bubble Chart

This chart is the centerpiece of the analysis, providing a complete portfolio overview in a single view.

(Investment-Portfolio-Risk-Opportunity-Dashboard/SnapshotPloty.png)

**Insights:**

*   **Top-Left Quadrant (Ideal):** Stocks in this area (e.g., MSFT in the sample data) offer high risk-adjusted returns (high Sharpe Ratio) with lower-than-market volatility (Beta < 1). These are strong performers.
*   **Top-Right Quadrant:** Stocks here (e.g., AMZN) provide high returns but come with higher-than-market risk. These are aggressive growth assets.
*   **Bottom-Left Quadrant:** Stocks with low risk and low return. They are stable but may underperform.
*   **The bubble size**, representing market capitalization, provides crucial context about the company's scale.

This visualization allows a portfolio manager to instantly identify leaders, laggards, and the risk profile of each holding relative to the others and the market benchmark (SPY).

## How to Run the Project

1.  Clone this repository to your local machine.
2.  Open the `financial_portfolio_analysis.ipynb` file in Google Colab or a local Jupyter environment.
3.  The notebook is self-contained; it generates its own sample data, so no external files are needed.
4.  Run all cells sequentially to execute the full ETL pipeline and generate the visualizations.

## Simulated Airflow DAG

The project includes a Python script defining a conceptual Apache Airflow DAG. This demonstrates how the notebook's logic would be productionized and automated. The DAG is designed to:

1.  Use a `FileSensor` to wait for a new market data CSV to be dropped in a specific directory each day.
2.  Once the file arrives, trigger the main Python script to process all new CSVs.
3.  Load the transformed data into the data warehouse, making it ready for BI tools and analysts.
