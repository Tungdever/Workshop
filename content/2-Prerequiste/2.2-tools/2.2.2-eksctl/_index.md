---
title : "Eksctl (CLI for Amazon EKS)"
date : "2025-07-29"
weight : 2
chapter : false
pre : " <b> 2.2.2 </b> "
---

eksctl is a command-line tool for creating and managing Kubernetes clusters on Amazon EKS. It simplifies cluster management tasks using the AWS API.

## Install

1. **Download the Binary**  
   - Go to [eksctl Releases](https://github.com/weaveworks/eksctl/releases).  
   - Download the latest version for Windows (`eksctl_Windows_amd64.zip` for 64-bit).  
   - Extract the ZIP file to obtain **eksctl.exe**.

2. **Move to a PATH Directory**  
   - Move **eksctl.exe** to a directory included in your system's PATH, such as `C:\Program Files\eksctl`.  
   - Create the directory if it does not exist.

3. **Add to PATH Environment Variable**  
   - Open **Edit the system environment variables** on Windows.  
   - In **System Variables**, find and edit the **Path** variable.  
   - Add the directory containing **eksctl.exe** (`C:\Program Files\eksctl`).  
   - Save changes.

![eksctl](/images/2.prerequisite/012-eksctl.png)

## Verify installation 
   Run the following command in Command Prompt or PowerShell:  
   ```
   eksctl version
   ```

![eksctl](/images/2.prerequisite/013-eksctl.png)
