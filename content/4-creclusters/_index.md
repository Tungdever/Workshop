---
title : "Create Workload Clusters"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

Workload clusters are managed by the management cluster using Cluster API.

1. **Create ClusterClass Definition**:
- Create a file called `cluster-class.yaml`:

   ```
   apiVersion: cluster.x-k8s.io/v1beta1
   kind: ClusterClass
   metadata:
   name: aws-eks-clusterclass
   namespace: default
   spec:
   controlPlane:
      ref:
         apiVersion: controlplane.cluster.x-k8s.io/v1beta2
         kind: AWSManagedControlPlaneTemplate
         name: aws-eks-control-plane-template
      machineInfrastructure:
         ref:
         apiVersion: infrastructure.cluster.x-k8s.io/v1beta2
         kind: AWSManagedControlPlaneTemplate
         name: aws-eks-control-plane-template
   infrastructure:
      ref:
         apiVersion: infrastructure.cluster.x-k8s.io/v1beta2
         kind: AWSClusterTemplate
         name: aws-cluster-template
   workers:
      machineDeployments:
      - class: default-worker
         template:
         bootstrap:
            ref:
               apiVersion: bootstrap.cluster.x-k8s.io/v1beta2
               kind: EKSConfigTemplate
               name: aws-eks-bootstrap-template
         infrastructure:
            ref:
               apiVersion: infrastructure.cluster.x-k8s.io/v1beta2
               kind: AWSMachineTemplate
               name: aws-machine-template
   ---
   apiVersion: controlplane.cluster.x-k8s.io/v1beta2
   kind: AWSManagedControlPlaneTemplate
   metadata:
   name: aws-eks-control-plane-template
   namespace: default
   spec:
   template:
      spec:
         region: us-east-1
         version: "1.32"
         eksClusterName: "{{.cluster.name}}"
         endpointAccess:
         public: true
         private: false
   ---
   apiVersion: infrastructure.cluster.x-k8s.io/v1beta2
   kind: AWSClusterTemplate
   metadata:
   name: aws-cluster-template
   namespace: default
   spec:
   template:
      spec:
         region: us-east-1
         sshKeyName: "default"
   ---
   apiVersion: infrastructure.cluster.x-k8s.io/v1beta2
   kind: AWSMachineTemplate
   metadata:
   name: aws-machine-template
   namespace: default
   spec:
   template:
      spec:
         instanceType: t3.medium
         iamInstanceProfile: "nodes.cluster-api-provider-aws.sigs.k8s.io"
         sshKeyName: "default"
   ---
   apiVersion: bootstrap.cluster.x-k8s.io/v1beta2
   kind: EKSConfigTemplate
   metadata:
   name: aws-eks-bootstrap-template
   namespace: default
   spec:
   template:
      spec: {}
   ```
2. **Apply ClusterClass**:

   ```
   kubectl apply -f cluster-class.yaml
   ```

4. **Generate and Deploy Workload Cluster**:

- Generate Cluster Configuration:

   ```
   $CLUSTER_NAME=workload-cluster-1
   $KUBERNETES_VERSION=v1.28.0
   $AWS_CONTROL_PLANE_MACHINE_TYPE=t3.medium
   $AWS_NODE_MACHINE_TYPE=t3.medium
   clusterctl generate cluster ${CLUSTER_NAME} --kubernetes-version ${KUBERNETES_VERSION} --control-plane-machine-count=1 --worker-machine-count=3 > ${CLUSTER_NAME}.yaml
   ```

4. **Deploy Workload Cluster**:

   ```
   kubectl apply -f ${CLUSTER_NAME}.yaml
   kubectl get cluster
   kubectl get awscluster
   kubectl get awsmanagedcontrolplane
   ```

5. **Deploy Workload Cluster**:
   ```
   kubectl get clusters

   clusterctl describe cluster capa-workload-cluster
   ```

6. **Access Workload Cluster**:
   - Retrieve kubeconfig for the workload cluster:
     ```
     clusterctl get kubeconfig workload-cluster-1 > workload-cluster-1.kubeconfig
     ```
   - Test access:
     ```
     kubectl --kubeconfig=workload-cluster-1.kubeconfig get nodes
     ```

7. **Repeat for Additional Clusters**:
   - Generate and apply manifests for `workload-cluster-2` in a different AWS region (e.g., `us-east-1`) for multi-region setup.