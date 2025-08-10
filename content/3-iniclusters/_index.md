---
title : "Initialize Cluster API Management Cluster"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
### 1️⃣ Fork and Clone the Git Repository

Start by forking the official sample repository [`eks-ec2-clusterapi-gitops`](https://github.com/aws-samples/eks-ec2-clusterapi-gitops) to your own GitHub account. Then, clone it to your local machine:

```
cd ./eks-ec2-clusterapi-gitops
```

This repository contains configuration files and manifests sample to help you deploy EKS clusters and Cluster EC2 using Cluster API and GitOps workflows.

---

### 2️⃣ Create the Management Cluster with Kind

Use Kind to create a Kubernetes cluster locally. This cluster will serve as your **management cluster**, which controls the lifecycle of other clusters:

```
kind create cluster
kubectl cluster-info --context kind-kind
```

The second command verifies that the cluster was created successfully and is accessible.

---

### 3️⃣ Bootstrap IAM Resources for AWS Provider

Cluster API for AWS requires specific IAM roles and policies to interact with AWS services. The `clusterawsadm` tool helps you create these resources via a CloudFormation stack:

```
clusterawsadm bootstrap iam create-cloudformation-stack --region us-east-1
```

This stack sets up the necessary permissions so that your management cluster can create and manage EKS workload clusters.

---

### 4️⃣ Set Required Environment Variables

Before initializing Cluster API, you need to define several environment variables that provide credentials and enable specific features:

```
export AWS_B64ENCODED_CREDENTIALS=$(clusterawsadm bootstrap credentials encode-as-profile)
export AWS_REGION=us-east-1
export EKS=true
export EXP_MACHINE_POOL=true
export CAPA_EKS_IAM=true
```

- `AWS_B64ENCODED_CREDENTIALS`: Encoded AWS credentials for Cluster API.
- `EKS=true`: Enables EKS support.
- `EXP_MACHINE_POOL=true`: Enables experimental support for MachinePools.
- `CAPA_EKS_IAM=true`: Enables IAM integration for EKS clusters.

---

### 5️⃣ Initialize Cluster API with AWS Infrastructure Provider

Now, use the `clusterctl init` command to install Cluster API components into your Kind cluster. This transforms it into a fully functional management cluster:

```
clusterctl init --infrastructure aws
```

This command installs:
- **Infrastructure provider**: `cluster-api-provider-aws`
- **Bootstrap provider**: `kubeadm`
- **Control plane provider**: `kubeadm`

Once completed, your management cluster is ready to create and manage EKS clusters on AWS using declarative APIs.

