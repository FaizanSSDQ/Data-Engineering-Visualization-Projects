# Implementing Monitoring with Amazon CloudWatch

In this project, I utilized Amazon CloudWatch tools to monitor a bastion host architecture. The goal was to ensure real-time observability, track system performance, and identify potential security threats within the infrastructure. By leveraging CloudWatch, I collected and analyzed logs, set up alarms for critical metrics, and gained valuable insights into the operational health of the bastion host.

To open the solution files, follow these steps:

- Go to the main menu and select `File -> Preferences -> Settings`.
- Click on `Text Editor` on the left, then scroll down to the `Files: Exclude` section.
- Remove the line `**/terraform_solution/**`. The folder will now appear in the explorer.
- You can close the `Settings` tab.

# Table of Contents
# Table of Contents
- [ 1 - Introduction](#1)
- [ 2 - Infrastructure deployment using Terraform](#2)
- [ 3 - Test the Infrastructure](#3)
- [ 4 - CloudWatch Logs and Metrics](#4)
  - [ 4.1 - CloudWatch Showcase](#4-1)
  - [ 4.2 - Monitoring your Metrics](#4-2)
- [ 5 - Monitoring your Architecture with CloudWatch Alarms](#5)
  - [ 5.1 - Creating the Alarms](#5-1)
  - [ 5.2 - Creating New Alerts for RDS](#5-2)
  - [ 5.3 - Trigger the Alarm](#5-3)
- [ 6 - Tear Down your Infrastructure](#6)

<div id='1'/>

## 1 - Introduction

In this project, I focused on enhancing the reliability and observability of our database infrastructure, which is deployed behind a bastion host. The bastion host, acting as a crucial security gateway, allows us to securely manage our database instances within a private network. Recognizing the critical nature of this setup, our leadership team requested the implementation of robust monitoring and alerting mechanisms. Specifically, they wanted to ensure we could promptly detect and respond to any potential failures of the bastion host.

To address this requirement, I leveraged Amazon CloudWatch, a powerful monitoring and observability service provided by AWS. My objective was to gain a deeper understanding of CloudWatch's capabilities and how to effectively apply them to our architecture. This involved exploring various CloudWatch features, such as metric collection, custom dashboards, and alarm configurations. By implementing comprehensive monitoring, I aimed to establish a proactive approach to infrastructure management, ensuring the continuous availability and performance of our database environment. This project not only reinforced the security of our infrastructure but also provided us with vital insights into the health and operational status of our bastion host.

The infrastructure consists of the following components:

- `VPC`: A Virtual Private Cloud
in which your solution will reside.
- `EC2`: A virtual instance that will work as a bastion host on a 
public subnet.
- `RDS`: A relational database instance that will reside in a private subnet group.
- `Public Subnet`: Subnet inside the VPC with internet access.
- `Private Subnets`: Subnets inside the VPC without internet access.

<div id='2'/>

## 2 - Infrastructure deployment using Terraform

The first step in this project will be setting up the infrastructure using 
Terraform. In your terminal, run the following command:

```bash
source scripts/setup.sh
```

Change the working directory to the `terraform` folder:

```bash
cd terraform
```

and then, run the initialization command:

```bash
terraform init
```

Execute the following command to generate the execution plan:

```bash
terraform plan
```

To deploy infrastructure, run the command

```bash
terraform apply
```

To initiate the deployment of the necessary AWS resources, I executed the command and confirmed with `yes`. This provisioning process, which typically takes 7-10 minutes, sets up the infrastructure required for our monitoring project.

This infrastructure is largely consistent with previous deployments, ensuring familiarity and ease of use. However, I implemented two key enhancements to facilitate our monitoring objectives. First, the bastion host's user data was modified to install essential monitoring packages. This ensures the host is equipped to provide the necessary metrics to CloudWatch.

Second, I enabled Performance Insights for the RDS database instance. To verify this configuration, I examined the `terraform/modules/bastion_host/rds.tf` file. Within the `resource "aws_db_instance" "database"` block, I confirmed that the `performance_insights_enabled` key was set to `true`. This crucial setting allows us to track a wide range of performance metrics for our RDS instance, providing deep visibility into its operational health and enabling proactive performance management. By implementing these changes, I've laid the groundwork for effective monitoring and alerting of our database infrastructure.
<div id='3'/>

## 3 - Test the Infrastructure

Once the infrastructure has been deployed successfully, you will connect to 
the database using the bastion host. You can get the database endpoint from the 
previous output or running the command:

```bash
terraform output db_host
```

This command will output the password (all of the outputs are in double quotes):

```bash
terraform output db_master_password
```

To test the infrastructure, connect to the bastion host using EC2 Connect. Run the following command to get the link to the AWS console:

```bash
cat ~/.aws/aws_console_url
```

Open the link in the new browser window.

*Note*: For security reasons, the URL to access the AWS console will expire every 15 minutes, 
but any AWS resources you created will remain available for the 2 hour period. 
If you need to access the console after 15 minutes, please rerun the command to obtain a new active link.

*Note:* If you see the window like in the following printscreen, click on **logout** link, 
close the window and click on console link again.

![AWSLogout](images/AWSLogout.png)

In the AWS console and search for **EC2**. In the left panel, click 
on **Instances**. Search for your bastion host instance `de-c2w3lab2-bastion-host` 
and click on its ID. Then, click on **Connect**.

![EC2_connect](./images/EC2_connect.png)

![Connect_to_instance](./images/Connect_to_instance.png)

You will be prompted to the terminal to interact with your bastion host. With the terminal open, you can access the RDS by using this command:

```bash
psql -h <RDS-HOST> -U postgres_admin -p 5432 -d postgres --password
```

Replace the placeholder `<RDS-HOST>` with the output from the command `terraform output db_host`, you will be prompted to write down the database password
which is an output from the command `terraform output db_master_password` 
(do not include the double quotes!). Then, execute the following command:

```sql
CREATE DATABASE sbtest;
```

You will use this database later so do not log out or do not close this terminal.

To exit the attempt either press `Ctrl+S` (it also might be `Cmd+S`, `Ctrl+Q` or 
`Ctrl+C` or typing `quit` and pressing Enter).

<div id='4'/>

## 4 - CloudWatch Logs and Metrics

Amazon CloudWatch is a comprehensive monitoring and observability service. It 
provides a unified view of operational health and performance across 
AWS resources and applications enable effective monitoring, logging, and alerting. 
To showcase the Cloudwatch capabilities, you are going to create a Cloudwatch 
dashboard to show some metrics from the deployed RDS. 

<div id='4-1'/>

### 4.1 - CloudWatch Showcase

Open a new terminal and go back to the terraform folder:

```bash
cd ~/project/terraform
```

Open the `main.tf` file from the `terraform` folder. Uncomment the module named 
`monitoring` (lines 12 to 19). Save changes to the file.

Then, in the `terraform/modules/monitoring` folder, open the `cloudwatch.tf` file and 
uncomment only the resource `"aws_cloudwatch_dashboard" "rds_dashboard"` (lines
1 to 94). You can use hotkeys `Ctrl+/` or `Cmd+/`. Save changes to the file.

In this resource, there is a key named `dashboard_body`; 
this is where you define the position, size and content of the dashboard elements. 
Inspect this key, you will find some widgets of text type, 
like the title of the dashboard, and other widgets of metric type. Between the 
metrics that you are going to monitor you can find the CPU Utilization, Free 
Storage Space, Read/Write IOPS and the number of Database Connections in your RDS. 
Those metrics are shown as an average over a window of 30 seconds.
In your terminal, deploy your new resources with the following commands:

```bash
terraform init
terraform plan
terraform apply
```

Remember to write `yes` to apply the changes. 

Go back to the AWS console. Now, search for **Cloudwatch**, and click on **Dashboards** button in the 
left panel. You will find the dashboard that you just deployed.

![Cloudwatch_dashboard](./images/Cloudwatch_dashboards.png)


Open your dashboard and you will see the four metrics that were created.

![RDS_dashboard](./images/RDS_dashboard.png)


You can export those metrics to Cloudwatch and add those metrics to the dashboard. 
You can find more information on this 
[AWS video tutorial](https://www.youtube.com/watch?v=YLIzUrXRzd0).

<div id='4-2'/>

### 4.2 - Monitoring your Metrics

To effectively monitor our database's performance metrics, I decided to utilize `sysbench`, a powerful open-source benchmarking tool. Sysbench is ideal for stress testing and performance tuning, allowing us to evaluate various system components, including databases like PostgreSQL. It's particularly valuable for simulating realistic workloads and understanding how our infrastructure responds under pressure. As `sysbench` was pre-installed on our bastion host, it provided a convenient and efficient way to conduct these tests.

To begin, I navigated to the AWS Management Console and accessed the EC2 service. Within the EC2 dashboard, I located the bastion host instance, identified by the name `de-c2w3lab2-bastion-host`, and connected to it via SSH. This allowed me to directly interact with the bastion host's terminal.

To initiate our performance testing, I prepared to execute a series of `sysbench` commands. The first step involved configuring the command with the correct placeholders, ensuring accurate targeting of our PostgreSQL database instance. This setup would allow us to generate a controlled workload, enabling us to observe the corresponding metrics within CloudWatch and Performance Insights.

```bash
sysbench /usr/share/sysbench/oltp_insert.lua \
  --db-driver=pgsql \
  --table-size=1000000 \
  --tables=15 \
  --threads=1 \
  --pgsql-host=<RDS-HOST> \
  --pgsql-port=5432 \
  --pgsql-user=postgres_admin \
  --pgsql-password=<DB-PASSWORD> \
  prepare
```

Let's understand what this command does and the options used:
- `--table-size=1000000`: Sets the size of each OLTP (Online Transaction Processing) 
    table to 1.000.000 rows. This defines how many rows will be inserted into each 
    table during the preparation phase.
- `--tables=15`: Specifies the number of OLTP tables to be created. 
    In this case, 15 tables will be created.
- `--threads=1:` Defines the number of threads to use. Here, only one thread is being used.
- `/usr/share/sysbench/oltp_insert.lua`: This is 
the path to the Lua script that Sysbench will execute. The `oltp_insert.lua` 
script is used to prepare the database by creating the necessary tables and populating them with data.
`prepare`: The action to perform. In this case, it tells Sysbench to prepare the database.

This command connects to a PostgreSQL Database, and executes the `oltp_insert.lua` 
to prepare the database by creating 15 OLTP tables, each one with one million rows.

Execute the previous command and go back to your Cloudwatch Dashboard to monitor 
the state changes of your defined metrics. 

*Note*: To paste copied text in Ubuntu terminal, you may need to use `Shift + Ctrl + V` or `Shift + Cmd + V`.

You should see a peak in the CPU Utilization and Read/Write IOPs charts, while the 
Free Storage Space will start decreasing. This setup can take between 5 to 7 minutes. 

![!Sysbench_test1](./images/Sysbench_test1.png)

<div id='5'/>

## 5 - Monitoring your Architecture with CloudWatch Alarms

<div id='5-1'/>

### 5.1 - Creating the Alarms

Now, open the `terraform/modules/monitoring/cloudwatch.tf` file. 
Uncomment the resources associated with the **Alerts** (lines 97 to 123). Save
changes. 

This is the description of the recourses:
- `aws_cloudwatch_metric_alarm`: This resource creates a CloudWatch alarm that 
    monitors the status of a bastion host instance in EC2.
- `aws_sns_topic`: This resource creates an SNS topic to send notifications 
    when the CloudWatch alarm is triggered.
- `aws_sns_topic_subscription`: This resource creates a subscription to the SNS 
    topic, allowing notifications to be sent to an email address.

Make sure to only uncomment those 3 resources; the RDS Alert must remain 
commented as you will work on that later.

Open the `terraform/variables.tf` file and search for the variable 
named `notification_email`. In the default key, write down your e-mail replacing
the placeholder `<EMAIL-FOR-MONITORING>`. 
This e-mail will be used to send you notifications and alerts.

Then, deploy the new infrastructure:

```bash
terraform plan
terraform apply
```

Once those resources have been deployed, a subscription should be sent to 
the e-mail that you added as the notification mail.

![Subscription_confirmation](./images/Subscription_confirmation.png)

![Subscription_confirmed](./images/Subscription_confirmed.png)

Maintaining the reliability of our infrastructure is paramount. To achieve this, I implemented comprehensive monitoring practices, enabling early detection of failures and ensuring swift recovery from unforeseen issues. In this project, I utilized the `monitoring` module to create a CloudWatch alarm specifically designed to monitor the health of our bastion host.

Furthermore, I configured an SNS (Simple Notification Service) topic and subscription to facilitate real-time alerts. This setup ensures that in the event the CloudWatch alarm is triggered, indicating a potential issue with the bastion host, an email notification is immediately dispatched. The SNS subscription, which I previously accepted, is linked to this topic, guaranteeing that I receive timely alerts whenever the bastion host's health deteriorates. This proactive approach to monitoring and alerting is crucial for maintaining the uptime and stability of our critical infrastructure.

<div id='5-2'/>

### 5.2 - Creating New Alerts for RDS

Now, you are going to create a new alert for your RDS instance. Open 
the `terraform/modules/monitoring/cloudwatch.tf` file, uncomment the last section
(lines 125 to 142) and complete the code associated with the resource `RdCpuUsage`: 

- Set the number of evaluation periods to 2.
- You will check the CPU Utilization. The associated metric name is `"CPUUtilization"`.
- Set the period to `"60"` seconds.
- Your CPU utilization threshold is 20%. Set the `threshold` key to "20".

Don't forget to save the changes. Then, deploy your new resources with:

```bash
terraform plan
terraform apply
```

Connect again to your bastion host to create a new sysbench test with the following command. You will 
need to replace the placeholders `<RDS-HOST>` and `<DB-PASSWORD>` with the corresponding values.

```bash
sysbench /usr/share/sysbench/oltp_read_write.lua \
  --db-driver=pgsql \
  --report-interval=5 \
  --table-size=1000000 \
  --tables=15 \
  --threads=64 \
  --time=300 \
  --pgsql-host=<RDS-HOST> \
  --pgsql-port=5432 \
  --pgsql-user=postgres_admin \
  --pgsql-password=<DB-PASSWORD> \
  --pgsql-db=sbtest \
  run
```

This script will mimic some real-world OLTP transactions over the database. 
In this case, the options used are the following:
* `--report-interval=5`: Sets the interval (in seconds) at which Sysbench will 
    report intermediate results.
* `--threads=64`: Specifies the number of threads used in the test. This will 
    increase the number of simultaneous database connections.
* `--time=300`: The duration of the test in seconds.
* `oltp_read_write.lua`: These scripts simulate an OLTP workload. It executes a mix of read 
    and write operations to simulate this real-world workload composed of 
    read (`SELECT`), write (`INSERT`) and update/delete operations.

This test will last for 5 minutes. You can check your dashboard to see the increase 
in CPU Utilization. Furthermore, a new notification should be sent to your 
e-mail reporting that the CPU usage has reached the threshold you expected.

<div id='6'/>

### 5.3 - Trigger the Alarm

You will artificially trigger an alarm by shutting the network interface of the 
bastion host. Now run the following command in the bastion host terminal:

```bash
sudo ifconfig ens5 down
```

*Note*: there will be an empty line as an output from this command. This will shutdown the terminal, this is fine as we no longer need to connect to the bastion host.

The alarm will take some time to trigger, but **after about 10 minutes** you should 
have received an email with the details of the alarm. Such as this:

![Alarm](./images/alarm.png)

Stop the command run by pressing `Ctrl+S` (it also might be `Cmd+S`, 
`Ctrl+Q` or `Ctrl+C`). 
Once you receive the notification, go to the AWS console, search for EC2, select your 
bastion host and click on **Instance State**; then, click on Reboot.

## 6 - Tear Down your Infrastructure

Another important advantage of using an IaC tool to manage your infrastructure 
is that you can easily destroy some resources or even tear down your whole 
infrastructure. The cleaning process is easier to perform when you are not going
to use a certain infrastructure anymore. To do so, check that you are in the 
`terraform` directory and run the following command:

```bash
terraform destroy
```  

Execute the command in the terminal to clean up the unwanted resources. 
You will see the execution plan for the destruction process and will be asked 
for confirmation. Answer `yes` to perform the cleanup process.

