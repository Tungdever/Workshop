---
title : "Kubectl (CLI for Kubernet)"
date : "2025-07-29"
weight : 1
chapter : false
pre : " <b> 2.2.1 </b> "
---

Kubernetes provides a command line tool for communicating with a Kubernetes cluster's control plane, using the Kubernetes API. This tool is named kubectl

## Install

- Go to [Download Binary](https://dl.k8s.io/v1.33.3/bin/windows/amd64/kubectl.exe)
- Move to a PATH directory: Move **kubectl.exe** to a directory like `C:\Program Files\kubectl` or another directory in PATH
- Add new PATH in Edit environment variable to use kubectl from anywhere

![kublectl](/images/2.prerequisite/010-kubectl.png)

## Verify installation
    kubectl version --client 

![kublectl](/images/2.prerequisite/011-kubectl.png)