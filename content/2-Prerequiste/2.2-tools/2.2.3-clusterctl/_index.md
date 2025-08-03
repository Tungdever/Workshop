---
title : "Clusterctl (CLI for Cluster API)"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 2.2.3 </b> "
---

clusterctl is a command-line tool for managing Kubernetes clusters using the Cluster API. It simplifies the creation, upgrade, and deletion of clusters by interacting with Cluster API providers.

## Install

1. **Install clusterctl binary with curl on Windows using PowerShell**  
   - Go to the working directory where you want clusterctl downloaded.
   - Download the latest release; on Windows, type: 
   ```
   curl.exe -L https://github.com/kubernetes-sigs/cluster-api/releases/download/v1.10.4/clusterctl-windows-amd64.exe -o clusterctl.exe
   ```
![clusterctl](/images/2.prerequisite/014-clusterctl.png)
2. **Add to PATH Environment Variable**  
   - Open **Edit the system environment variables** on Windows.  
   - In **System Variables**, find and edit the **Path** variable.  
   - Add the directory containing **clusterctl.exe** (`C:\Program Files\clusterctl`).  
   - Save changes.

![clusterctl](/images/2.prerequisite/015-clusterctl.png)
## Verify Installation
   Test to ensure the version you installed is up-to-date:
   ```
   clusterctl version
   ```
![clusterctl](/images/2.prerequisite/016-clusterctl.png)

