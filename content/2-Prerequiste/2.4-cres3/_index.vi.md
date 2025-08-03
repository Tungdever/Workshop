---
title: "Tạo S3 Bucket"
date: "2025-07-29"
weight: 4
chapter: false
pre: " <b> 2.4 </b> "
---

**Create an S3 Bucket for Cluster API State**:
   - Tạo một S3 bucket để lưu Cluster API state:
     ```
     aws s3 mb s3://capi-workshop-state --region us-east-1
     ```
   - **Kiểm tra**:
     ```
     aws s3 ls s3://capi-workshop-state
     ```

{{% notice info %}}
Tên bucket phải là duy nhất. Nếu `capi-workshop-state` đã tồn tại, hãy thêm tiền tố.
{{% /notice %}}

