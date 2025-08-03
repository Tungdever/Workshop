---
title : "Clusterctl (CLI for Cluster API)"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 2.2.4 </b> "
---

clusterawsadm provides helpers for bootstrapping Kubernetes Cluster API Provider AWS. Use clusterawsadm to view required AWS Identity and Access Management (IAM) policies as JSON docs, or create IAM roles and instance profiles automatically using AWS CloudFormation.

clusterawsadm additionally helps provide credentials for use with clusterctl.

## Install

1. **Install clusterawsadm on Windows using PowerShell**  
   - Go to the working directory where you want clusterctl downloaded.
   - Download the latest release; on Windows, type: 
   ```
   curl.exe -L https://github.com/kubernetes-sigs/cluster-api/releases/download/v1.10.4/clusterctl-windows-amd64.exe -o clusterctl.exe
   $release = Invoke-RestMethod -Uri "https://api.github.com/repos/kubernetes-sigs/cluster-api-provider-aws/releases/latest"
   $downloadUrl = $release.assets | Where-Object { $_.name -eq "clusterawsadm-windows-amd64.exe" } | Select-Object -ExpandProperty browser_download_url
   $outputPath = "$env:USERPROFILE\Downloads\clusterawsadm.exe"
   Invoke-WebRequest -Uri $downloadUrl -OutFile $outputPath
   ```
2. **Add to PATH Environment Variable**  
   - Open **Edit the system environment variables** on Windows.  
   - In **System Variables**, find and edit the **Path** variable.  
   - Add the directory containing **clusterawsadm.exe** (`C:\Program Files\clusterawsadm`).  
   - Save changes.

![clusterawsadm](/images/2.prerequisite/042-prepare.png)
## Verify Installation
   Test to ensure the version you installed is up-to-date:
   ```
   clusterawsadm version
   ```
![clusterawsadm](/images/2.prerequisite/043-prepare.png)

