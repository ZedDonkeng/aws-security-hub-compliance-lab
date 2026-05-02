# 🛡️ AWS Security Hub Compliance Automation Lab

![AWS](https://img.shields.io/badge/AWS-SecurityHub-orange)
![EventBridge](https://img.shields.io/badge/AWS-EventBridge-blue)
![SNS](https://img.shields.io/badge/AWS-SNS-yellow)
![Status](https://img.shields.io/badge/Project-Completed-brightgreen)
# Automating Multi-Standard Compliance with AWS Security Hub

**CyberDyne Systems** is a rapidly scaling tech firm that recently migrated its core infrastructure to AWS. To maintain its competitive edge and meet strict regulatory requirements, CyberDyne needs a way to continuously audit its environment. Manually checking configurations is no longer sustainable.

## 🎯 Objective

Design a system that:

- Monitors AWS resources using **AWS Security Hub**
- Enforces compliance with:
  - PCI DSS v3.2.1
  - AWS Foundational Security Best Practices (FSBP)
- Triggers alerts for **High** and **Critical** findings
- Sends real-time notifications via email

---

## 🧰 Architecture

**Core AWS Services Used:**

- AWS Security Hub (compliance & findings)
- AWS Config (resource evaluation)
- Amazon EventBridge (event routing)
- Amazon SNS (notifications)

---

## 📚 Table of Contents


- [Phase 1: Establish the Notification Pipeline (SNS)](#-phase-1-establish-the-notification-pipeline-sns)
- [Phase 2: Orchestrate Event Routing (EventBridge)](#-phase-2-orchestrate-event-routing-eventbridge)
- [Phase 3: Enable Security Monitoring (Security Hub + AWS Config)](#-phase-3-enable-security-monitoring-security-hub--aws-config)
- [Phase 4: Validate the System (Generate Findings via EC2)](#-phase-4-validate-the-system-generate-findings-via-ec2)
- [Phase 5: Verification (Findings & Alerts)](#-phase-5-verification-findings--alerts)

## 🚀 Phase 1: Establish the Notification Pipeline

### Create an SNS Topic

1. Log in to AWS Console (`us-east-1`)
2. Navigate to **Simple Notification Service (SNS)**
3. Click **Create topic**

**Configuration:**
- Name: `SecurityAlerts_North`

4. Expand **Access policy**
   - Set **Publishers** = Everyone
   - Set **Subscribers** = Everyone

5. Click **Create topic**

---

### Subscribe to Alerts

1. Click **Create subscription**
2. Protocol: **Email**
3. Endpoint: Enter your email
4. Click **Create subscription**

> ⚠️ Confirm the subscription via email (status must be **Confirmed**)

---

## 🔄 Phase 2: Orchestrate Event Routing with EventBridge

### Create Rule

1. Go to **Amazon EventBridge**
2. Click **Create rule**

**Configuration:**
- Name: `AlertOnCriticalFindings`
- Description: Triggers email when Security Hub detects High/Critical failures

---

### Event Pattern

- AWS Service: **Security Hub**
- Event Type: **Security Hub Findings - Imported**
- Compliance Status: **FAILED**
- Record State: **ACTIVE**
- Severity: **HIGH**, **CRITICAL**

---

### Target

- Type: **SNS Topic**
- Select: `SecurityAlerts_North`

Finish setup → Click **Create rule**

---

## 🛡️ Phase 3: Enable Security Monitoring

### Enable AWS Config

1. Navigate to **AWS Config**
2. Click **1-click setup**
3. Click **Confirm**

---

### Enable Security Hub

1. Navigate to **Security Hub**
2. Click **Get started**
3. Click **Enable Security Hub**

---

### Configure Standards

✅ Enable:
- AWS Foundational Security Best Practices (FSBP)
- PCI DSS v3.2.1  

❌ Disable:
- CIS AWS Foundations Benchmark  

---

## 🧪 Phase 4: Validate the System (Generate Findings)

To validate the pipeline, create an insecure EC2 instance.

---

### Step 1: Create Security Group (Intentionally Insecure)

1. Go to **EC2 → Security Groups**
2. Click **Create security group**

**Configuration:**
- Name: `Permissive_Test_SG`
- Description: Testing Security Hub alerting
- VPC: Default

### Inbound Rules

| Type             | Protocol | Port | Source     |
|------------------|----------|------|-----------|
| All ICMP - IPv4  | ICMP     | All  | 0.0.0.0/0 |
| SSH              | TCP      | 22   | 0.0.0.0/0 |
| SSH              | TCP      | 22   | ::/0      |
| RDP              | TCP      | 3389 | 0.0.0.0/0 |
| RDP              | TCP      | 3389 | ::/0      |

Click **Create security group**

> ⚠️ These rules are intentionally unsafe to trigger alerts

---

### Step 2: Launch EC2 Instance

1. Go to **EC2 → Instances → Launch instances**

**Configuration:**
- Name: `Insecure-Test-Instance`
- AMI: Amazon Linux 2
- Instance Type: `t2.micro`

### Key Pair
- Select or create one (optional for this test)

### Network Settings
- VPC: Default
- Auto-assign Public IP: **Enable**

### Security Group
- Select existing → `Permissive_Test_SG`

Click **Launch instance**

---

### Step 3: Wait for Evaluation

⏱️ Allow **5–15 minutes** for:
- AWS Config recording
- Security Hub evaluation

---

## 🔍 Phase 5: Verification

1. Navigate to **Security Hub → Findings**
2. Look for **High severity findings**

### Expected Issues

- Open SSH access (0.0.0.0/0)
- Open RDP access (0.0.0.0/0)
- Unrestricted ICMP

---

### 📧 Alert Confirmation

- Check your email inbox
- You should receive an **SNS notification**
- Message format: **JSON**

---

## ✅ Result

You have successfully built:

- Continuous compliance monitoring
- Automated alerting pipeline
- Multi-standard enforcement (PCI DSS + FSBP)

---

## 🎉 Outcome

CyberDyne Systems now has a **scalable, automated security monitoring system** that:

- Detects misconfigurations in real time
- Prioritizes critical risks
- Notifies stakeholders instantly

