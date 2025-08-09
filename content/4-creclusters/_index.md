---
title : "Create Workload Cluster"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
## Generate the Kubernetes Cluster Manifests
1. **Create the SSH key**:
```
aws ec2 create-key-pair --key-name capi-eks --region ap-southeast-2 --query 'KeyMaterial' --output text > capi-eks.pem
aws ec2 create-key-pair --key-name capi-ec2 --region us-east-1 --query 'KeyMaterial' --output text > capi-ec2.pem
```
2. **Define environment variables**:
```
export AWS_CONTROL_PLANE_MACHINE_TYPE=t3.medium
export AWS_NODE_MACHINE_TYPE=t3.medium
```

3. **Generate the Kubernetes Cluster Manifest for EC2**:

```
export AWS_SSH_KEY_NAME=capi-ec2 (Given we are deploying the EC2 cluster and EKS cluster in different regions)
export AWS_REGION=us-east-1 
clusterctl generate cluster capi-ec2 --kubernetes-version v1.31.0 --control-plane-machine-count=3 --worker-machine-count=3 > ./capi-cluster/aws-ec2/aws-ec2.yaml
```


4. **Generate the Kubernetes Cluster Manifest for EKS**:

```
export AWS_SSH_KEY_NAME=capi-eks
export AWS_REGION=ap-southeast-2
clusterctl generate cluster capi-eks --flavor eks-managedmachinepool --kubernetes-version v1.31.0 --worker-machine-count=3 > ./capi-cluster/aws-eks/capi-eks.yaml
```

## Install Argo CD on the management Kubernetes cluster

**install Argo CD in the Kind management cluster:**
```
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**Get the first login password to Argo CD Web UI:**
```
kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

**Set up Argo CD can be authenticated to Git**

- **Login Argo CD’s web portal:**

   ```
   kubectl port-forward svc/argocd-server 8080:80
   ```

  - Username: admin
  - password: `first login password`
 ![workload](/images/4.workload/001-workload.png) 

- Navigate setting > Repositories > Connect repo
  - Choose your connection method: HTTP/HTTPS
  - Type: Git
  - Repository URL: Your url fork repo
  - Select `skip server verification`
 ![workload](/images/4.workload/003-workload.png) 
 ![workload](/images/4.workload/002-workload.png) 
- Connect

## Create Workload Clusters
**To authorize Argo CD to manage Cluster API objects, create a ClusterRoleBinding to grant the necessary permissions to the argocd-application-controller ServiceAccount, using the cluster-admin role for simplicity.**
```
kubectl apply -f ./management/argo-cluster-role-binding.yaml
```

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-argocd-contoller
subjects:
  - kind: ServiceAccount
    name: argocd-application-controller
    namespace: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
```

### Create Clusters for K8S cluster that runs on EC2:

To create a new EKS cluster or a K8S cluster that runs on EC2, we can first define an application in a declarative approach, as a representation of a collection of Kubernetes manifests that makes up all the pieces to deploy the new cluster. In this guide, all of the configuration of the Argo CD application to be added is stored in “Management” folder of the cloned repository. For example, below is the application manifest file for creating a new K8S cluster that runs on EC2:
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: ec2-cluster
spec:
  destination:
    name: ''
    namespace: 'default'
    server: 'https://kubernetes.default.svc'
  source:
    path: capi-cluster/aws-ec2
    repoURL: '[update the Git Repo accordingly]' #Indicate which source repo for fetching the cluster configuration
    targetRevision: HEAD
  project: default #You can give a project name here
  syncPolicy:
    automated:
      prune: true
      allowEmpty: true
```
After modifying the Argo CD application yaml file as above, commit and push the updates to the source repo:
```
git push .
git add .
git commit -m “updates Argo CD app manifest file” 
```
Run the following command to create a new Application to create the K8S cluster that runs on EC2:
```
kubectl apply -f ./management/argocd-ec2-app.yaml
```

We will use an Argo CD application to provision the EKS cluster. First, create an application and update the relevant YAML file to set the repoURL to your Git repository. Then, commit and push the changes to the source repository.
```
kubectl apply -f ./management/argocd-eks-app.yaml
```

Check status of the clusters
```
clusterctl describe cluster capi-eks
clusterctl describe cluster capi-ec2
```

The EC2 cluster's worker nodes show a "False" READY status due to the absence of a CNI, preventing them from joining the cluster. For demonstration, deploy the Calico CNI to the EC2 cluster, which can be automated using Argo CD.

Fetch the kubeconfig file for the newly created cluster on EC2:

```
clusterctl get kubeconfig capi-ec2 > capikubeconfig-ec2
```
Deploy Calico CNI
```
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

Calico CNI is successfully deployed, check the cluster status again

When creating an EKS cluster, the system automatically generates and stores kubeconfig files as secrets within the management cluster. This behavior differs from creating a non-managed cluster using the AWS provider, where kubeconfigs are not stored in this way. The secret containing the kubeconfig will be named [cluster-name]-user-kubeconfig, where you should replace [cluster-name] with the actual name of your cluster.

To get the user kubeconfig for a cluster named ‘capi-eks’ you can run a command similar to:
```
kubectl --namespace=default get secret capi-eks-user-kubeconfig -o jsonpath={.data.value} | base64 --decode > capikubeconfig-eks
```

## Cluster Upgrades
1. **Choose the target Kubernetes version** (e.g., `v1.32.0`).
2. **Update your cluster manifest**:
- Open your EC2 manifest file `aws-ec2.yaml`.
   - Modify the `version` field in `KubeadmControlPlane` and `MachineDeployment`:
   ![workload](/images/4.workload/005-workload.png) 
   ![workload](/images/4.workload/006-workload.png) 
   
   - Ensure the MachineDeployment is configured to handle rolling updates
      ```
      spec:
      strategy:
         type: RollingUpdate
         rollingUpdate:
            maxSurge: 1
            maxUnavailable: 0
      ```
      ![workload](/images/4.workload/010-workload.png) 
   - Commit and push
   - Check in argo cd UI
   ![workload](/images/4.workload/007-workload.png) 

- Open your EC2 manifest file `capi-eks.yaml`.
   - Modify the `version` field in `AWSManagedControlPlane`:
   ![workload](/images/4.workload/008-workload.png) 
   - Commit and push
   - Check in argo cd UI
      ![workload](/images/4.workload/009-workload.png) 
---

## Cluster Autoscaling

Autoscaling allows your clusters to dynamically adjust resources based on workload demand.

### 1. EC2-Based Cluster Autoscaling
#### Manifest Cluster Autoscaler

Create file `management/cluster-autoscaler.yaml`:
   ```
   apiVersion: apps/v1
   kind: Deployment
   metadata:
   name: cluster-autoscaler
   namespace: kube-system
   labels:
      app: cluster-autoscaler
   spec:
   replicas: 1
   selector:
      matchLabels:
         app: cluster-autoscaler
   template:
      metadata:
         labels:
         app: cluster-autoscaler
         annotations:
         cluster-autoscaler.kubernetes.io/safe-to-evict: "false"
         prometheus.io/scrape: "true"
         prometheus.io/port: "8085"
      spec:
         serviceAccountName: cluster-autoscaler
         containers:
         - name: cluster-autoscaler
            image: registry.k8s.io/autoscaling/cluster-autoscaler:v1.32.0
            resources:
               limits:
               cpu: 100m
               memory: 300Mi
               requests:
               cpu: 100m
               memory: 300Mi
            command:
               - ./cluster-autoscaler
               - --cloud-provider=aws
               - --nodes=1:10:capi-ec2-md-0
               - --cluster-name=capi-ec2
               - --skip-nodes-with-local-storage=false
               - --balance-similar-node-groups
               - --expander=least-waste
               - --logtostderr=true
               - --v=4
            volumeMounts:
               - name: ssl-certs
               mountPath: /etc/ssl/certs/ca-certificates.crt
               readOnly: true
            securityContext:
               allowPrivilegeEscalation: false
               readOnlyRootFilesystem: true
               runAsNonRoot: true
               runAsUser: 65534
         volumes:
         - name: ssl-certs
            hostPath:
               path: /etc/ssl/certs/ca-certificates.crt
   ```
#### RBAC for autoscaler

Create file `management/cluster-autoscaler-rbac.yaml`:
   ```
   apiVersion: v1
   kind: ServiceAccount
   metadata:
   name: cluster-autoscaler
   namespace: kube-system
   ---
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRole
   metadata:
   name: cluster-autoscaler
   rules:
   - apiGroups: [""]
      resources: ["events", "endpoints", "pods", "services", "nodes", "namespaces"]
      verbs: ["get", "list", "watch"]
   - apiGroups: ["apps"]
      resources: ["replicasets", "statefulsets", "daemonsets"]
      verbs: ["get", "list", "watch"]
   - apiGroups: ["extensions"]
      resources: ["replicasets", "daemonsets"]
      verbs: ["get", "list", "watch"]
   - apiGroups: ["policy"]
      resources: ["poddisruptionbudgets"]
      verbs: ["get", "list"]
   - apiGroups: ["autoscaling.k8s.io"]
      resources: ["*"]
      verbs: ["*"]
   - apiGroups: ["cluster.x-k8s.io"]
      resources: ["machinedeployments", "machinesets", "machines"]
      verbs: ["get", "list", "watch", "patch"]
   ---
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
   name: cluster-autoscaler
   roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: cluster-autoscaler
   subjects:
   - kind: ServiceAccount
      name: cluster-autoscaler
      namespace: kube-system
   ```

#### Manifest Argo CD Application Cluster Autoscaler

Create file `management/argocd-autoscaler-app.yaml`:
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cluster-autoscaler
spec:
  project: default
  source:
    repoURL: https://github.com/Tungdever/multi-cluster-with-clusterapi
    targetRevision: HEAD
    path: management
  destination:
    server: https://kubernetes.default.svc
    namespace: kube-system
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=false
```

- Commit file `cluster-autoscaler.yaml`, `cluster-autoscaler-rbac.yaml`, and `argocd-autoscaler-app.yaml`
- Push on repo
- Apply Argo CD Application:
   ```
   kubectl apply -f management/argocd-autoscaler-app.yaml
   ```

![workload](/images/4.workload/011-workload.png) 
![workload](/images/4.workload/012-workload.png) 

#### EKS Cluster Autoscaling

- **Use managed node groups** with autoscaling parameters:

```
apiVersion: infrastructure.cluster.x-k8s.io/v1beta2
kind: AWSManagedMachinePool
metadata:
  name: capi-eks-pool-0
  namespace: default
spec:
  instanceType: t3.medium
  scaling:
    maxSize: 5
    minSize: 1
```
![workload](/images/4.workload/013-workload.png) 
![workload](/images/4.workload/014-workload.png) 

- Alternatively, deploy Cluster Autoscaler for EKS if you're using unmanaged node groups.
