## What is K-means Clustering?
K-means clustering is an unsupervised machine learning algorithm for user segmentation that allows us to group users into distinct behavioral personas based on patterns in how they interact with a product. This helps us understand how different types of users behave so that we can move beyond treating everyone the same and instead build targeted strategies for marketing, onboarding, engagement, retention, etc.

In this project, we used K-means clustering to analyze user behavior in a fintech app and identify meaningful personas based on usage, feature adoption, and engagement patterns.
<br>
<br>

## Project Overview
- Engineer a relevant set of behavioral and transactional features
- Explore and preprocess the data before clustering
- Apply K-means and determine the optimal number of clusters
- Analyze each segment to identify patterns and label personas in a digestible way for the business teams
- Translate the labels into actionable insights that can be consumed for product and growth strategies
<br>

## Dataset & Initial Overview
The input data contains 5 CSV files:
| File Name                 | Description                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| `dim_users.csv`          | base user-level data like user_id, signup date, and loan_status, etc     |
| `dim_user_contacts.csv`  | details of contacts added by each user (e.g customers, suppliers, friends, family, etc) |
| `fct_ledger_entries.csv` | records of ledger entries such as credits, debits, and notes added by user (does not represent actual transactions made via the app)  |
| `fct_payment_transactions.csv` | records of _actual_ payment transactions made via the app            |
| `int_events.csv`         | in-app behavioral events for feature usage like screens viewed, buttons clicked, funnels completed etc         |
<br>

## Feature Engineering
We defined several user-level and behavioral features that reflect different types of engagement in the product.
<br>
<br>

## Exploratory Data Analysis (EDA) on the features
Before jumping into the clustering, we explored:
- basic descriptive statistics
- missing values
- distributions of continuous metrics to determine if there were any outliers and skews in the data
- negative-value features
- high correlations between features (e.g are users who onboarded with referrals also more likely to apply for loans?)
- we used seaborn and matplotlib to build the visualizations
<br>

## Data Preprocessing
Once we had a handle on the data, we cleaned and prepped it by:
- removing irrelevant columns (IDs, timestamps)
- replacing missing values with reasonable defaults e.g:
    - replacing NULLs with 0 for users with no contacts/entries/transactions
    - replacing with a large value (e.g., days_since_signup + 1) for days_to_first_transaction/entry/contact
    - filling NULL transaction/entry frequencies with days_since_signup (implies very low frequency) for users who have 0 or 1 entry/transaction
- converting True/False values to numeric (0/1) in boolean fetaures
- for features with outliers -- capping the feature values to 1st and 99th percentile
- for features with skewed distributions -- log transformation to make the data more normally distributed
- for features with negative values -- appyling Yeo-Johnson Power Transformer (from sklearn)
- for highly correlated feature-pairs -- removing features that are correlated in any of the pairs
<br>

## Feature Scaling
Since K-means relies on distance calculations, it’s important that all features are on a similar scale. Otherwise, a high-range feature (like ledger_entry_amount with values ranging from 10 to 100,000) could dominate other low-range features during the clustering process.

We applied `StandardScaler()` from `sklearn.preprocessing` to normalize all numeric features bring them into a 0-1 range. This helped ensure that each feature contributed fairly to the clustering algorithm.
<br>
<br>

## Clustering with K-means
We then applied K-means clustering:
- used 2 the Elbow Method to determine the optimal number of clusters
    - It’s a way to plot the clustering error for different values of K (number of clusters), and find the point where the improvement slows down (rate of decrease in clustering error), which we call the _elbow_
- trained the K-means model on the scaled data with the engineering and preprocessed features
- assigned a cluster label to each user
<br>

## Persona Insights

| Cluster    | Persona Name     | % of Users | Key Behavioral Traits                                       | 
|------------|------------------|-------------|--------------------------------------------------------------|
| 0          | Power Users       | 35%         | - **_Longtime users_**: ~800 days since signup<br>- **_High financial activity_**: High total_ledger_amount, num_transactions, and num_contacts<br>- **_Quick to engage after signup_**: Lower days_to_first_contact/entry/transaction<br>- **_Possibly high value users_**: Very high net_ledger_balance<br>- **_Engaging across multiple app features_**: App breadth score is also highest          | 
| 1          | Low Engaged       | 40%         | - **_Slow to get started_**: Very high days_to_first_contact/entry/transaction<br>- **_Low frequency of trasactions_**: High ledger_entry_frequency_days, low num_contacts/transactions<br>- **_Minimal engagement beyond basic app use-case_**: Lowest app_breadth_score    | 
| 2          | Light Users  | 25%         | - **_Recent users_**: Low days since signup (~200-300)<br>- **_Haven’t done much yet_**: Low total_ledger_amount and num_transactions<br>- **_Lower app_breadth_score (but better than Cluster 1)_** <br>- **_A bit more engaged than Cluster 1, but not nearly as active as Cluster 0_**: Medium values across most metrics<br>- **_Somewhat engaged, but not deep yet_** | 

