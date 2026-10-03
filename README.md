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

<img width="1609" height="978" alt="image" src="https://github.com/user-attachments/assets/1a1118e8-f3cd-4c6a-a3b1-3a638e1dd2f4" />

Inbound rules 

<img width="610" height="87" alt="Screenshot 2026-10-03 at 12 08 57 pm" src="https://github.com/user-attachments/assets/a28145cc-6d26-4e41-bfef-205488daf0fe" />

Outbound rules 

<img width="610" height="87" alt="Screenshot 2026-10-03 at 12 09 23 pm" src="https://github.com/user-attachments/assets/60f9f122-10a8-4059-b1f4-1aeb5333622c" />

S3 Storage

I created an S3 bucket with test objects to give ScoutSuite additional AWS resources to assess. Public access was blocked to keep the bucket private.

<img width="704" height="214" alt="Screenshot 2026-10-03 at 12 11 42 pm" src="https://github.com/user-attachments/assets/813faa6e-118c-4ba3-84c9-5e42270ee0f3" />

### ScoutSuite IAM Access

I created a dedicated IAM user for ScoutSuite and attached the AWS-managed SecurityAudit policy, allowing ScoutSuite to inspect the AWS environment without granting administrator access

<img width="1863" height="844" alt="AWS IAM ScoutSuite Permissions Dashboard" src="https://github.com/user-attachments/assets/a24b9769-1c43-4988-80bb-463472dbf306" />

### Step 2: Introduce Test Misconfigurations

### Step 3: Run ScoutSuite & Investigate Findings 

### Finding 1:

### Step 4: Remediate + Validate Findings
