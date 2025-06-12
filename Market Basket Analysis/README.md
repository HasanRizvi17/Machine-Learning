##  What is Market Basket Analysis (MBA)?
MBA is a technique used to find patterns or associations between items that customers frequently buy together. It helps businesses understand buying behavior and make data-driven decisions like product bundling, and building recommendation systems.
<br>
<br>

## Project Objectives
The following steps are taken in this project:
- Create a synthetic dataset with random transactions and a pool of 19 unique common consumer items
- Explore and understand the data: item frequencies, basket sizes, transaction characteristics (single-item transactions, delivery returns, etc) and temporal trends for seasonality
- Preprocess data into a format suitable for the two algorithms we use -- Apriori and FP-Growth
- Run association rule mining to extract meaningful rules (using both algorithms mentioned above)
<br>

## Dataset Generation
We randomly generate 1,000 transactions which are then stored in CSV file and re-ingested. Each transaction includes 1 to 5 items randomly sampled from a fixed list of 19 products (e.g., milk, bread, butter, etc). While the data is synthetic, it shares similar features and structure as real retail transactions.
<br>
<br>

## Exploratory Data Analysis (EDA)
The analysis includes:
- Item frequency analysis: Count of how often each product/item is ordered in terms of transactions
- Basket size distribution: How many items there are in a single transaction
- Single-item transactions: Important because MBA is not useful if most transactions contain only one item
- Temporal trends: This can be important for understanding seasonality patterns (even though we don't account for them in this project)
- All plots are built using Seaborn and Matplotlib
<br>

## Pre-processing for MBA
Market Basket algorithms require data in a specific structure before it can be mined: one-hot encoded format, where each row is a transaction and each column is a product (True = present, False = not present). We use pd.crosstab() and mlxtend preprocessing functions to achieve this.
<br>
<br>

## Association Rule Mining
We run both algorithms under the same conditions:
- **Apriori**: checks all possible item combinations and keeps the frequent ones, but it scans the data many times and can be slow on large datatsets
- **FP-Growth**: builds a tree of transactions to find frequent itemsets without scanning all combinations, making it much faster for large datasets

We extract frequent itemsets and then generate association rules using the following output metrics:
- **support**: how frequently itemsets are ordered together in transactions
- **confidence**: how often item B is purchased when item A is
- **lift**: how much more likely items are ordered together than by random chance



