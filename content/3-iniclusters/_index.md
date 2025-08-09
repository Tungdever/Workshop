---
title : "Initialize Cluster API Management Cluster"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
### 1. Fork the git repo
Fork the Git repository `https://github.com/aws-samples/eks-ec2-clusterapi-gitops` to your own Github account, clone to your local workstation, then change to the following directory
```
cd ./eks-ec2-clusterapi-gitops
```
### 2. Initialize the management cluster
Create the kind cluster and verify the success of the creation:
```
kind create cluster
kubectl cluster-info --context kind-kind
```

The Cluster API Provider for AWS includes clusterawsadm, a tool to manage IAM objects. It uses environment variables, encodes them into a Kubernetes Secret in a Kind cluster, and retrieves necessary permissions to create workload clusters.
```
clusterawsadm bootstrap iam create-cloudformation-stack --region us-east-1
```

### 3. Predefine all necessary environment parameters
```
export AWS_B64ENCODED_CREDENTIALS=$(clusterawsadm bootstrap credentials encode-as-profile)
export AWS_REGION=us-east-1
export EKS=true
export EXP_MACHINE_POOL=true
export CAPA_EKS_IAM=true
```

### 4. Initialize Cluster API with AWS provider
To install the Cluster API components for AWS, the kubeadm boostrap provider, and the kubeadm control-plane provider, and transform the Kind cluster into a management cluster, we use the clusterctl init command.
```
clusterctl init --infrastructure aws
```



