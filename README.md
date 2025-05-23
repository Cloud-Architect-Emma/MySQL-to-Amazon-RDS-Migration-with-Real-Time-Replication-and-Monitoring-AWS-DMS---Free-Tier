# MySQL to Amazon RDS Migration with Real-Time Replication and Monitoring (AWS DMS – Free Tier)

## Executive Summary

This project implements a production-grade, cost-optimized database migration architecture using **AWS Database Migration Service (DMS)** to replicate a MySQL database hosted on **Amazon EC2** to an **Amazon RDS for MySQL** instance in near real-time. 

The solution leverages native AWS services for **Change Data Capture (CDC)**, **automated monitoring via CloudWatch**, and **alerting via SNS**, and adheres to the AWS Well-Architected Framework’s pillars: **operational excellence**, **reliability**, and **cost optimization**.

---

## Architecture Diagram

![AWS DMS Architecture](AWS%20DMS%20Architecture/DMS.JPG)

---

## Tech Stack and AWS Services

| Component           | Description                                                        |
|---------------------|--------------------------------------------------------------------|
| **EC2 (MySQL Source)** | Linux-based MySQL DB simulating on-premise infrastructure         |
| **Amazon RDS**         | Managed MySQL DB instance as the target                          |
| **AWS DMS**            | Replication instance enabling full load and ongoing replication  |
| **CloudWatch**         | Logging and metric monitoring                                    |
| **SNS**                | Alerting mechanism for replication failures or lag               |

---

## Key Features

- **Full Load + Ongoing Replication**: Uses CDC to maintain near real-time consistency between source and target.
- **Zero Downtime Migration**: RDS becomes live with replicated production data without downtime.
- **Free-Tier Architecture**: Fully operable under AWS Free Tier with serverless monitoring and notifications.
- **Production-Ready Monitoring**: Real-time logs, replication lag metrics, and email alerting.
- **Scalable and Secure**: Isolated subnets, IAM roles, and fine-grained port access to minimize attack surface.

---

##  Use Case Simulation

> Scenario: A retail company wants to migrate its legacy MySQL database to AWS with minimal downtime and continuous replication.

---

## Deployment Steps

### 1. **Provision Source MySQL (EC2)**

- Launch `t2.micro` EC2 instance with Amazon Linux 2.
- Install MySQL and create `ecommerce` database with sample tables.
- Ensure port `3306` is open **temporarily** to allow DMS connection.
- Insert sample data (`orders`, `products`).

### 2. **Provision Target RDS MySQL**

- Launch `db.t3.micro` MySQL instance using Free Tier template.
- Configure security group to allow access from the DMS VPC.
- Create schema matching source (manual or via AWS SCT).

### 3. **Set Up AWS DMS Replication**

- Create `dms.t3.micro` replication instance.
- Ensure DMS is in the same VPC/subnet as RDS target.
- Create source and target endpoints (test connections).
- Create a **DMS migration task**:
  - Task Type: `Migrate existing data and replicate ongoing changes`
  - Mapping Rules: Select entire schema or specific tables
  - Enable logging to CloudWatch

### 4. **Monitor & Verify Replication**

- Track task status and progress from AWS DMS console.
- Use CloudWatch metrics (`ReplicationLag`, `FullLoadThroughputRowsSource`) to assess performance.
- Insert new records into the EC2 MySQL source and verify real-time appearance in RDS.

### 5. **Set Up SNS Alerts (Monitoring)**

- Create an SNS Topic (`DMSAlertTopic`) and subscribe via email.
- In CloudWatch, create alarms for:
  - `ReplicationLag > 5 seconds`
  - `ErrorCount > 0`
- Link CloudWatch alarms to the SNS topic for proactive alerts.

---

## Security Considerations

- Use IAM roles with least privilege for DMS replication tasks.
- Restrict MySQL access via security groups and VPC routing rules.
- Remove public access to RDS after DMS testing is complete.
- Rotate database credentials and avoid storing in plain text.

---

## Performance and Observability

- Built-in metrics and logging through **Amazon CloudWatch**:
  - DMS task logs
  - Replication throughput
  - Errors and lag time
- Observability aligned with production-level expectations.
- Alerting integration with **Amazon SNS** provides proactive failure notifications.

---

## Future Enhancements

- Replace EC2 source with on-premise MySQL connected via VPN or Direct Connect
- Automate deployment with Terraform or AWS CDK
- Integrate AWS Schema Conversion Tool (SCT) for heterogeneous migrations
- Implement private endpoint connectivity for secure production-grade workflows

---

## License
MIT License. Free to use, modify, and share for educational and professional purposes.
