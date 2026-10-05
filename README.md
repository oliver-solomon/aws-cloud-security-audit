# AWS Cloud Security Audit — ScoutSuite Misconfiguration Assessment.
AWS cloud security project using ScoutSuite to identify, investigate, and remediate security misconfigurations.

This lab focuses on three test cases: SSH open to the internet, an IAM user without MFA, and disabled S3 Block Public Access settings. The remediation and validation below cover these three cases, not every finding in the AWS account.

### Step 1: Building the AWS Environment 

I created a small AWS environment to simulate a real cloud setup for the ScoutSuite security assessment.

### Network Architecture
I created a VPC with separate public and private subnets, each using its own route table. The public subnet routes internet traffic through an Internet Gateway, while the private subnet has no direct route to the internet.

A NAT Gateway could be added to allow resources in the private subnet to make outbound internet connections without allowing unsolicited inbound connections. I left this out of the lab to avoid unnecessary AWS costs.

<img width="1285" alt="AWS network architecture with resource identifier redacted" src="images/network-architecture-redacted.png" />

### EC2 and Network Security

I deployed an EC2 instance in the public subnet and configured a security group to control inbound and outbound network traffic.

<img width="1399" alt="EC2 instance details with account and resource identifiers redacted" src="images/ec2-instance-redacted.png" />

Inbound rules 

<img width="610" height="87" alt="Screenshot 2026-10-03 at 12 08 57 pm" src="https://github.com/user-attachments/assets/a28145cc-6d26-4e41-bfef-205488daf0fe" />

Outbound rules 

<img width="610" height="87" alt="Screenshot 2026-10-03 at 12 09 23 pm" src="https://github.com/user-attachments/assets/60f9f122-10a8-4059-b1f4-1aeb5333622c" />

### S3 Storage

I created an S3 bucket with test objects to give ScoutSuite additional AWS resources to assess. Public access was blocked to keep the bucket private.

<img width="704" height="214" alt="Screenshot 2026-10-03 at 12 11 42 pm" src="https://github.com/user-attachments/assets/813faa6e-118c-4ba3-84c9-5e42270ee0f3" />

### ScoutSuite IAM Access

I created a dedicated IAM user for ScoutSuite and attached the AWS-managed SecurityAudit policy, allowing ScoutSuite to inspect the AWS environment without granting administrator access

<img width="1863" height="844" alt="AWS IAM ScoutSuite Permissions Dashboard" src="https://github.com/user-attachments/assets/a24b9769-1c43-4988-80bb-463472dbf306" />

### Step 2: Run a Baseline ScoutSuite Assessment 

Before introducing any test misconfigurations, I ran ScoutSuite to assess the current security state of my AWS environment and establish a baseline for later comparison.

**Running the Baseline ScoutSuite Assessment**

<img width="831" height="469" alt="Screenshot 2026-10-03 at 9 16 52 pm" src="https://github.com/user-attachments/assets/96d59ba8-f611-4f68-94f8-50a72c3bd67e" />

**Baseline ScoutSuite Assessment Results**

<img width="569" height="701" alt="Screenshot 2026-10-03 at 9 32 31 pm" src="https://github.com/user-attachments/assets/cd731b4c-dc52-429f-9329-5d67baabdc80" />

The baseline assessment identified existing findings across EC2, IAM, S3, VPC and other AWS services. These results provide a reference point before introducing controlled test misconfigurations.

### Step 3: Introduce Test Misconfigurations

Test Misconfiguration 1 - Overly Permissive SSH 
I intentionally changed the EC2 security group's SSH rule from a single trusted /32 address to 0.0.0.0/0. This exposes port 22 to connection attempts from any IPv4 address. This was introduced temporarily to test whether ScoutSuite would identify this issue.

<img width="954" alt="SSH test rule allowing all IPv4 sources with rule identifier redacted" src="images/ssh-test-rule-redacted.png" />

Test Misconfiguration 2 - IAM User Without MFA

I gave the test user read-only access to S3 rather than administrative permissions. I intentionally left MFA disabled to test whether ScoutSuite would identify it as a security issue.

<img width="1912" height="802" alt="iam-readonly-no-mfa-side-by-side" src="https://github.com/user-attachments/assets/ef981587-ad52-4c3b-9428-6a712a2e0ee7" />

Test Misconfiguration 3 - Disable S3 Block Public Access

I created an S3 bucket with the Block Public Access settings disabled. This was intentionally configured as a security misconfiguration to test whether ScoutSuite would identify the potential exposure.

<img width="463" height="566" alt="Screenshot 2026-10-04 at 11 11 23 pm" src="https://github.com/user-attachments/assets/c64a9d71-f4de-4650-8674-534b9b0853a9" />

### Step 4: Second ScoutSuite Scan

After intentionally introducing security misconfigurations into the AWS environment, I ran a second ScoutSuite scan to determine whether the security issues could be detected. I then reviewed the findings to identify the affected resources, understand the associated security risks, and determine appropriate remediation steps.

<img width="570" height="701" alt="Screenshot 2026-10-04 at 11 55 58 pm" src="https://github.com/user-attachments/assets/64a44e65-58bd-4d39-82ad-ce95590fe97f" />

The baseline and second scan show different numbers of resources and checks. For example, EC2 resources increased from 7 to 40 and EC2 checks from 101 to 593. The reason for this difference is not established in this write-up, so the overall finding totals are not a direct before-and-after comparison of the three test changes. The individual findings and validation results below are the evidence for each test case.

### Step 5: Remediate + Validate Findings
Finding 1: SSH Open To Internet

ScoutSuite detected that the public-ec2-sg security group allowed inbound SSH traffic on TCP port 22 from 0.0.0.0/0. This means any IPv4 address could attempt to connect to the EC2 instance over SSH.

<img width="854" alt="ScoutSuite SSH finding with account and resource identifiers redacted" src="images/ssh-finding-redacted.png" />

### Remediate Finding 1: 

I changed the SSH inbound rule from 0.0.0.0/0 to my public IP /32, restricting SSH access to my network. I kept the egress rule unchanged because security groups are stateful

<img width="610" height="87" alt="Screenshot 2026-10-03 at 12 08 57 pm" src="https://github.com/user-attachments/assets/a28145cc-6d26-4e41-bfef-205488daf0fe" />

## Finding 2: IAM Users Without MFA 

ScoutSuite detected that test--user-no-mfa had password access enabled without MFA. If the user's password were compromised, an attacker could access the account without an additional authentication factor.

<img width="542" alt="ScoutSuite IAM user without MFA with account identifier redacted" src="images/iam-no-mfa-redacted.png" />

### Remediate Finding 2: 
I enabled MFA for test--user-no-mfa, adding an additional authentication factor to secure the IAM user.

<img width="954" alt="IAM user with MFA enabled and account identifiers redacted" src="images/iam-mfa-enabled-redacted.png" />

## Finding 3: S3 Public Access Block Disabled

ScoutSuite detected that the S3 Public Access Block settings were disabled. This increases the risk of the bucket being accidentally exposed to the internet.

<img width="847" height="147" alt="Screenshot 2026-10-05 at 12 24 01 pm" src="https://github.com/user-attachments/assets/cbab3dc3-cb88-4ab5-a070-2eb096eea14c" />

## Remediate Finding 3: 

I re-enabled Block all public access to prevent the S3 bucket from accidentally being exposed publicly through ACLs or bucket policies.

<img width="708" height="264" alt="Screenshot 2026-10-05 at 12 27 40 pm" src="https://github.com/user-attachments/assets/02b42b2e-33d3-4c76-9977-6d5b4180e309" />

## Step 6: Final Validation Scan

After remediating the three test misconfigurations, I ran a final ScoutSuite validation scan to check the results.

## Validating Finding 1:

I reran ScoutSuite after remediation. The finding returned 0 rules flagged, confirming that SSH was no longer exposed to all source addresses.

<img width="1111" height="204" alt="Screenshot 2026-10-05 at 1 26 40 pm" src="https://github.com/user-attachments/assets/06ecb9b7-1e15-42c5-9356-bc388a415112" />

## Validating Finding 2:

I reran ScoutSuite after enabling MFA. The finding returned 0 users flagged, confirming that the IAM user was no longer detected as being without MFA.

<img width="1111" height="168" alt="Screenshot 2026-10-05 at 1 27 18 pm" src="https://github.com/user-attachments/assets/7ece8101-e91a-4380-84db-8b7e3dfb2929" />

## Validating Finding 3: 

I verified that Block all public access was enabled again, confirming that the public-access protection had been restored.

<img width="839" height="148" alt="Screenshot 2026-10-05 at 3 15 12 pm" src="https://github.com/user-attachments/assets/b7b4fb1e-c82f-4b98-8981-c2629e568c5a" />

## Additional Security Improvement: Root Account Usage 

During the project, I noticed that I had been using the AWS root account for administrative tasks. I improved the account's security by using a dedicated IAM administrator account instead and locking away the root account for tasks that specifically require it.

<img width="1954" height="805" alt="image" src="https://github.com/user-attachments/assets/b372841f-68b3-4eab-904c-9955086ed877" />

## Conclusion 

The project was about me building an AWS environment with a VPC, public/private subnets, route tables, an internet gateway, EC2 instance with a security group, S3 bucket, and IAM access for ScoutSuite. I purposely introduced misconfigurations into the environment to see whether ScoutSuite could detect them.

This project helped me understand not only how to build an AWS environment, but also how to investigate misconfigurations, remediate them, and validate the findings. It showed me that finding and fixing security issues in a cloud environment is not a one-step process.

Most importantly, this project improved my practical skills in investigating findings, fixing them, and validating that they were properly remediated. In future projects, I would like to go into more detail during investigations and explore more complex cloud security misconfigurations.
