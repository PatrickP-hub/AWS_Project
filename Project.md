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

I started this project with creating a EC2-instance in AWS manager. I searched "EC2" and pressed launch instance with these informations:
 - Name = project
 - AMI = Aamzon Linux 2023 kernel-6.1 AMI
 - Instance type = t2.mciro
 - Network setting -> edit 
    - key pair name = vockey
    - VPC = default
    - Subnet = Public
    - Auto-assign public IP = enable
    - Security group name = project security group
    - description = permit ssh connections
- configure storage = 1x8GiB gp3

And lastly launch the instance.

<img width="429" height="60" alt="image" src="https://github.com/user-attachments/assets/6fc24921-360b-4b4a-81df-7963b976957f" />

<img width="640" height="728" alt="image" src="https://github.com/user-attachments/assets/fed4d657-31b5-4966-b16f-beee19315be4" />

After this I downloaded the .ppk file from AWS lab and put it to PuTTy.exe with connection timed out 30seconds and with the public IPv4 address from the EC2-instance.

<img width="653" height="252" alt="image" src="https://github.com/user-attachments/assets/796ee7c6-c0b1-48f9-846e-8919cf614391" />

Connected to the EC2 instance using PuTTY and a .ppk private key and "ec2-user". 

And started with updating system and installed apache `sudo yum install httpd -y`

<img width="777" height="270" alt="image" src="https://github.com/user-attachments/assets/a3919020-48cc-41b9-aedf-54979c5da029" />

After this I started and enabled the web server `sudo systemctl start httpd` & `sudo systemctl enable httpd`

<img width="776" height="338" alt="image" src="https://github.com/user-attachments/assets/09bc15c8-729f-4fc2-8ebb-22ae55b2b509" />

Then I created the index page with command `echo "<h1>Tämä on projektin etusivu</h1>" | sudo tee /var/www/html/index.html`

<img width="775" height="62" alt="image" src="https://github.com/user-attachments/assets/6712b0e7-87e8-47ba-aa7f-4ef033104387" />

I went to check web page in the browser `http://publicIP`

<img width="856" height="367" alt="image" src="https://github.com/user-attachments/assets/4868bc6e-6656-48d5-9b84-56f389bdcc8f" />

It couldn't be reached so I checked with command `sudo systemctl status httpd` if it is running or not 

<img width="774" height="257" alt="image" src="https://github.com/user-attachments/assets/ddb65e2c-4bdb-4374-a525-c33bcda3a56b" />

I forgot to allow rule "http" in the security group so I did this part next

<img width="1212" height="318" alt="image" src="https://github.com/user-attachments/assets/6bc91a06-23c5-48f7-a507-1fa8bcd45dc6" />

I gave this informations to inbound rules and tried again.

<img width="427" height="108" alt="image" src="https://github.com/user-attachments/assets/d6f38047-6f20-40ec-b177-26d22bd0e7b5" />

It worked! I tried also with the command `curl -I localhost`

<img width="426" height="37" alt="image" src="https://github.com/user-attachments/assets/48b8b0db-cba4-4b2c-8122-9da5c8dee4a5" />

I made one more rule to allow "HTTPS" so I have the all 3 protocols added to my project.

<img width="1186" height="84" alt="image" src="https://github.com/user-attachments/assets/86546305-d589-4e9c-ae42-4c2343aa4610" />

Then I downloaded SSLmodule `sudo yum install -y mod_ssl`

<img width="714" height="87" alt="image" src="https://github.com/user-attachments/assets/d7cb599d-a97e-4a85-92b3-3514f5b7f06c" />
<img width="1203" height="530" alt="image" src="https://github.com/user-attachments/assets/10c313e1-93dc-48eb-b235-9871100dd987" />


I checked if the https connection worked and it worked, but te connection is not secured so maybe I try to do that next




After this I wanted to create IAM role for the phase 2 "CloudWacth" logs. I went to IAM -> Roles and created role. I gave these informations
- trusted entity type = AWS service
- Use case = EC2

After this I added permissions policies to "CloudWatchAgentServerPolicy"

<img width="947" height="281" alt="image" src="https://github.com/user-attachments/assets/d0f14226-527e-456f-bf2d-f44eef1f42fb" />

Last I gave the name "Project-CloudWatch" and moved on with the default .json trust policy.

<img width="916" height="579" alt="image" src="https://github.com/user-attachments/assets/eec50469-f339-4746-add8-25ac23450f8e" />
<img width="1179" height="80" alt="image" src="https://github.com/user-attachments/assets/bfafcc93-d88f-439a-853a-2de11cd90e91" />

It didn't let me do the IAM role so I went to my EC2 instance -> actions -> modify IAM role and chose "labinstanceProfile" and updated the IAM role

<img width="478" height="49" alt="image" src="https://github.com/user-attachments/assets/74a9a452-09b5-4e07-91cf-a217722f1909" />



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







