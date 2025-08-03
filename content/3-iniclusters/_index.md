---
title : "Initialize Cluster API Management Cluster"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
### 1. Deploy Management Cluster
Create AWS Identity and Access Management (IAM) roles for use with Kubernetes Cluster API Provider AWS.
  ```
  clusterawsadm bootstrap iam create-cloudformation-stack --region us-east-1
  ```

![clusterawsadm](/images/2.prerequisite/041-prepare.png)
The management cluster is an EKS cluster hosting Cluster API components to manage workload clusters.

- Create the management cluster with eksctl:

  ```
  eksctl create cluster --name management-cluster --region us-east-1 --node-type t3.medium --nodes 3 --nodes-min 1 --nodes-max 4 --managed 
  ```
- If you encounter a timeout error during cluster creation, try switching to public DNS:
  - Open Control Panel → Network and Sharing Center → Change adapter settings.
  - Right-click your active connection (Wi-Fi/Ethernet) → Properties.
  - Select Internet Protocol Version 4 (TCP/IPv4) → Properties.
  Set:
  - Preferred DNS: 8.8.8.8 (Google)
  - Alternate DNS: 8.8.4.4 (Google) or 1.1.1.1 (Cloudflare)

{{% notice info %}}
You may also try turning off Windows Firewall temporarily if DNS changes don’t resolve the issue. 
{{% /notice %}}

{{% notice info %}}
This command may take 10-15 minutes to complete. 
{{% /notice %}}

**After successful creation**

![inicluster](/images/2.prerequisite/019-inicluster.png)

**Verify witk EKS and CloudFormation**

![inicluster](/images/2.prerequisite/020-inicluster.png)
![inicluster](/images/2.prerequisite/021-inicluster.png)


**Configure kubectl**:

  After cluster creation, configure **kubectl** to connect to your cluster:
  ```
  aws eks update-kubeconfig --name management-cluster --region us-east-1
  ```
![inicluster](/images/2.prerequisite/037-inicluster.png)

**Verify Cluster**

  Test your cluster connection:
  ```
  # Check cluster nodes
  kubectl get nodes

  # Check system pods
  kubectl get pods --all-namespaces

  # Get cluster information
  kubectl cluster-info
  ```
### 2. Initialize Cluster API
Cluster API is a Kubernetes project providing declarative APIs for cluster lifecycle management. Here, we initialize Cluster API with the AWS provider.

- **Encode credentials for use with clusterctl init**

  ```
  $env:AWS_REGION = "us-east-1"
  $env:AWS_B64ENCODED_CREDENTIALS = clusterawsadm bootstrap credentials encode-as-profile
  ```
- **Initialize Cluster API with AWS provider**:

  ```
  clusterctl init --infrastructure aws
  ```
![inicluster](/images/2.prerequisite/038-inicluster.png)
### 3. Verify Initialization

- **Check pods in `capi-system` and `capa-system` namespace**:

```
# Check core Cluster API components
kubectl get pods -n capi-system

# Check AWS provider components
kubectl get pods -n capa-system

# Verify CRDs are installed
kubectl get crd | findstr cluster-api
```

![inicluster](/images/2.prerequisite/040-inicluster.png)
![inicluster](/images/2.prerequisite/039-inicluster.png)


