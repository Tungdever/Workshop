---
title : "Create EC2 Keypair"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

1. **Create the SSH key pair**:
   Run the following command to create an SSH key pair named `workshop-key` in the `us-east-1` region:
   ```
   aws ec2 create-key-pair --key-name workshop-key --query 'KeyMaterial' --output text > workshop-key.pem
   ```

2. **Verify the key pair in AWS Console**:
   - Sign in to the [AWS Management Console](https://aws.amazon.com/console/).
   - Navigate to **EC2** → **Key Pairs**.
   - Look for the `workshop-key` in the `us-east-1` region.

![keypair](/images/2.prerequisite/017-crekey.png)

