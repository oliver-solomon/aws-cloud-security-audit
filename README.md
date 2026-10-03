# AWS Cloud Security Audit — ScoutSuite Misconfiguration Assessment.
AWS cloud security project using ScoutSuite to identify, investigate, and remediate security misconfigurations.

### Step 1: Building the AWS Environment 

I created a small AWS environment to simulate a real cloud setup for the ScoutSuite security assessment.

### Network Architecture
I created a VPC with separate public and private subnets, each using its own route table. The public subnet routes internet traffic through an Internet Gateway, while the private subnet has no direct route to the internet.

A NAT Gateway could be added to allow resources in the private subnet to make outbound internet connections without allowing unsolicited inbound connections. I left this out of the lab to avoid unnecessary AWS costs.

<img width="1285" height="343" alt="Screenshot 2026-10-03 at 10 33 13 am" src="https://github.com/user-attachments/assets/fcbe76c0-c407-49bc-827f-499abcc6553e" />

### EC2 and Network Security

I deployed an EC2 instance in the public subnet and configured a security group to control inbound and outbound network traffic.

S3 Storage
I created an S3 bucket with test objects to give ScoutSuite additional AWS resources to assess. Public access was blocked to keep the bucket privat


### Implementing Least Privilege 

Created a dedicated IAM user for ScoutSuite and attached the AWS-managed SecurityAudit policy, providing security-audit permissions without granting unnecessary administrator access. This follows the principle of least privilege.

<img width="1863" height="844" alt="AWS IAM ScoutSuite Permissions Dashboard" src="https://github.com/user-attachments/assets/a24b9769-1c43-4988-80bb-463472dbf306" />
