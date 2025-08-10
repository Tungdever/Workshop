+++
title = "Clean up resources"
date = 2022
weight = 10
chapter = false
pre = "<b>6. </b>"
+++


To avoid unnecessary AWS charges and ensure a clean resources after completing the workshop, follow these steps to remove all provisioned resources.

### Delete the Argo CD applications: 

These applications were used to deploy clusters and workloads. Removing them ensures Argo CD no longer manages or syncs related resources.
```
kubectl delete -f ./management/argocd-ec2-app.yaml 
kubectl delete -f ./management/argocd-eks-app.yaml
```

### Delete the created clusters: 
```
kubectl delete cluster capi-eks
kubectl delete cluster capi-ec2
```

### Delete the created EC2 SSH key in each region:
```
aws ec2 delete-key-pair --key-name capi-eks --region ap-southeast-2
aws ec2 delete-key-pair --key-name capi-ec2 --region us-east-1
```

### Delete IAM Resources via CloudFormation

When initializing Cluster API for AWS, a CloudFormation stack was created to provision IAM roles and policies. You should delete this stack to clean up those resources.
```
aws cloudformation delete-stack --stack-name cluster-api-provider-aws-sigs-k8s-io --region us-east-1
```

### Delete management cluster
```
kind delete cluster
```