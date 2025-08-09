---
title : "Kubectl (CLI for Kubernet)"
date : "2025-07-29"
weight : 1
chapter : false
pre : " <b> 2.2.1 </b> "
---

Kubernetes provides a command line tool for communicating with a Kubernetes cluster's control plane, using the Kubernetes API. This tool is named kubectl

## Install
- **Run the following commands:**
```
curl -LO "https://dl.k8s.io/release/v1.33.3/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

## Verify installation
```
kubectl version --client 
```
