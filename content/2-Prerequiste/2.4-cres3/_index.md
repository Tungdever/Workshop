---
title : "Create S3 Bucket"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---

**Create an S3 Bucket for Cluster API State**:
   - Create an S3 bucket to store Cluster API state:
     ```
     aws s3 mb s3://capi-workshop-state --region us-east-1
     ```
   - **Verify**:
     ```
     aws s3 ls s3://capi-workshop-state
     ```
![vpc](/images/2.prerequisite/033-vpc.png)
![vpc](/images/2.prerequisite/034-vpc.png)

{{% notice info %}}
The bucket name must be globally unique. If `capi-workshop-state` is taken, append a unique suffix.
{{% /notice %}}
