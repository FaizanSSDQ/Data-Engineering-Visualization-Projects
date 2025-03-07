# Project: Building Data Pipelines with Apache Airflow

In this project, I designed and implemented data pipelines using **Apache Airflow** to extract, transform, and load (ETL) data from an RDS database into a storage service. The goal was to apply best practices in orchestrating data pipelines, ensuring they are deterministic, idempotent, and scalable. By the end of this project, I achieved the following:

- Implemented variables and templating in Airflow DAGs to follow best practices.
- Integrated cross-communication between Airflow tasks using **XCOMs**.
- Organized Airflow tasks into **Task Groups** to improve DAG readability and monitoring.

---

## Table of Contents
- [1 - Best Practices in Writing DAGs](#1)
  - [1.1 - Determinism and Idempotence](#1-1)
  - [1.2 - Built-in Variables and Templating](#1-2)
  - [1.3 - User-Created Variables](#1-3)
  - [1.4 - XCOMs](#1-4)
  - [1.5 - Task Groups](#1-5)
- [2 - Exploring Airflow's Components and Project Resources](#2)
- [3 - Building a Simple DAG](#3)
  - [3.1 - DAG Structure](#3-1)
  - [3.2 - Preparing the Python Code](#3-2)
    - [Task 1: Define DAG Parameters](#ex01)
    - [Task 2: Use Templating for S3 Partitioning](#ex02)
    - [Task 3: Create Airflow Connections and Variables](#ex03)
    - [Task 4: Implement Data Transformation](#ex04)
    - [Task 5: Set Up Task Dependencies](#ex05)
- [4 - Running the DAGs with Airflow](#4)
- [5 - Building a Grouped Tasks DAG](#5)
  - [5.1 - DAG Structure](#5-1)
  - [5.2 - Preparing the Python Code](#5-2)
    - [Task 6: Define Task Groups](#ex06)
    - [Task 7: Set Up Task Dependencies](#ex07)
- [6 - Optional Material](#6)

---

## 1 - Best Practices in Writing DAGs

In this section, I reviewed and applied best practices for writing Airflow DAGs. These practices ensure that the pipelines are reproducible, efficient, and reliable.

### 1.1 - Determinism and Idempotence
Determinism and idempotence are foundational concepts in data engineering. **Determinism** means that the same input will always produce the same output. **Idempotence** means that executing the same operation multiple times will yield the same result. To achieve this in Airflow, I:
- Defined a static `start_date` for the DAG to avoid missing runs.
- Set the `catchup` parameter to `False` to have more control over DAG execution.
- Used parameterized operators to ensure consistent behavior across runs.

### 1.2 - Built-in Variables and Templating
I used built-in Airflow variables like `{{ ds }}` to retrieve the DAG run’s logical date in the `YYYY-MM-DD` format. This ensured that my DAG runs were deterministic and could dynamically adapt to different execution dates. I also leveraged **Jinja Templating** to avoid hardcoding values in my DAGs.

### 1.3 - User-Created Variables
To avoid hardcoding values and follow the **DRY (Don’t Repeat Yourself)** principle, I used user-created variables in Airflow. These variables allowed me to store and retrieve key-value pairs dynamically.

### 1.4 - XCOMs
XCOMs enabled me to share small pieces of data between tasks. For example, I used `xcom_push` to store the number of rows processed in a transformation task and `xcom_pull` to retrieve it in a notification task.

### 1.5 - Task Groups
I grouped tasks using **Task Groups** to organize my DAGs and make them more readable. Inside a Task Group, I defined tasks and their dependencies using the bit-shift operators `<<` and `>>`.

---

## 2 - Exploring Airflow's Components and Project Resources

For this project, I was provided with a MySQL database (`classicmodels`) hosted on Amazon RDS. I interacted with four tables: `orders`, `customers`, `payments`, and `products` to create two DAGs. The Airflow environment was dockerized and running on an EC2 instance. I interacted with the Airflow UI and an S3 bucket for storing DAG files.

---

## 3 - Building a Simple DAG

I built a simple DAG to process the `orders` table. The DAG consisted of the following tasks:
1. **Extract and Load**: Extracted data from the `orders` table and loaded it into an S3 bucket.
2. **Transform**: Cleaned the data by dropping nulls and duplicates.
3. **Notification**: Emulated a notification task to print the number of valid records.
4. **End**: Marked the end of the DAG.

---

## 4 - Running the DAGs with Airflow

After completing the `simple_dag.py` file, I uploaded it to the **_DAGs Bucket_** using the following command:

```bash
aws s3 sync src s3://<DAGS-BUCKET>/dags
```

Once the DAG was recognized in the Airflow UI, I activated it and triggered it manually. If any tasks failed, I used the logs to identify and fix the issues. After successful runs, I verified the output in the **_Raw Data Bucket_**.

## 5 - Building a Grouped Tasks DAG

I built a second DAG to process multiple tables (`customers`, `payments`, and `products`). This DAG used **Task Groups** to organize tasks for each table. The structure included:

1. **Extract and Load**: Extracted data from each table and loaded it into S3.
2. **Transform**: Cleaned the data by dropping nulls and duplicates.
3. **Notification**: Printed the number of valid records for each table.
4. **End**: Marked the end of the DAG.

## 6 - Optional Material

For additional exploration, I reviewed the structure of the `classicmodels` database and connected to it using the MySQL command-line interface. This helped me understand the data and verify its integrity before building the pipelines.

## Conclusion

This project provided hands-on experience with Apache Airflow and demonstrated the importance of best practices in building robust and scalable data pipelines. By applying concepts like determinism, idempotence, templating, and task grouping, I created efficient and maintainable workflows for ETL processes.

## How to Run the Project

1. **Set Up Airflow**: Ensure Airflow is installed and configured. Use the provided dockerized environment if available.
2. **Upload DAGs**: Upload the `simple_dag.py` and `grouped_task_dag.py` files to the **_DAGs Bucket_**.
3. **Trigger DAGs**: Activate and trigger the DAGs from the Airflow UI.
4. **Monitor and Debug**: Use the Airflow logs to monitor task execution and debug any issues.