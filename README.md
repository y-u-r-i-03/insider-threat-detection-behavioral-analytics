# Insider Threat Detection with Behavioral Analytics

## Project Overview

This project implements and compares two primary approaches for detecting insider threats based on user logon activity: a **rule-based detection system** and an **unsupervised machine learning anomaly detection model (Isolation Forest)**. The goal is to identify anomalous user behaviors that could indicate malicious activity, using the CERT Insider Threat Dataset (r1).

## Data Source

-   **Dataset**: CERT Insider Threat Dataset, Release 1 (r1.tar.bz2)
-   **Key Data**: `logon.csv` containing user logon/logoff events.

## ETL (Extract, Transform, Load) Pipeline

1.  **Extract**: Loads raw logon data from `logon.csv`, performing initial date parsing.
2.  **Transform**: Cleans data by removing duplicates and critical nulls. It also enriches the data by adding time-based features (hour, day of week, month, off-hours flag, weekend flag).
3.  **Feature Engineering**: Aggregates the cleaned logon data into user-centric behavioral features, such as `total_events`, `total_logins`, `off_hours_logins`, `unique_pcs`, `unique_days`, `off_hours_ratio`, and `avg_daily_logins`.

## Rule-Based Detection

A set of three distinct rules is applied to the user behavioral features to flag suspicious activity:

1.  **Off-Hours Login Detection**: Flags users with an excessive number of logins during non-business hours (before 7 AM or after 8 PM).
2.  **Multi-PC Access Detection**: Flags users who access an unusually high number of unique personal computers, which can indicate lateral movement.
3.  **Abnormal Session Frequency**: Flags users with an abnormally high average daily login count, determined dynamically based on the dataset's mean and standard deviation.

## Machine Learning Detection (Isolation Forest)

An Isolation Forest model is used for unsupervised anomaly detection. This algorithm is particularly effective for identifying outliers in high-dimensional datasets. It flags users whose behavioral patterns deviate significantly from the norm, based on features such as `total_logins`, `off_hours_logins`, `unique_pcs`, `unique_days`, and `off_hours_ratio`.

## Performance Evaluation

To quantitatively compare the detection methods, a small set of 10 users was designated as simulated malicious insiders (ground truth). The performance is evaluated using:

-   **True Positive Rate (TPR / Recall)**: The proportion of actual malicious users correctly identified.
-   **False Positive Rate (FPR)**: The proportion of benign users incorrectly flagged as malicious.

### Key Findings

-   **Rule-Based DLP**: Achieves a perfect 100% TPR (meaning no malicious users are missed) but comes with a high FPR (e.g., 36.26%), leading to many false alarms.
-   **Isolation Forest ML**: Also achieves a 100% TPR while significantly reducing the FPR (e.g., 4.04%), indicating a more precise detection with fewer false positives.

### Detection Overlap

Comparison of flagged users reveals three groups:

-   **Rules Only**: Users caught by rules but not ML.
-   **ML Only**: Users caught by ML but not rules.
-   **Both**: Users flagged by both methods, representing the highest confidence suspects. This overlap helps prioritize investigations.

## Scalability Considerations

The project also includes a performance report on the ETL pipeline's execution time and memory footprint. It projects scalability for 10x and 100x larger datasets, highlighting that for enterprise-level data volumes, a distributed framework like Apache Spark or Dask would be necessary.
