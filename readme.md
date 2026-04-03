# 🌐 AWS Production-Grade Web Hosting Infrastructure

## 📋 Project Overview

This project implements a **complete, highly available web hosting infrastructure** on AWS. The architecture includes a custom VPC with public/private subnets, NAT Gateway, Auto Scaling Group, Application Load Balancer (ALB), and a serverless monitoring system that sends email alerts when the application fails.

### 🎯 What This Project Demonstrates
- Production-ready VPC design with proper network segmentation
- High availability across multiple Availability Zones
- Auto-scaling web servers running Nginx
- Load balancing for traffic distribution
- Serverless health monitoring using Lambda + EventBridge
- Real-time email notifications via SNS

---

## 🏗️ Architecture Components

### Network Layer (VPC)

![VPC Architecture](https://github.com/user-attachments/assets/3d63e128-c9a2-484a-b2ef-7a23ef56ad4b)

### Flow Diagram

![Flow Diagram](https://github.com/user-attachments/assets/02b2934a-d976-4cd1-ac69-3aad0cfaadb9)

**VPC Configuration:**
- **VPC Name**: `web-hosting-vpc`
- **VPC ID**: `vpc-0034b98a0f47cb24e`
- **IPv4 CIDR**: `10.0.0.0/16` (Default VPC range)
- **DNS Resolution**: Enabled
- **Tenancy**: Default
- **Main Network ACL**: `acl-05a385034af01f558`

### Subnet Configuration

![Subnet Configuration](https://github.com/user-attachments/assets/e947305f-9f08-4d7c-9ce0-82ed0bb62ec5)

The VPC contains **4 subnets** distributed across two Availability Zones:
- `web-hosting-subnet-private1` (ap-south-1a)
- `web-hosting-subnet-private2` (ap-south-1b)
- `web-hosting-subnet-private3` (ap-south-1c)
- `web-hosting-subnet-private4` (ap-south-1d)

### Route Tables & NAT Gateway

![Route Tables & NAT Gateway](https://github.com/user-attachments/assets/aa7a32dd-e7c1-4b30-8102-4bff373b7adf)

**Route Tables Created:**
- `web-hosting-rtb-private1` (ap-south-1a)
- `web-hosting-rtb-private2` (ap-south-1b)
- `web-hosting-rtb-private3` (ap-south-1c)
- `web-hosting-rtb-private4` (ap-south-1d)
- `web-hosting-rtb-public` (for internet access)

**NAT Gateway:**

![NAT Gateway](https://github.com/user-attachments/assets/bbb30dec-0b22-4377-9a2f-c80e05cb6a5d)

- **Name**: `web-hosting-regional-nat`
- **ID**: `nat-16578515295ccfe0`
- **Type**: Regional, Public connectivity
- **Public IPs**: 43.204.117.50, 13.234.16.32, 13.234.15.24
- **Purpose**: Allows private instances to access internet

---

## 💻 Compute Resources

### EC2 Instances

![EC2 Instances](https://github.com/user-attachments/assets/40d46505-cbf9-469c-a21a-afa6f535d6e5)

| Instance ID | Name | Type | Status | AZ | Public IP |
|-------------|------|------|--------|----|------------|
| i-0b602ceb1d2e7a46e | - | t3.micro | Running | ap-south-1b | Disabled |
| i-0d6103ff495c9e0e1 | public | t3.micro | Terminated | ap-south-1a | Disabled |
| i-0fc8e937791abeb90 | private | t3.micro | Running | ap-south-1a | 15.206.160.26 |
| i-0b60918252424a14d1 | public | t3.micro | Running | ap-south-1a | Disabled |
| i-0595a51b9f3c43293 | - | t3.micro | Running | ap-south-1a | Disabled |

### Web Server Configuration (Nginx)

![Nginx Configuration](https://github.com/user-attachments/assets/d8995785-d2f5-4357-80c0-c7116b7a1c87)

**Server Details:**
- **OS**: Ubuntu 20.04 LTS
- **Web Server**: Nginx
- **Status**: Active and running
- **Process**: Master + 2 worker processes
- **Memory Usage**: ~3.6 MB

**System Status:**
- Disk Usage: 32% (0.71GB used)
- Active Processes: 114
- Nginx Service: Enabled and running

---

## ⚖️ Load Balancing & Auto Scaling

### Application Load Balancer (ALB)

![Application Load Balancer](https://github.com/user-attachments/assets/caaea66a-1df4-4f21-afa6-25011637e13c)

**Configuration:**
- **Name**: `web-vpc-loadbalancer`
- **Type**: Application Load Balancer
- **State**: Active
- **DNS Name**: `web-vpc-loadbalancer-1268693883.ap-south-1.elb.amazonaws.com`
- **Hosted Zone**: ZP97RAFLXTNZK
- **Listener**: HTTP:80
- **Availability Zones**: 
  - `subnet-004b518627c525088`
  - `subnet-0415f7617ac949696`

### Target Group

![Target Group](https://github.com/user-attachments/assets/a0184de1-b6b2-4b1e-af7e-0af8203c555a)

**Configuration:**
- **Name**: `vpc-target`
- **Target Type**: Instance
- **Protocol**: HTTP:80
- **Protocol Version**: HTTP1
- **Health Check Status**:

| Instance ID | Name | AZ | Health Status |
|-------------|------|----|---------------|
| i-0b602ceb1d2e7a46e | - | ap-south-1b | ✅ Healthy |
| i-0595a51b9f3c43293 | - | ap-south-1a | ✅ Healthy |
| i-060918252424a14d1 | public | ap-south-1a | 🔄 Draining |

### Auto Scaling Group

![Auto Scaling Group](https://github.com/user-attachments/assets/2d3169b2-b865-4162-90db-5b825f26257e)

**Configuration:**
- **Name**: `web-vpc-asg`
- **Launch Template**: `web-vpc-template` (ID: lt-0d05d69f226af83c0)
- **AMI**: `ami-0e8bad09ae4be267b`
- **Instance Type**: t3.micro
- **Key Pair**: `key-mumbai`
- **Security Group**: `sg-02a7112e67aa5254b`

**Scaling Configuration:**
- **Desired Capacity**: 2 instances
- **Minimum**: 2 instances
- **Maximum**: 4 instances
- **Availability Zones**: ap-south-1a, ap-south-1b
- **Distribution**: Balanced best effort

---

## 🌐 Web Page DNS Hosting

![DNS Hosting](https://github.com/user-attachments/assets/e5c3ba6e-1060-4627-b653-2b82b2adde86)

**Access Your Application:**

---

## 📧 Monitoring & Alerting System

### SNS Topic for Notifications

![SNS Topic](https://github.com/user-attachments/assets/dbd79b48-dafe-4d75-bf3a-d6d2a30c950a)

**Configuration:**
- **Topic Name**: `vpc-sns-web`
- **ARN**: `arn:aws:sns:ap-south-1:392423995402:vpc-sns-web`
- **Subscription**: Email endpoint (kishorehc99@gmail.com)
- **Status**: Confirmed

### Lambda Function for Health Checks

![Lambda Function](https://github.com/user-attachments/assets/53b1dd8a-08f5-49b8-b508-9bfb8c001522)

**Configuration:**
- **Function Name**: `function1`
- **Runtime**: Node.js/Python (Lambda)
- **Trigger**: EventBridge (CloudWatch Events)
- **Schedule**: Periodic execution
- **Purpose**: Health check monitoring

### EventBridge Rule
- **Rule Name**: `xfusion-ec2-aml`
- **Schedule**: Regular intervals
- **Target**: Lambda function `function1`

### Email Notification Received

![Email Notification](https://github.com/user-attachments/assets/91399f26-7a7c-4aa6-a7eb-6091d72fd7d0)

### Sample Notification:

json
{
  "version": "1.0",
  "timestamp": "2026-04-03T19:10:44.172Z",
  "requestContext": {
    "requestId": "50afe715-c2ce-4c34-afed-97a2f9990f2f",
    "functionArn": "arn:aws:lambda:ap-south-1:392423995402:function:1:SLATEST",
    "condition": "Success"
  }
}



### 📋 Project Overview

**Dream Job Cloud Platform** is a complete job portal web application deployed on AWS with enterprise-grade architecture. The platform helps job seekers find their dream jobs while demonstrating production-ready cloud infrastructure patterns.

**What makes this special?**
- 🚀 **True High Availability** - Runs across multiple Availability Zones
- ⚡ **Auto-scaling** - Automatically handles traffic spikes
- 📧 **Smart Monitoring** - Serverless Lambda sends email alerts if anything fails
- 🔒 **Enterprise Security** - Private subnets, NAT Gateway, no direct instance exposure

**From your screenshots**: The UI clearly shows "Are you looking for your dream job?" - that's where the name comes from!

---

## 🏗️ Architecture at a Glance
User → ALB (Public) → Auto Scaling Group → EC2 Instances (Private Subnets)
↓
Health Checks
↓
Lambda (Every 5 mins)
↓
SNS → Email Alert if Failed

## 📊 Quick Stats from Your Deployment

| Metric | Value |
|--------|-------|
| **VPC ID** | vpc-0034b98a0f47cb24e |
| **Load Balancer DNS** | web-vpc-loadbalancer-1268693883.ap-south-1.elb.amazonaws.com |
| **Running Instances** | 3 (t3.micro) |
| **Auto Scaling** | 2-4 instances |
| **Availability Zones** | 4 (ap-south-1a,1b,1c,1d) |
| **NAT Gateway IPs** | 43.204.117.50, 13.234.16.32, 13.234.15.24 |
| **Monitoring** | Lambda + SNS (email alerts) |
| **Web Server** | Nginx on Ubuntu 20.04 |

## 👨‍💻 Author

**Kishore H C**

- 📧 Email: [kishorhc2004@gmail.com](mailto:kishorhc2004@gmail.com)
- 💼 LinkedIn: [linkedin.com/in/kishore-h-c-847775287/](https://www.linkedin.com/in/kishore-h-c-847775287/)

---

> ⭐ If you found this project helpful, please consider giving it a star on GitHub!



📄 License
This project is for educational purposes. Feel free to use and modify for learning AWS.


