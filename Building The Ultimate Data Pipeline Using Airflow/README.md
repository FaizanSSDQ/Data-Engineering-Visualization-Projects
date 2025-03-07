# Building a Data Pipeline for DeFtunes - Data Engineering Project

In this project, I implemented a **data pipeline** using **Apache Airflow** to support **DeFtunes**, a fictitious company in the music industry. DeFtunes offers a subscription-based app for streaming songs and has recently expanded its services to include digital song purchases. With this new retail feature, DeFtunes required a data pipeline to extract purchase data from their API and make it available for downstream systems.

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

As a Data Engineer at DeFtunes, I was tasked with developing a pipeline to extract user and session data from the API. The pipeline was designed to:
1. Extract information about new users for a given date.
2. Retrieve session data for users on a specific date.
3. Fetch detailed information about users within those sessions.

The pipeline was scheduled to run daily, ensuring that the data is always up-to-date for downstream systems.

---

## Key Features

- **API Integration**: Performed API requests to extract user and session data.
- **Daily Scheduling**: Configured the pipeline to run every day for up-to-date data.
- **Error Handling**: Implemented robust error handling to ensure pipeline reliability.
- **Downstream Integration**: Made the extracted data available for analytics and other processes.

---

## Technologies Used

- **Apache Airflow**: For orchestrating the data pipeline.
- **Python**: For scripting the API requests and data processing logic.
- **Docker**: For containerizing the Airflow environment.
- **AWS EC2**: For hosting the Airflow instance.

---


---

## How to Run the Project

### Prerequisites
1. **Apache Airflow**: Ensure Airflow is installed and configured.
2. **Docker**: Install Docker if using a containerized Airflow environment.
3. **AWS EC2**: Set up an EC2 instance for hosting Airflow (if applicable).

### Steps
1. **Set Up Airflow**:
   - Install Airflow and configure connections to the API.
   - Install required libraries using:
     ```bash
     pip install -r requirements.txt
     ```

2. **Upload DAGs**:
   - Place the DAG files (`extract_users.py`, `extract_sessions.py`, `process_data.py`) in the `dags/` directory of your Airflow environment.

3. **Trigger DAGs**:
   - Activate and trigger the DAGs from the Airflow UI.

4. **Monitor and Debug**:
   - Use the Airflow logs to monitor task execution and debug any issues.

---

## Conclusion

This project provided hands-on experience in building and orchestrating data pipelines using Apache Airflow. By extracting user and session data from the API and making it available for downstream systems, I ensured that DeFtunes could effectively support its new retail feature. The pipeline is scalable and can be extended to handle additional data sources or use cases.

---

Feel free to explore the code and adapt it for your own data pipelines!