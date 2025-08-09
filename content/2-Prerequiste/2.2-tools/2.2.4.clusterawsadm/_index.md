---
title : "Clusterawsadm"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 2.2.4 </b> "
---

clusterawsadm provides helpers for bootstrapping Kubernetes Cluster API Provider AWS. Use clusterawsadm to view required AWS Identity and Access Management (IAM) policies as JSON docs, or create IAM roles and instance profiles automatically using AWS CloudFormation.

clusterawsadm additionally helps provide credentials for use with clusterctl.

## Install
- **Run the following commands:**
```
curl -LO https://github.com/kubernetes-sigs/cluster-api-provider-aws/releases/download/v2.8.4/clusterawsadm-linux-amd64
chmod +x clusterawsadm-linux-amd64
sudo install -o root -g root -m 0755 clusterawsadm-linux-amd64 /usr/local/bin/clusterawsadm
```

## Verify Installation
   Test to ensure the version you installed is up-to-date:
   ```
   clusterawsadm version
   ```


