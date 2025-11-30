📘 Customer Purchase Analytics & 30-Day Repurchase Prediction

A complete pipeline from raw transactional data → clean customer base → engineered dataset → machine learning prediction

🧭 Project Overview

This project transforms a noisy retail transaction dataset into a fully structured analytical base suitable for customer analytics, LTV modeling, segmentation (RFM), and predictive modeling.

The final goal of the modeling phase is to predict whether a customer will make another purchase within 30 days after their most recent transaction.

The project includes two major phases:

Data Cleaning & Transaction-Level Preprocessing

Feature Engineering & Repurchase Prediction Modeling

-------------------------------------------------------
🧹 Phase 1 — Data Cleaning & Transaction Preprocessing
-------------------------------------------------------
🎯 Objective

The raw transactional dataset contained issues such as:

Missing CustomerIDs

Test/system transactions

Negative quantities not representing real returns

Invalid or unmatched returns

Missing historical purchases

Service charges and discounts that distorted sales values

The goal was to clean and stabilize the dataset before any type of modeling.

✅ 1. Removing transactions without CustomerID

Rows without CustomerID cannot be used for customer-based analytics or modeling.
✔️ All invoices lacking CustomerID were removed.

✅ 2. Detecting and removing test customers

Negative quantities often included suspicious keywords:

manual, test, sample, ?, adjustment


These rows indicated test/system behavior.

✔️ All customers whose transactions contained these patterns were removed.

✅ 3. Correcting returns (negative quantities)

A detailed return-matching algorithm was implemented:

Negative quantity = return

Returns were matched backwards to previous purchases of the same item for the same customer

No return was assigned across customers or unrelated purchases

✔️ All valid returns were reconciled with prior purchases
✔️ Irreconcilable returns were removed

✅ 4. Removing returns before the first purchase

Some customers had returns before any positive purchase — meaning missing historical data.
✔️ All negative quantities before the first positive purchase were removed.

✅ 5. Removing POSTAGE, CARRIAGE and DISCOUNT rows

StockCodes:

POST, C2, D


These were always negative (charges/discounts).

Their total financial impact:
-3839.59
≈ 0.046% of total sales → negligible

✔️ All such rows were removed completely.

📌 Phase 1 Outcome

After all cleaning steps:

All invalid returns fixed

All test/system customers removed

All ID-less rows discarded

Missing return history issues resolved

Minor service/discount artifacts removed

Dataset is now consistent, balanced, and trustworthy

✔️ Final dataset ready for LTV, RFM, churn, basket analysis and predictive modeling.

-------------------------------------------------------
🤖 Phase 2 — Feature Engineering & Repurchase Prediction
-------------------------------------------------------
🎯 Objective

Predict whether a customer will purchase again within 30 days after their most recent transaction.

We transformed the dataset from invoice-level to customer-level, creating aggregated features for each customer.

🧪 Feature Engineering

A function was built to convert full transaction history into a customer-level analytical dataset.

✔️ Key engineered features:

Total purchase amount (price × quantity)

Number of purchases

Mean / median / min / max purchase amount

Unique items purchased

Recency (days since last purchase minus last transaction to avoid leakage)

Tenure

Min/max interpurchase intervals

Rolling 3-purchase average amount (excluding last actual purchase)

Purchase trend slope

Top month of activity

Share of top item

High-value purchase ratio

Purchase frequency per year

🛡️ Data leakage prevention:
All features were calculated excluding the final transaction, because the label depends on what happens after that last purchase.

🏷️ Label Definition

For each customer:

anchor_date = date of second-last purchase  
next_date   = date of last purchase  

label = 1 if (next_date − anchor_date) <= 30 days  
else 0


In simple terms:
Did the customer return within 30 days after their previous purchase?

🔧 Model

A XGBoost Classifier was trained.

🎯 Performance

Train accuracy: 84%

Test accuracy: 80%

🧾 Classification Report
               precision    recall  f1-score   support
0.0               0.75      0.91      0.82       121
1.0               0.88      0.69      0.77       118
accuracy                              0.80       239

🌟 Feature Importance (Top Predictors)
Feature	Importance
recency_days_prior	0.4158
tenure_days_prior	0.0886
total_quantity_prior	0.0511
min_interpurchase_days	0.0472
max_interpurchase_days	0.0464
last_3_avg_amount_prior	0.0457

🔍 Interpretation

Recency was by far the strongest predictor — consistent with typical purchasing behavior in transactional retail.

🎉 Final Notes

This project provides a full, end-to-end customer analytics pipeline:

✔️ Deep transactional cleaning
✔️ Robust feature engineering
✔️ Data leakage–safe pipeline
✔️ Repurchase prediction using XGBoost
✔️ Strong accuracy and well-balanced metrics
