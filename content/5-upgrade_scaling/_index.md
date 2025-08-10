---
title : "Automation Upgrade and Auto-scaling"
date : "2025-07-29"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
## Cluster Upgrades

Upgrading a Kubernetes cluster managed by Cluster API is a declarative process. You simply update the version fields in your cluster manifests and let Argo CD synchronize the changes automatically.

1. **Choose the target Kubernetes version**  
   For example: `v1.32.0`. Make sure the version is compatible with your infrastructure and supported by Cluster API.

2. **Update your cluster manifest files**

#### For EC2-based clusters (`aws-ec2.yaml`):

- Locate the `KubeadmControlPlane` and `MachineDeployment` resources in the manifest.
- Update the `version` field to the desired Kubernetes version.
- Ensure `MachineDeployment` uses a rolling update strategy to avoid downtime:
  ```
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  ```
  This configuration ensures that only one new node is added at a time, and no existing node is taken offline during the update.

- Commit and push the changes to your Git repository.
- Argo CD will detect the changes and apply them automatically. You can monitor the sync status in the Argo CD UI.

#### For EKS clusters (`capi-eks.yaml`):

- Locate the `AWSManagedControlPlane` resource.
- Update the `version` field to the new Kubernetes version.
- Commit and push the changes.
- Argo CD will synchronize the update and apply it to the EKS cluster.

{{% notice note %}}
Note: This upgrade method ensures consistency, traceability, and automation through GitOps.
{{% /notice %}}


---

## Cluster Autoscaling

Autoscaling allows your cluster to dynamically adjust the number of nodes based on resource usage, improving cost efficiency and performance.

### EC2-Based Cluster Autoscaling

#### 1. Create Cluster Autoscaler Deployment

Create a manifest file `management/cluster-autoscaler.yaml` that defines the Cluster Autoscaler deployment:

- Uses the official image: `registry.k8s.io/autoscaling/cluster-autoscaler:v1.32.0`
- Targets the EC2 node group: `--nodes=1:10:capi-ec2-md-0`
- Sets the cluster name: `--cluster-name=capi-ec2`
- Includes flags for balancing, logging, and safe eviction

This deployment enables the autoscaler to scale the node group between 1 and 10 nodes based on demand.

#### 2. Define RBAC Permissions

Create `management/cluster-autoscaler-rbac.yaml` to grant the autoscaler access to necessary resources:

- ServiceAccount: `cluster-autoscaler`
- ClusterRole: allows reading and watching pods, nodes, machine deployments, etc.
- ClusterRoleBinding: binds the role to the service account

Without these permissions, the autoscaler cannot monitor or modify cluster resources.

#### 3. Deploy via Argo CD

Create an Argo CD `Application` manifest (`management/argocd-autoscaler-app.yaml`) to automate deployment of the autoscaler and its RBAC configuration:

```
kubectl apply -f management/argocd-autoscaler-app.yaml
```

Once applied, Argo CD will manage the autoscaler and keep it in sync with your Git repository.

---

### EKS Cluster Autoscaling

For EKS clusters, autoscaling is typically handled via **Managed Node Groups**, which are natively supported by AWS.

#### Configure Managed Node Group

In your EKS manifest (`AWSManagedMachinePool`), define the scaling parameters:

```
spec:
  instanceType: t3.medium
  scaling:
    maxSize: 5
    minSize: 1
```

This configuration allows AWS to automatically adjust the number of nodes between 1 and 5 based on workload.

#### Alternative: Use Cluster Autoscaler for Unmanaged Node Groups

If you're using unmanaged node groups in EKS, you can deploy the Cluster Autoscaler manually using the same approach as the EC2 cluster.
