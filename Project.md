# Secure Web Application with Monitoring and Threat Detection

### Patrick Palmroth 
### AWS Cloud Operations
### 9.2.2026

## Chapter 1 - Introduction

This project focuses on building a secure web application in AWS with basic monitoring and threat detection capabilites. 

The main idea is to deploy a simple web application running on an EC2 instance and secure it using AWS security best practices such as security groups (firewall rules) and IAM roles. In addition, the system will be monitored using Amazon CloudWatch to detect suspicous activity and ensure system health.

The motivation for choosing this topic is to gain hands-on experience in building secure cloud atchitechtures and understanding how monitoring and alerting work in AWS environments.

Project Goals:
   - To design and implement a simple cloud-based web application
   - To apply basic security practices (IAM and firewall configuration)
   - To monitor system performance and activity using CloudWatch
   - To create alerts for suspicious or unusual behavior
   - To improve overall AWS cloud architechture and operational skills

## Phase 1. Simple architecture planning and implementation

### Phase 1 design. 
- EC2 isntance: Hosts the web applciation (Windows server running a web server)
- Firewall / Security group: Controls inbound/outbound trafficc to the EC2 instance.
- IAM Roles: Ensures least-privilege access to AWS Resources.
- Cloudwatch: Monitors logs and metrics, triggers alarms for suspicious activity.

Reasons for chosen services:
- EC2: Provides a simple and flexible environment for hosting a web application.
- Security: Basic network-level security to restrict access.
- IAM: Proper access control is critical for cloud security best practices.
- Cloudwatch: Enables operational visibility and automated alerting

[User] --> [Security Group/Firewall] --> [EC2 Instance (Web Server)] --> [CloudWatch Logs & Alarms] --> [IAM Role Access to AWS Resources]

### Phase 1 implementation



### Phase 2. desing
- incoming.....




