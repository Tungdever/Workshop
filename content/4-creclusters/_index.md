---
title : "Create Workload Cluster"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

## Create Workload Clusters on EC2 and EKS

### Generate SSH Keys

Cluster API requires SSH keys to access EC2 nodes. You need to create separate keys for each region:

```
aws ec2 create-key-pair --key-name capi-eks --region ap-southeast-2 --query 'KeyMaterial' --output text > capi-eks.pem
aws ec2 create-key-pair --key-name capi-ec2 --region us-east-1 --query 'KeyMaterial' --output text > capi-ec2.pem
```

{{% notice note %}}
Note: EC2 and EKS clusters are deployed in different regions, so each requires its own key.
{{% /notice %}}

---

### Define Environment Variables

```
export AWS_CONTROL_PLANE_MACHINE_TYPE=t3.medium
export AWS_NODE_MACHINE_TYPE=t3.medium
```

These variables specify the EC2 instance types for control plane and worker nodes.

---

### Generate EC2 Cluster Manifest

```
export AWS_SSH_KEY_NAME=capi-ec2
export AWS_REGION=us-east-1
clusterctl generate cluster capi-ec2 --kubernetes-version v1.31.0 --control-plane-machine-count=3 --worker-machine-count=3 > ./capi-cluster/aws-ec2/aws-ec2.yaml
```

This command generates a YAML manifest for a Kubernetes cluster running on EC2 with 3 control plane nodes and 3 worker nodes.

---

### Generate EKS Cluster Manifest

```
export AWS_SSH_KEY_NAME=capi-eks
export AWS_REGION=ap-southeast-2
clusterctl generate cluster capi-eks --flavor eks-managedmachinepool --kubernetes-version v1.31.0 --worker-machine-count=3 > ./capi-cluster/aws-eks/capi-eks.yaml
```

The `eks-managedmachinepool` flavor is used to deploy an EKS cluster with managed node groups.

---

## Install Argo CD on the Management Cluster

Install Argo CD on the Kind-based management cluster:

```
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Retrieve the initial admin password:

```
kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

Access the Argo CD web UI:

```
kubectl port-forward svc/argocd-server 8080:80
```

- Username: `admin`
- Password: (use the password retrieved above)

Connect your Git repository via **Settings > Repositories > Connect repo**.

---

## Grant Permissions to Argo CD

To allow Argo CD to manage Cluster API resources, grant it cluster-admin permissions:

```
kubectl apply -f ./management/argo-cluster-role-binding.yaml
```

---

## Deploy Clusters via Argo CD

Argo CD uses `Application` manifests to deploy clusters. Example for EC2:

```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: ec2-cluster
spec:
  destination:
    namespace: default
    server: https://kubernetes.default.svc
  source:
    path: capi-cluster/aws-ec2
    repoURL: [update the Git Repo accordingly]
    targetRevision: HEAD
  project: default
  syncPolicy:
    automated:
      prune: true
      allowEmpty: true
```

After updating the manifest, commit and push to Git:

```
git add .
git commit -m "Update Argo CD app manifest"
git push
```

Apply the manifest to create the EC2 cluster:

```
kubectl apply -f ./management/argocd-ec2-app.yaml
```

Similarly, deploy the EKS cluster:

```
kubectl apply -f ./management/argocd-eks-app.yaml
```

---

## Check Cluster Status

```
clusterctl describe cluster capi-eks
clusterctl describe cluster capi-ec2
```

If the EC2 cluster shows `READY=False`, it may be missing a CNI. Deploy Calico to enable node networking:

```
clusterctl get kubeconfig capi-ec2 > capikubeconfig-ec2
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

---

## Retrieve Kubeconfig for EKS Cluster

Unlike EC2 clusters, EKS kubeconfig is stored as a secret in the management cluster:

```
kubectl --namespace=default get secret capi-eks-user-kubeconfig -o jsonpath={.data.value} | base64 --decode > capikubeconfig-eks
```

