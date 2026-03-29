# Configure VPC Flow Logs and Store Logs in S3 Using IAM Role

## Project Overview

Amazon VPC Flow Logs capture information about the IP traffic going to and from network interfaces in a VPC. By storing these logs in Amazon S3, we can perform long-term analysis, security audits, and performance monitoring.

#### This project demonstrates:

- Setting up VPC Flow Logs

- Configuring S3 bucket for log storage

- Creating an IAM role with least privilege

- Verification of logs and understanding log structure

### Objectives

- Capture all network traffic in a VPC

- Securely store VPC Flow Logs in Amazon S3

- Implement IAM policies for access control

- Understand log structure for auditing purposes

## Prerequisites

Before starting, ensure you have:

- An active AWS account

- Permissions to create: VPC, S3 bucket, IAM roles

- EC2 instances to generate traffic (for verification)

- AWS CLI configured (optional, for testing)

## Architecture

     ┌─────────────────────┐
     │      EC2 Instances  │
     │   Generate Traffic  │
     └─────────┬───────────┘
               │
               ▼
     ┌─────────────────────┐
     │    VPC Flow Logs    │
     │  Captures Traffic   │
     └─────────┬───────────┘
               │
               ▼
     ┌─────────────────────┐
     │     IAM Role        │
     │ Grants S3 Access    │
     └─────────┬───────────┘
               │
               ▼
     ┌─────────────────────┐
     │       S3 Bucket     │
     │ Stores Logs Securely│
     └─────────────────────┘

![](./Img/Architecture.png)

## Step 1: Identify or Create VPC

Navigate to AWS VPC Console → Your VPCs

Use an existing VPC or click Create VPC

Note VPC ID (e.g., vpc-03c431cfd4d3d0caf)

Best Practice: Use descriptive names for VPCs to track multiple environments.

![](./Img/Vpc.png)

## Step 2: Configure S3 Bucket for Logs

1. Go to S3 Console → Create bucket

2. Name: vpc-flow-logs-bucket

3. Choose region same as VPC for low latency

4. Enable Versioning (optional, recommended for auditing)

5. Configure Bucket Policy for Flow Logs

![](./Img/bucket%20policy.png)

## Step 3: Create IAM Role for VPC Flow Logs

1. Open IAM Console → Roles → Create Role

2. Select AWS Service → VPC Flow Logs

3. Attach Policy for S3 PutObject

### IAM Role Policy:

    {
     "Version": "2012-10-17",
     "Statement": [
       { "Effect": "Allow",  "Action":"s3:PutObject"       "Resource": "arn:aws:s3:::vpc-flow-logs-bucket/*" }
     ]
    }

### Trust Relationship:

    {
     "Version": "2012-10-17",
     "Statement": [
        { "Effect": "Allow", "Principal": { "Service": "vpc-flow-logs.amazonaws.com" }, "Action": "sts:AssumeRole" }
      ]
    }

![](./Img/Iam%20policy.png)

## Step 4: Enable VPC Flow Logs

1. Navigate to VPC → Flow Logs → Create Flow Log

2. Configure:

- Filter / Traffic Type: All (captures accepted + rejected traffic)

- Resource Type: VPC / Subnet / ENI

- Destination: S3 Bucket (vpc-flow-logs-bucket)

- IAM Role: Role created above

3. Click Create

## Step 5: Generate Network Traffic

-  Launch EC2 instances in the VPC

-  Ping other instances, SSH, or access web servers

- This generates logs that appear in S3

![](./Img/trafic.png)

## Step 6: Verify Logs in S3

Navigate to S3 bucket path:

s3://vpc-flow-logs-bucket/account-id/region/vpc-id/

Open a sample log file

![](./Img/bucket%20logs.png)

## Folder Uploaded

![](./Img/object%20uploded.png)

## Best Practices and Security Considerations

- IAM Least Privilege – Only allow Flow Logs to write to target bucket

- Encryption – Enable SSE-S3 or SSE-KMS for sensitive logs

- Structured Paths – Helps in organizing logs across multiple accounts/VPCs

- Retention Policies – Configure S3 lifecycle rules to archive or delete old logs

- Traffic Type – All gives complete insight; use Reject only for troubleshooting rejected traffic

## Troubleshooting

- Logs not appearing: Check IAM role trust relationship

- Access denied: Verify S3 bucket policy and permissions

- Delayed logs: Flow Logs delivery can take a few minutes


## Deliverables

- IAM Role JSON Policy & Trust Relationship – Included above

- S3 Bucket Policy JSON – Included above

- Flow Log Configuration Screenshot – Placeholder

- S3 Bucket Logs Screenshot – Placeholder

- Sample Log Line Explanation – Included above

## Conclusion

In this project, we successfully implemented AWS VPC Flow Logs to capture and analyze network traffic within a VPC. By configuring an IAM role with least-privilege permissions and delivering logs to an Amazon S3 bucket, we achieved a secure and centralized logging solution for auditing, troubleshooting, and monitoring purposes.

## Author 
 mansi kadam

 mansikadam11000@gmail.com

Github: https://github.com/mansikadam1100

