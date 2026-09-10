# Highly Available Web Application with ALB, Auto Scaling & RDS

## 📌 Project Overview

This project demonstrates the deployment of a highly available web application on AWS using a secure multi-tier architecture.

The application uses an **Application Load Balancer (ALB)** to distribute incoming HTTP traffic across EC2 instances managed by an **Auto Scaling Group (ASG)**. The application servers run in private subnets and connect securely to an Amazon RDS MySQL database located in private database subnets.

## 🏗️ Architecture


                         Internet
                            │
                            ▼
                ┌──────────────────────┐
                │ Application Load     │
                │ Balancer (ALB)       │
                │ Public Subnets       │
                └──────────┬───────────┘
                           │
                           ▼
              ┌─────────────────────────┐
              │    Auto Scaling Group   │
              │                         │
              │  EC2     EC2     EC2    │
              │ Private Application     │
              │ Subnets                 │
              └────────────┬────────────┘
                           │
                           ▼
                 ┌──────────────────┐
                 │  RDS MySQL       │
                 │  Private DB      │
                 │  Subnets         │
                 └──────────────────┘

The infrastructure is distributed across **3 Availability Zones** for improved availability and resilience.

## 🎯 Project Objectives

* Build a highly available AWS web application architecture
* Distribute traffic using an Application Load Balancer
* Automatically scale EC2 instances based on demand
* Keep application servers in private subnets
* Deploy RDS MySQL without public access
* Implement security-group-based communication between application and database tiers
* Verify EC2-to-RDS connectivity
* Demonstrate basic AWS networking and troubleshooting

## ☁️ AWS Services Used

* Amazon VPC
* Availability Zones
* Public and Private Subnets
* Internet Gateway
* NAT Gateway
* Route Tables
* Security Groups
* Amazon EC2
* EC2 Launch Template
* EC2 Auto Scaling Group
* Application Load Balancer
* Target Group
* Amazon RDS for MySQL
* AWS Systems Manager Session Manager

## 🌐 Network Architecture

The VPC is distributed across three Availability Zones.

### Public Tier

The public subnets contain the Application Load Balancer.

The ALB receives HTTP traffic from the Internet and forwards requests to healthy EC2 instances.

### Application Tier

EC2 instances are deployed in private application subnets and are managed by an Auto Scaling Group.

The EC2 instances run Apache HTTP Server and serve the web application.

### Database Tier

Amazon RDS for MySQL is deployed using a dedicated DB subnet group containing private database subnets.

Public access to the RDS database is disabled.

## 🔐 Security Design

Security groups were configured to control communication between the tiers.

### ALB Security Group

Allows HTTP traffic from the Internet:

```text
HTTP / TCP 80
Source: 0.0.0.0/0
```

### EC2 Security Group

Allows HTTP traffic only from the ALB security group:

```text
HTTP / TCP 80
Source: ALB Security Group
```

### RDS Security Group

Allows MySQL traffic only from the EC2 application security group:

```text
MySQL / TCP 3306
Source: EC2 Application Security Group
```

The RDS database does not accept direct Internet traffic.

## ⚖️ Application Load Balancer

The ALB distributes HTTP requests across healthy instances in the Auto Scaling Group.

Health check configuration:

```text
Protocol: HTTP
Port: 80
Path: /
Success Code: 200
```

The target instances were successfully verified as **Healthy**.

## 📈 Auto Scaling

An EC2 Launch Template was used to provide a consistent configuration for instances launched by the Auto Scaling Group.

The Auto Scaling Group maintains application availability by adding or removing EC2 instances based on the configured scaling policy.

Apache HTTP Server is installed automatically through EC2 User Data.

## 🖥️ EC2 Web Application

The EC2 instances run Apache HTTP Server.

The application displays:

* Server Private IP
* EC2 Instance ID
* Apache Web Server status

Example:

```text
Application Load Balancer + Auto Scaling

Welcome to AWS!

Server Private IP: 10.x.x.x
Instance ID: i-xxxxxxxxxxxxxxxxx

Apache Web Server is Running
```

Refreshing the ALB endpoint can demonstrate traffic being served by different instances.

## 🗄️ RDS MySQL

An Amazon RDS MySQL database was deployed in private database subnets.

Configuration highlights:

* MySQL
* Private DB subnet group
* Public access: Disabled
* Dedicated RDS security group
* Port 3306 accessible only from the EC2 application security group

A test database named `myappdb` was created.

A test table named `employees` was created and populated successfully.

Example test:

```sql
CREATE DATABASE myappdb;

CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    role VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

The EC2 instance successfully connected to RDS and retrieved the inserted database record.

## 🧪 Testing & Validation

The following tests were successfully completed:

### ALB Health Check

```text
HTTP
Port: 80
Path: /
Expected Response: 200
Status: Healthy
```

### Apache Validation

Apache was verified as running on the EC2 instance.

```text
Apache HTTP Server: Active
Port: 80: Listening
```

### HTTP Validation

The EC2 instance returned:

```text
HTTP/1.1 200 OK
```

### EC2 → RDS Connectivity

Connectivity to RDS MySQL port 3306 was successfully verified.

### Database Validation

The `employees` table was successfully queried and returned the inserted record.

## 📸 Screenshots

### VPC & Networking

<img width="1366" height="768" alt="VPC 0 0" src="https://github.com/user-attachments/assets/ed59d9ef-9ad6-49aa-ac82-0bfe406f3942" />
*<img width="1366" height="768" alt="VPC 0 1" src="https://github.com/user-attachments/assets/742c8178-30b0-4f5a-a474-4ee13dbcb76c" />


### Application Load Balancer

<img width="1366" height="768" alt="ALB 0 0" src="https://github.com/user-attachments/assets/17960b5f-8090-4250-ad73-71308eb6daa4" />
*<img width="1366" height="768" alt="ALB 0 1" src="https://github.com/user-attachments/assets/e3f538f1-8933-48aa-948b-ee4b7cd61cca" />


### Target Group

*<img width="1366" height="768" alt="Target Group" src="https://github.com/user-attachments/assets/10f8500b-0d96-4a4f-bbee-95b1cbcd5811" />
<img width="1366" height="768" alt="Target group healthy" src="https://github.com/user-attachments/assets/f08994d7-726f-4cd0-bf33-e5782be85d47" />


### Auto Scaling

*<img width="1366" height="768" alt="ASG 0 0" src="https://github.com/user-attachments/assets/aebc78fe-fe76-4258-a124-c62ca2b3d985" />
<img width="1366" height="768" alt="ASG 0 1" src="https://github.com/user-attachments/assets/7ef77f02-6270-41a0-afe8-3f299e5cc861" />
<img width="1366" height="768" alt="ASG 0 2" src="https://github.com/user-attachments/assets/ac8b03fc-c7d4-432e-90c7-86e795aa7f37" />
<img width="1366" height="768" alt="ASG 0 3" src="https://github.com/user-attachments/assets/2d4e354b-ffeb-49a4-bccf-e15312ffba5d" />
<img width="1366" height="768" alt="ASG 0 4" src="https://github.com/user-attachments/assets/934416c5-05f6-495e-bf76-aca52a0c7d66" />
<img width="1366" height="768" alt="Launch template" src="https://github.com/user-attachments/assets/3025ee58-0ade-4c8c-8289-687910c8ac78" />


### Web Application

*<img width="1366" height="768" alt="Apache Running EC2 1" src="https://github.com/user-attachments/assets/7130b61a-3fd1-4c63-a7b5-fae038d6d321" />
<img width="1366" height="768" alt="Apache Running EC2 2" src="https://github.com/user-attachments/assets/bbb1ed0a-3565-4407-8b7b-7c6050dd75e9" />


### RDS

*<img width="1366" height="768" alt="RDS Database overview" src="https://github.com/user-attachments/assets/476d05b0-d85f-4626-88b6-a3cba10212bc" />
<img width="1366" height="768" alt="RDS Database Connectivity" src="https://github.com/user-attachments/assets/7b361a5e-2b74-4c03-925c-a888aa9c6669" />
<img width="1366" height="768" alt="RDS Database security" src="https://github.com/user-attachments/assets/517fdd79-eb7f-4523-a6e4-57767886feab" />
<img width="1366" height="768" alt="RDS Security group rule" src="https://github.com/user-attachments/assets/f0d8b66d-c1f2-494f-a92e-7f83c235b7bd" />


### Database Test

<img width="1366" height="768" alt="SSM terminal output" src="https://github.com/user-attachments/assets/d46c5b93-f27c-4938-8377-6b7588c5b17f" />


## 🔑 Key Learnings

Through this project, I practiced:

* Designing multi-AZ AWS infrastructure
* Public vs private subnet architecture
* Application Load Balancer configuration
* EC2 Auto Scaling
* Launch Templates
* Target Group health checks
* Security Group-based communication
* Private RDS deployment
* EC2-to-RDS connectivity
* MySQL database operations
* AWS Systems Manager Session Manager
* Troubleshooting unhealthy ALB targets

## 💼 Interview Summary

> I built a highly available web application on AWS across three Availability Zones. The Application Load Balancer receives Internet traffic and distributes it to EC2 instances managed by an Auto Scaling Group in private application subnets. I deployed an RDS MySQL database in private DB subnets and configured the RDS security group to allow MySQL traffic only from the application security group. I verified ALB health checks, EC2 HTTP responses, EC2-to-RDS connectivity, and database operations.

## 🧹 Cost Management

This project is intended for learning and portfolio purposes.

AWS resources should be stopped or deleted when they are no longer required to avoid unnecessary charges, especially resources such as:

* NAT Gateway
* RDS
* EC2
* Application Load Balancer

## 📚 Skills Demonstrated

**AWS | VPC | EC2 | ALB | Auto Scaling | RDS MySQL | IAM | Security Groups | NAT Gateway | Linux | Apache | MySQL | Systems Manager**

