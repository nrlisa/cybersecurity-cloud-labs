# Introduction to Amazon EC2 ☁️

## 📌 Overview

This lab introduces the basics of launching, monitoring, securing, and resizing an Amazon EC2 instance using Amazon Web Services. The objective is to understand how virtual servers work in the cloud and how to manage them through the AWS Management Console.

---

# 🚀 Task 1 — Launching an EC2 Instance

## Objective

Create and launch a virtual machine using Amazon EC2.

## Activities

* Selected Amazon Machine Image (AMI)
* Chose instance type (`t3.micro`)
* Configured security settings
* Added user data script
* Launched EC2 instance
* Verified instance status

## Expected Screenshots

* Launch Instance page
  <div align="center"><img src="Images/task1-launch-instance-page.png" alt="Launch Instance page" width="600"></div>
* Instance configuration
  <div align="center"><img src="Images/task1-instance-configuration.png" alt="Instance configuration" width="600"></div>
* Running instance dashboard
  <div align="center"><img src="Images/task1-running-instance-dashboard.png" alt="Running instance dashboard" width="600"></div>
* Status checks passed
  <div align="center"><img src="Images/task1-status-checks-passed.png" alt="Status checks passed" width="600"></div>

---

# 🔐 Task 2 — Configure Security Group & Access Web Server

## Objective

Allow HTTP traffic to access the hosted web server.

## Activities

* Copied Public IPv4 address
* Attempted web access before rule configuration
* Edited inbound security group rules
* Added HTTP rule (Port 80)
* Refreshed browser to verify web server access

## Expected Screenshots

* Browser connection failure
  <div align="center"><img src="Images/task3-browser-connection-failure.png" alt="Browser connection failure" width="600"></div>
* Inbound rules configuration
  <div align="center"><img src="Images/task3-inbound-rules-configuration.png" alt="Inbound rules configuration" width="600"></div>
* Added HTTP rule
  <div align="center"><img src="Images/task3-added-http-rule.png" alt="Added HTTP rule" width="600"></div>
* “Hello From Your Web Server!” webpage
  <div align="center"><img src="Images/task3-web-server-webpage.png" alt="Web Server webpage" width="600"></div>

---

# ⚙️ Task 3 — Resize EC2 Instance & EBS Volume

## Objective

Modify EC2 resources based on workload requirements.

## Activities

### Instance Resize

* Stopped EC2 instance
* Changed instance type from `t3.micro` to `t3.small`

### Restart Instance

* Started resized instance
* Verified successful startup

## Expected Screenshots

* Stop instance page
  <div align="center"><img src="Images/task4-stop-instance-page.png" alt="Stop instance page" width="600"></div>
* Change instance type window
  <div align="center"><img src="Images/task4-change-instance-type.png" alt="Change instance type window" width="600"></div>



---

# 🧠 Skills Gained

* **Cloud Infrastructure Deployment:** Configuring, provisioning, and launching virtual machines (EC2) using the AWS Management Console.
* **Network Security Configuration:** Setting up Security Groups and editing inbound rules to allow specific web traffic (HTTP Port 80).
* **Resource Scaling:** Managing instance lifecycles by stopping, resizing (modifying instance types), and restarting instances to meet changing workload requirements.
* **Bootstrapping:** Using user data scripts to automate the installation and configuration of a web server during the initial instance launch.

---

# ✅ Conclusion

This lab provided hands-on experience with launching and managing an EC2 instance in Amazon Web Services. Key skills learned include instance deployment, monitoring, security configuration, and resource scaling within a cloud environment.
