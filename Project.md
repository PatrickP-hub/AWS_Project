# Secure Web Application with Monitoring and Threat Detection

### Patrick Palmroth 
### AWS Cloud Operations
### 9.2.2026

## 1. Introduction

This project focuses on building a secure web application in AWS with basic monitoring and threat detection capabilites. 

The main idea is to deploy a simple web application running on an EC2 instance and secure it using AWS security best practices such as security groups (firewall rules) and IAM roles. In addition, the system will be monitored using Amazon CloudWatch to detect suspicous activity and ensure system health.

The motivation for choosing this topic is to gain hands-on experience in building secure cloud atchitechtures and understanding how monitoring and alerting work in AWS environments.

Project Goals:
   - To design and implement a simple cloud-based web application
   - To apply basic security practices (IAM and firewall configuration)
   - To monitor system performance and activity using CloudWatch
   - To create alerts for suspicious or unusual behavior
   - To improve overall AWS cloud architechture and operational skills

## 2. Project plan

### Phase 1 design. 
- EC2 isntance: Hosts the web applciation (Windows server running a web server)
- Firewall / Security group: Controls inbound/outbound trafficc to the EC2 instance.
- IAM Roles: Ensures least-privilege access to AWS Resources.

Reasons for chosen services:
- EC2: Provides a simple and flexible environment for hosting a web application.
- Security: Basic network-level security to restrict access.
- IAM: Proper access control is critical for cloud security best practices.

### Phase 2. design.
- cloudwatch
- .....

## 3. Implementation (phase 1)

Resources and Configuration

Resources to create:

- EC2 instance – Windows or Linux web server (t2.micro, free tier).
- Security Group – Allow HTTP (80), HTTPS (443), and SSH (22) from your IP only.
- IAM Role – Minimum permissions for CloudWatch logs.
- Optional: Key pair for SSH access.

System monitoring and alerting with CloudWatch will be implemented in Phase 2 of the project.

### Step 1. Create Key Pair

`aws ec2 create-key-pair --key-name MyWebAppKey --query 'KeyMaterial' --output text > MyWebAppKey.pem`

Goal is to SSH access to EC2.
Permissions must be resticted for SSH access on Linux

`chmod 400 MyWebAppKey.pem`

### Step 2. Create Security Group

`aws ec2 create-security-group --group-name MyWebAppSG --description "Security group for web application"`

Security group ID: ``

Add inbound rules:

- SSH from my IP only : `aws ec2 authorize-security-group-ingress --group-name MyWebAppSG --protocol tcp --port 22 --cidr YOUR_IP/32`

- HTTP : `aws ec2 authorize-security-group-ingress --group-name MyWebAppSG --protocol tcp --port 80 --cidr 0.0.0.0/0`

- HTTPS : `aws ec2 authorize-security-group-ingress --group-name MyWebAppSG --protocol tcp --port 443 --cidr 0.0.0.0/0`

Troubleshooting: If unable to connect via SSH, check the IP in the rule matches your current public IP.

### Step 3: Create IAM Role for EC2

I created a trust policy (trust-policy.json):

`{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}`

Then role: `aws iam create-role --role-name MyWebAppRole --assume-role-policy-document file://trust-policy.json`

Attach CloudWatch access policy: `aws iam attach-role-policy --role-name MyWebAppRole --policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy`

Role appears in IAM console with proper policy attached. CloudWatch logs require EC2 instance profile to allow agent to send metrics.

“The role includes the CloudWatch policy, which will be utilized in Phase 2 when monitoring is configured.

### Step 4: Launch EC2 Instance

`aws ec2 run-instances --image-id ami-0abcdef1234567890 --count 1 --instance-type t2.micro --key-name MyWebAppKey --security-groups MyWebAppSG --iam-instance-profile Name=MyWebAppRole`

Replace ami-0abcdef1234567890 with the latest Linux/Windows AMI ID in your region. Public IP and Instance ID returned.

### Step 5: SSH into EC2

`ssh -i MyWebAppKey.pem ec2-user@PUBLIC_IP`

### Step 6: Deploy Web Application

Linux Example (Apache web server):

`sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
echo "<h1>Hello AWS Secure Web App</h1>" | sudo tee /var/www/html/index.html`

I opened browser at http://PUBLIC_IP and see “Hello AWS Secure Web App”.

This is fully structured and ready for your Phase 1 report. You just need to:

- Replace AMI ID, Public IP, Instance ID, and SNS ARN with your actual values.
- Take screenshots during implementation and insert them where placeholders are.
- Copy commands into Appendix or CLI section of the report for grading.

## (phase2)

## 4. Enchancements
   
## 5. Enchancement X implementation

## 6. Conclusions

### References

Cloud Operation AWS course Moodle page

AWS CLI Command Reference: https://docs.aws.amazon.com/cli/latest/







