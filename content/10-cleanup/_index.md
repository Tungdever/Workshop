+++
title = "Clean up resources"
date = 2022
weight = 10
chapter = false
pre = "<b>10. </b>"
+++

To avoid unnecessary costs, clean up all resources created during the workshop.

To make sure there’s no unwanted cloud cost, please clean up the environment. 
- Delete the Argo CD applications: 
```
kubectl delete -f ./management/argocd-ec2-app.yaml 
kubectl delete -f ./management/argocd-eks-app.yaml
kubectl delete -f ./app/eks-app/eks-nginx-deploy.yaml
```

- Delete the created clusters: 
```
kubectl delete cluster capi-eks
kubectl delete cluster capi-ec2
```

- Delete the created EC2 SSH key in each region:
```
aws ec2 delete-key-pair --key-name capi-eks --region ap-southeast-2
aws ec2 delete-key-pair --key-name capi-ec2 --region us-east-1
```

- Delete the CloudFormation Stack when running “clusterawsadm boostrap” command to create all necessary IAM resources
```
aws cloudformation delete-stack --stack-name cluster-api-provider-aws-sigs-k8s-io --region us-east-1
```

- Delete management cluster
```
kind delete cluster - 
```