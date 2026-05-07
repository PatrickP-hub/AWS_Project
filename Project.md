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
- Amazon CloudWatch - collects metrics and logs from the EC2 instance
- CloudWatch Agent - sends system-level metrics (CPU, memory, disk)
- CloudWatch Alarms - triggers alerts based on thresholds
- SNS (Simple Notification Service) - sends notifications (email)

Objectives:
- Detect unusual system activity (high CPU usage)
- Monitor system health in real time
- Receive Alerts about potential issues or threats

## 3. Implementation (phase 1)

Resources and Configuration

Resources to create:

- EC2 instance – Windows or Linux web server (t2.micro, free tier).
- Security Group – Allow HTTP (80), HTTPS (443), and SSH (22) from your IP only.
- IAM Role – Minimum permissions for CloudWatch logs.
- Optional: Key pair for SSH access.

System monitoring and alerting with CloudWatch will be implemented in Phase 2 of the project.

### EC2 instance creation

The project begins by creating an EC2 instance in AWS manager. I searched "EC2" and pressed launch instance. The EC2 instance was launched with the following settings…
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

### Apache installation

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

### Security group configuration

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


I checked if the https connection worked and it worked, but te connection is not secured, but after some research I think it doesn't matter that much and the browser just doens't recognize the signature but it is still trusted because I made it. 

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
<img width="117" height="58" alt="image" src="https://github.com/user-attachments/assets/be32b12a-b376-40d6-9499-44d84722116b" />

Phase 1 successfully established a basic web application environment on EC2 with proper security group rules, IAM role preparation for CloudWatch, and a working Apache web server.

## 4. Enchancements

Phase 2 improves the system with:
- Real-time monitoring using CloudWatch
- Centralized log collection and analysis
- Automated alerts for suspicious activity
- Better visibility into system performance
- Faster response to incidents
   
## 5. Enchancement X implementation (phase2)

### Installing CloudWatch Agent

I started first by going to EC2 with putty.exe, then was time to configure the followings:
- Installing the CloudWatch agent
- Configuration
- Starting
- Testing

First I updated the system packages
<img width="402" height="17" alt="image" src="https://github.com/user-attachments/assets/5af3d92b-057f-4989-94b6-73422276037a" />

After that I installed the CloudWatch Agent

<img width="644" height="280" alt="image" src="https://github.com/user-attachments/assets/a14e2d5e-d748-4eb6-9f6b-e34c9a86a7c8" />

When this was done I wanted to define the metrics and log files with the wizard command with:

<img width="647" height="30" alt="image" src="https://github.com/user-attachments/assets/a0327984-2b08-4350-b50f-6f7d6c1581a6" />

First I chose the Linux operation system and then I chose these options:

<img width="755" height="950" alt="image" src="https://github.com/user-attachments/assets/c9308028-841b-4972-83aa-b9e8a597d15b" />

Here is the whole command:

<img width="601" height="405" alt="image" src="https://github.com/user-attachments/assets/72ce785c-e274-4b89-a166-270593bd575a" />

<img width="752" height="430" alt="image" src="https://github.com/user-attachments/assets/74ba5545-ce08-4c10-9a4e-b09f640b5258" />

<img width="756" height="674" alt="image" src="https://github.com/user-attachments/assets/2def5b3a-7332-45ac-8d3b-fb63a6d5b01a" />

<img width="753" height="642" alt="image" src="https://github.com/user-attachments/assets/cd2de5ac-521c-4020-8cc6-06b0d23047f2" />

<img width="672" height="131" alt="image" src="https://github.com/user-attachments/assets/22525190-39fd-44f4-8a97-cf656eb97603" />

After these congiurations I gave these commands:

<img width="751" height="67" alt="image" src="https://github.com/user-attachments/assets/81cd6e24-056b-4a4f-aa30-05ed1b276675" />

CloudWatch Agent was started and enabled to run automatically on system boot. I wanted to check the status from the cli:

<img width="771" height="112" alt="image" src="https://github.com/user-attachments/assets/c3adf650-37bd-4d39-bf2d-df253807368c" />

It didn't start properly... I researched some and tried to overwrite the whole code again

<img width="853" height="579" alt="image" src="https://github.com/user-attachments/assets/13ad3665-29c4-493a-a4eb-587af3a20c6f" />

<img width="868" height="32" alt="image" src="https://github.com/user-attachments/assets/d7fc93e2-dca6-451a-9f97-d58a297049e3" />

Then was time to try again the status:

<img width="866" height="170" alt="image" src="https://github.com/user-attachments/assets/22fb2dcf-6196-4f08-86d8-b6a3bffa4148" />

It worked!

### CloudWatch Alarm

I went to CloudWatch in AWS Management console and from the left navigation pane "alarms" -> create alarm.

First select metric -> EC2

<img width="826" height="728" alt="image" src="https://github.com/user-attachments/assets/a0f7f2ca-b5c3-4de2-977f-8421ace2e94f" />

Then Per-Instance metrics

<img width="378" height="53" alt="image" src="https://github.com/user-attachments/assets/38659e1b-6e78-437d-8062-f8694611c790" />

Selected the CPUUtilization

<img width="560" height="35" alt="image" src="https://github.com/user-attachments/assets/bf9811a0-2944-45dd-9579-eeedb56033b0" />

<img width="637" height="480" alt="image" src="https://github.com/user-attachments/assets/6e582087-45bf-42ff-9b50-3581277e9750" />

And I chose these options:

<img width="625" height="408" alt="image" src="https://github.com/user-attachments/assets/8bc120d7-8397-4e49-8674-2a084a7d37dc" />

<img width="653" height="626" alt="image" src="https://github.com/user-attachments/assets/e85baedd-5574-422c-b9e8-4cfca25a4aaa" />

And lastly the name and create alarm:

<img width="186" height="70" alt="image" src="https://github.com/user-attachments/assets/caedb93d-6845-432e-9cca-75954d12842d" />

<img width="692" height="40" alt="image" src="https://github.com/user-attachments/assets/d797123d-3ced-4823-9342-1deaaed87596" />

Went to confirm the email to SNS notifications.

<img width="511" height="149" alt="image" src="https://github.com/user-attachments/assets/2aa346d0-ac75-466a-9b90-b227b3924a92" />

Now was time to test the alarm with command:

<img width="859" height="182" alt="image" src="https://github.com/user-attachments/assets/d3f95594-2590-4a32-8327-90d6b7834a1c" />



## 6. Conclusions

### References

Cloud Operation AWS course Moodle page

AWS CLI Command Reference: https://docs.aws.amazon.com/cli/latest/











