---
title : "Eksctl (CLI for Amazon EKS)"
date : "2025-07-29"
weight : 2
chapter : false
pre : " <b> 2.2.2 </b> "
---

eksctl is a command-line tool for creating and managing Kubernetes clusters on Amazon EKS. It simplifies cluster management tasks using the AWS API.

## Install

- **Run the following commands:**
```
curl --silent --location "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin/
sudo chmod +x /usr/local/bin/eksctl
```
## Verify installation 
   Run the following command in Command Prompt or PowerShell:  
   ```
   eksctl version
   ```
