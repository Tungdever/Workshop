---
title : "Set Up Cross-Cluster Networking"
date : "2025-07-29"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

Enable communication between clusters using AWS VPC peering or a service mesh like Istio.

1. **Create VPC Peering**:
   - Create a peering connection between the management cluster and workload clusters:
     ```
     aws ec2 create-vpc-peering-connection --vpc-id <management-vpc-id> --peer-vpc-id <workload-vpc-id> --region us-east-1
     ```
   - Accept the peering connection:
     ```
     aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id <peering-id> --region us-east-1
     ```
   - Update route tables to allow traffic between VPCs.

2. **Install Istio for Service Mesh**:
   - Install Istio on the management cluster:
     ```
     istioctl install --set profile=demo -y
     ```
   - Deploy Istio on workload clusters and configure cross-cluster service discovery:
     ```
     kubectl --kubeconfig=workload-cluster-1.kubeconfig apply -f istio-multicluster.yaml
     ```

3. **Verify Cross-Cluster Communication**:
   - Deploy a sample application (e.g., `httpbin`) on `workload-cluster-1` and access it from `workload-cluster-2` using Istio service routing.
