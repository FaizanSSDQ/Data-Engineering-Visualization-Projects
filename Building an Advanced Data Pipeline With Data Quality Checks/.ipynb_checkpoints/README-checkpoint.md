# Project: Machine Learning Pipeline for Mobility-As-A-Service Vendors

In this project, I built a **Machine Learning (ML) pipeline** using **Apache Airflow** to support three fictitious Mobility-As-A-Service vendors: **Alitran**, **Easy Destiny**, and **ToMyPlaceAI**. The pipeline preprocesses and validates data, trains a model to estimate ride duration, and evaluates the model to determine if it is suitable for deployment. Continuous training and evaluation allow each vendor to improve their ride duration estimation service.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Key Features](#key-features)
- [Technologies Used](#technologies-used)
- [Project Structure](#project-structure)
- [How to Run the Project](#how-to-run-the-project)
- [Conclusion](#conclusion)

---

## Project Overview

The goal of this project was to create a scalable and maintainable ML pipeline that:
1. **Preprocesses Data**: Cleans and transforms raw data into a format suitable for training.
2. **Trains and Evaluates Models**: Trains an ML model to estimate ride duration and evaluates its performance using metrics like **Mean Absolute Error (MAE)** and **R-squared**.
3. **Determines Deployment**: Uses evaluation metrics to decide whether the model should be deployed or retrained.
4. **Supports Multiple Vendors**: Dynamically generates DAGs for each vendor based on configuration files.

---

## Key Features

- **Taskflow API**: Used to implement the DAG in a clean and modular way.
- **Great Expectations**: Integrated for data quality checks to ensure the dataset meets required standards.
- **Branching Logic**: Employed the `BranchPythonOperator` to decide whether to deploy or retrain the model based on evaluation metrics.
- **Dynamic DAGs**: Created multiple DAGs dynamically using configuration files for each vendor.
- **Scalable Design**: Designed the pipeline to support additional vendors or adapt to other ML use cases.

---

## Technologies Used

- **Apache Airflow**: For orchestrating the ML pipeline.
- **Great Expectations**: For data validation and quality checks.
- **scikit-learn**: For training and evaluating the ML model.
- **Python**: For scripting the pipeline and preprocessing logic.
- **Amazon S3**: For storing processed data and model artifacts.
- **Docker**: For containerizing the Airflow environment (if applicable).

---


---

## How to Run the Project

### Prerequisites
1. **Apache Airflow**: Ensure Airflow is installed and configured.
2. **Great Expectations**: Install the library for data validation.
3. **scikit-learn**: Install for model training and evaluation.
4. **Amazon S3**: Set up an S3 bucket for storing processed data and model artifacts.

### Steps
1. **Set Up Airflow**:
   - Install Airflow and configure connections to your data sources.
   - Install required libraries using:
     ```bash
     pip install -r requirements.txt
     ```

2. **Upload DAGs**:
   - Generate dynamic DAGs using the template and configuration files.
   - Upload the DAGs to the Airflow environment.

3. **Trigger DAGs**:
   - Activate and trigger the DAGs from the Airflow UI.

4. **Monitor and Debug**:
   - Use the Airflow logs to monitor task execution and debug any issues.

---

## Conclusion

This project provided hands-on experience in building a machine learning pipeline using Apache Airflow. By implementing data quality checks, training and evaluating models, and dynamically generating DAGs, I created a scalable and maintainable solution for ride duration estimation. This pipeline can be extended to support additional vendors or adapted for other machine learning use cases.

---

Feel free to explore the code and adapt it for your own machine learning pipelines!
