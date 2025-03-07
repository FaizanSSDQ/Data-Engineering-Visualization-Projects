# AWS Infrastructure Monitoring with CloudWatch and SNS

This project focuses on implementing robust monitoring and alerting for an AWS infrastructure involving a bastion host and an RDS database. By leveraging Amazon CloudWatch and SNS, we ensure the reliability and availability of critical resources.

## Project Description

The project addresses the need for proactive infrastructure monitoring by setting up CloudWatch alarms to detect potential failures in the bastion host. When an alarm is triggered, an SNS topic sends an email notification, enabling rapid response and recovery. This project also demonstrates the use of `sysbench` for performance testing and the enabling of RDS Performance Insights for in-depth database monitoring.

## Key Features

* **Bastion Host Monitoring:** Implementation of CloudWatch alarms to monitor the health of the bastion host.
* **Real-time Alerts:** Configuration of an SNS topic and subscription for immediate email notifications upon alarm triggers.
* **RDS Performance Insights:** Enabling and utilizing RDS Performance Insights for detailed database monitoring.
* **Performance Testing:** Usage of `sysbench` for stress testing and performance evaluation.
* **Infrastructure as Code (IaC):** Utilizes Terraform for infrastructure provisioning and management.

## Getting Started

1.  **Prerequisites:**
    * An AWS account with appropriate permissions.
    * Terraform installed.
    * AWS CLI configured.

2.  **Clone the Repository:**

    ```bash
    git clone [repository URL]
    cd [repository directory]
    ```

3.  **Initialize Terraform:**

    ```bash
    terraform init
    ```

4.  **Apply Terraform Configuration:**

    ```bash
    terraform apply
    ```

    * Confirm the deployment by typing `yes`.

5.  **Connect to Bastion Host:**
    * Navigate to the EC2 console.
    * Find the bastion host `de-c2w3lab2-bastion-host`.
    * Connect via SSH.

6.  **Run Sysbench Tests:**
    * Use the provided sysbench commands on the bastion host terminal to generate load and monitor metrics.

7.  **Monitor CloudWatch and SNS:**
    * Observe CloudWatch metrics and alarms.
    * Verify email notifications from SNS.
    * To see the rds performance insights, navigate to the RDS dashboard, and then to the database instance.

## Architecture

The architecture consists of:

* A bastion host for secure access to the private network.
* An RDS database instance with Performance Insights enabled.
* CloudWatch alarms to monitor the bastion host.
* An SNS topic and subscription for email notifications.
* Terraform files for infrastructure creation.

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for any improvements or bug fixes.

## License

[This repository is licensed under the MIT License - see the LICENSE file for details]


## Conclusion

This project successfully demonstrates the implementation of robust monitoring and alerting mechanisms for a critical AWS infrastructure. By leveraging CloudWatch, SNS, and best practices in infrastructure management, we have established a proactive approach to ensuring the availability and reliability of our systems. The insights gained from this project, including the use of `sysbench` for performance testing and RDS Performance Insights for database monitoring, can be applied to other AWS environments, enhancing overall operational efficiency and minimizing downtime. This project not only reinforces the importance of proactive monitoring but also provides a practical guide for setting up similar systems in production environments.