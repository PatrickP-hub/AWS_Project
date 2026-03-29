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

Step 1: Create Key Pair

Command:

aws ec2 create-key-pair --key-name MyWebAppKey --query 'KeyMaterial' --output text > MyWebAppKey.pem

Purpose: Generate key for SSH access to EC2.
Validation: File MyWebAppKey.pem created.

Screenshot placeholder:
[Insert screenshot showing MyWebAppKey.pem created]

Lesson Learned: Ensure key permissions are restricted:

chmod 400 MyWebAppKey.pem
Step 2: Create Security Group

Command:

aws ec2 create-security-group --group-name MyWebAppSG --description "Security group for web application"

Add Inbound Rules:

# SSH from your IP only
aws ec2 authorize-security-group-ingress --group-name MyWebAppSG --protocol tcp --port 22 --cidr YOUR_IP/32

# HTTP
aws ec2 authorize-security-group-ingress --group-name MyWebAppSG --protocol tcp --port 80 --cidr 0.0.0.0/0

# HTTPS
aws ec2 authorize-security-group-ingress --group-name MyWebAppSG --protocol tcp --port 443 --cidr 0.0.0.0/0

Validation: Test SSH connection.

Screenshot placeholder:
[Insert screenshot showing Security Group rules in AWS console]

Troubleshooting:

Issue: Cannot SSH → check that Security Group allows your current public IP.
Step 3: Create IAM Role for EC2

Trust Policy (trust-policy.json):

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {"Service": "ec2.amazonaws.com"},
      "Action": "sts:AssumeRole"
    }
  ]
}

Commands:

aws iam create-role --role-name MyWebAppRole --assume-role-policy-document file://trust-policy.json
aws iam attach-role-policy --role-name MyWebAppRole --policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy

Validation: Role appears in IAM console with correct policy attached.

Screenshot placeholder:
[Insert screenshot of IAM role with CloudWatch policy attached]

Lesson Learned: CloudWatch requires IAM permissions to send metrics from EC2.

Step 4: Launch EC2 Instance

Command:

aws ec2 run-instances --image-id ami-0abcdef1234567890 --count 1 --instance-type t2.micro --key-name MyWebAppKey --security-groups MyWebAppSG --iam-instance-profile Name=MyWebAppRole
Replace ami-0abcdef1234567890 with your region’s latest Linux/Windows AMI.

Validation: Instance launches; note the Instance ID and Public IP.

Screenshot placeholder:
[Insert screenshot showing EC2 instance running with public IP]

Troubleshooting:

EC2 does not start → verify key pair, IAM role, and security group.
Step 5: SSH / RDP into EC2

Linux Example (SSH via PuTTY / cmd):

ssh -i MyWebAppKey.pem ec2-user@PUBLIC_IP

Validation: You can access EC2 terminal.

Screenshot placeholder:
[Insert screenshot showing successful SSH login]

Step 6: Deploy Web Application

Linux Web Server (Apache example):

sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
echo "<h1>Hello AWS Secure Web App</h1>" | sudo tee /var/www/html/index.html

Validation: Open browser at http://PUBLIC_IP → page displays “Hello AWS Secure Web App”.

Screenshot placeholder:
[Insert screenshot showing web page loaded]

Troubleshooting:

Page not loading → check Security Group allows HTTP port 80.
Step 7: Configure CloudWatch Agent

Install and Start Agent:

sudo yum install amazon-cloudwatch-agent -y
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a start

Validation: Metrics appear in CloudWatch.

Screenshot placeholder:
[Insert screenshot showing EC2 metrics in CloudWatch]

Lesson Learned: JSON config must be valid for metrics to be collected.

Step 8: Create CloudWatch Alarm

Command:

aws cloudwatch put-metric-alarm --alarm-name HighCPUAlarm --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 300 --threshold 70 --comparison-operator GreaterThanThreshold --dimensions Name=InstanceId,Value=INSTANCE_ID --evaluation-periods 1 --alarm-actions arn:aws:sns:REGION:ACCOUNT_ID:MySNS

Validation: Trigger test alarm (e.g., stress CPU). Alarm activates in CloudWatch console.

Screenshot placeholder:
[Insert screenshot showing alarm triggered in CloudWatch]

Troubleshooting:

Alarm does not trigger → check Instance ID, CloudWatch metrics, or SNS subscription.

9. Summary of Learning
AWS CLI is powerful for quick deployment and configuration.
Key lessons: Security Group rules, IAM permissions, and CloudWatch configuration are essential for security and monitoring.
Testing step-by-step avoids hidden errors and simplifies troubleshooting.

### Phase 2. desing
- incoming.....




