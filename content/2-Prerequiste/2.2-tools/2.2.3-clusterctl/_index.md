---
title : "Clusterctl (CLI for Cluster API)"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 2.2.3 </b> "
---

clusterctl is a command-line tool for managing Kubernetes clusters using the Cluster API. It simplifies the creation, upgrade, and deletion of clusters by interacting with Cluster API providers.

## Install

- **Run the following commands:**
```
curl -L https://github.com/kubernetes-sigs/cluster-api/releases/download/v1.9.3/clusterctl-linux-amd64 -o clusterctl
chmod +x clusterctl
sudo install -o root -g root -m 0755 clusterctl /usr/local/bin/clusterctl
```
## Verify Installation
   Test to ensure the version you installed is up-to-date:
   ```
   clusterctl version
   ```
