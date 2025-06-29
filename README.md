🚀Title: Configure VPC Flow Logs and Store Logs in S3 Using IAM Role
🔗 🔍Project Objective
  Set up VPC Flow Logs for a specified VPC and configure the log delivery to an Amazon S3 bucket using an IAM role with the required permissions.

🚀 Steps-

🔗 1 use dfault vpc.

 Go to VPC Console → Select an existing VPC.

 Note down the VPC ID, Region, and Account ID.


🔗 2: Create an S3 Bucket for Logs
   
  Open the S3 Console → Click Create bucket.

Name the bucket: vpc-flow-logs-bucket-<unique-id> 

 Uncheck "Block all public access" (S3 will still be private unless bucket policy allows otherwise)

 Enable Bucket Versioning (optional but good for log retention)

 Click Create bucket


🔗  3: Bucket Policy 

Replace placeholders (<ACCOUNT_ID>, <BUCKET_NAME>, <REGION>) accordingly:

📌 This policy ensures that only the VPC Flow Logs service can write logs from your account into your S3 bucket.


🔗 4: Create IAM Role for VPC Flow Logs

 Go to IAM Console → Roles → Click Create role

  Trusted entity type: AWS Service

  Use case: Select EC2 

   Click Next: Permissions


🔗  5: Custom IAM Policy (Attach this to the role)

📌 You can limit the role further with conditions, but this allows writing logs to the specified S3 prefix.

Name the role: VPCFlowLogsToS3Role

  Click Create Role


🔗  6: Update Trust Relationship

Modify the trust policy to allow VPC Flow Logs service to assume this role:

📌This policy is used to modify the trust policy to allow the flow logs service.


🔗 7: Enable VPC Flow Logs

Go to VPC Console → Your VPCs → Select your VPC
    In the lower panel, go to Flow Logs tab → Create flow log

Required Settings:

  Filter: All 

 Destination: Send to an S3 bucket

   Destination S3 bucket ARN:

   arn:aws:s3:::<BUCKET_NAME>/<ACCOUNT_ID>/AWSLogs/<ACCOUNT_ID>/

 IAM Role: Select the role VPCFlowLogsToS3Role

  Log format: Use default 


🔗  8: Generate and Verify Logs

  Launch or use existing EC2 instances in that VPC.
Generate some network activity (ping,ssh).

 Go to your S3 Bucket and navigate to the path:
s3://<BUCKET_NAME>/<ACCOUNT_ID>/AWSLogs/<ACCOUNT_ID>/vpcflowlogs/<region>/<vpc-id>/...


🔗 9: ping ICMP Traffic (Internet Control Message Protocol)
    *Basic Troubleshooting Tool:
     Ping is often the first step to check if a server or device is alive and responding before moving to deeper diagnostics.
     ping google.com (Helps you identify where problems may be happening.)
     ping -c 5 google.com (Helps in automated scripts or logs where you want a fixed amount of data).


🔗 10: Download a log file.

✅ A default flow log line looks like:

version account-id interface-id srcaddr dstaddr srcport dstport protocol packets bytes start end action log-status

✅ How to navigate and download a VPC Flow Logs file from S3.
   Find your S3 Bucket:
  In the Buckets list, click on your bucket name: <BUCKET_NAME>

 ✅  Click on the folder named with your Account ID.
Then open the AWSLogs folder.
   Inside, again open the folder named with your Account ID.
 Next, open the vpcflowlogs folder.
 Then select the folder for your specific region.
  Inside, open the folder for your VPC ID.
 You will see folders sorted by date and time for the flow logs.

 ✅  Download the log file:
 Find the log file (usually .log.gz or .txt).

   Select the file by checking the box next to it.
    Click the Download button (top right).




