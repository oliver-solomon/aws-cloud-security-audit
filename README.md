# AWS Cloud Security Audit — ScoutSuite Misconfiguration Assessment.
AWS cloud security project using ScoutSuite to identify, investigate, and remediate security misconfigurations.

**Step 1: Creating AWS Environment**

Firstly, I would go to AWS cloud and create a VPC and add public subnets with EC2 instances and private subnets. I will also going to add route table for both public and private subnet so it knows where to go (public goes through internet, private goes through NAT gateway). The NAT gateway is located in public subnet and if private subnet wants to communicate outbound it needs to go through NAT gateway. Furthermore, I'm adding internet gateway for public subnet so that it can access the internet. 

### AWS Environment
