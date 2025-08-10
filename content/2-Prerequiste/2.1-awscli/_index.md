---
title : "Install and configure AWS CLI"
date : "2025-07-29"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

### 1. Install
- **Run the following commands:**
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

### 2. Configuration
- **Run configuration command**:
``` 
aws configure 
```
Enter the following:

- **AWS Access Key ID**: Obtain from AWS IAM
1. **Sign in to the AWS Management Console**:
   - Navigate to https://aws.amazon.com/console/.
   - Log in with your AWS account credentials.
2. **Navigate to IAM**:
   - In the AWS Management Console, search for **IAM**.
   - Select **IAM** from the services list to open the IAM dashboard.

![CLI](/images/2.prerequisite/002-awscli.png)
3. **Select an IAM User**:
   - In the dashboard menu, click **Users**.
   - Click on the IAM user.

![CLI](/images/2.prerequisite/003-awscli.png)
4. **Create Access Key**:
   - Click the **Security credentials** tab.
   - Click **Create access key**.

![CLI](/images/2.prerequisite/004-awscli.png)
   - Select the use case (e.g., **Command Line Interface (CLI)**).

![CLI](/images/2.prerequisite/005-awscli.png)
   - Add an optional description tag for the key (e.g., `workshop-key`).
   - Click **Create access key**.

![CLI](/images/2.prerequisite/006-awscli.png)
   - On the next screen, you’ll see:
     - **Access Key ID**.
     - **Secret Access Key**.

![CLI](/images/2.prerequisite/007-awscli.png)
{{% notice info%}}
Download the `.csv` file containing these credentials or copy them to a secure location. The Secret Access Key is only shown once and cannot be retrieved later.
{{% /notice %}}

- **AWS Secret Access Key**: Import from CSV file.
- **Default region name**: E.g., `us-east-1`.
- **Default output format**: Enter `json` or press Enter to use the default.

![CLI](/images/2.prerequisite/008-awscli.png)

The configuration is saved in: ``%USERPROFILE%\.aws\credentials`` and ``%USERPROFILE%\.aws\config``

Test the configuration by running:

```
aws sts get-caller-identity
```  
![CLI](/images/2.prerequisite/009-awscli.png)
