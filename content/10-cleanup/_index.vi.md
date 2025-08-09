+++
title = "Dọn dẹp tài nguyên  "
date = 2021
weight = 10
chapter = false
pre = "<b>10. </b>"
+++

## Dọn Dẹp Tài Nguyên Sau Workshop

Để đảm bảo không phát sinh chi phí đám mây ngoài ý muốn, hãy thực hiện các bước sau để xóa toàn bộ môi trường đã tạo:

### Xóa các ứng dụng Argo CD:
```
kubectl delete -f ./management/argocd-ec2-app.yaml 
kubectl delete -f ./management/argocd-eks-app.yaml
kubectl delete -f ./app/eks-app/eks-nginx-deploy.yaml
```

### Xóa các cụm đã tạo:
```
kubectl delete cluster capi-eks
kubectl delete cluster capi-ec2
```

### Xóa SSH key EC2 đã tạo ở mỗi vùng:
```
aws ec2 delete-key-pair --key-name capi-eks --region ap-southeast-2
aws ec2 delete-key-pair --key-name capi-ec2 --region us-east-1
```

### Xóa CloudFormation Stack được tạo khi chạy lệnh “clusterawsadm bootstrap”:
```
aws cloudformation delete-stack --stack-name cluster-api-provider-aws-sigs-k8s-io --region us-east-1
```

### Xóa cụm quản lý (Kind cluster):
```
kind delete cluster
```
