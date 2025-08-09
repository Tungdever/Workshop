---
title : "Khởi tạo Cluster API quản lý cluster"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
### 1. Fork kho lưu trữ Git
Fork kho Git `https://github.com/aws-samples/eks-ec2-clusterapi-gitops` vào tài khoản Github của bạn, sao chép (clone) về máy trạm cục bộ, sau đó chuyển đến thư mục sau:
```
cd ./eks-ec2-clusterapi-gitops
```

### 2. Khởi tạo cụm quản lý
Tạo cụm Kind và xác minh việc tạo thành công:
```
kind create cluster
kubectl cluster-info --context kind-kind
```

Cluster API Provider for AWS bao gồm `clusterawsadm`, một công cụ để quản lý các đối tượng IAM. Công cụ này sử dụng các biến môi trường, mã hóa chúng vào một Kubernetes Secret trong cụm Kind, và lấy các quyền cần thiết để tạo các cụm workload.
```
clusterawsadm bootstrap iam create-cloudformation-stack --region us-east-1
```

### 3. Xác định trước các tham số môi trường cần thiết
```
export AWS_B64ENCODED_CREDENTIALS=$(clusterawsadm bootstrap credentials encode-as-profile)
export AWS_REGION=us-east-1
export EKS=true
export EXP_MACHINE_POOL=true
export CAPA_EKS_IAM=true
```

### 4. Khởi tạo Cluster API với nhà cung cấp AWS
Để cài đặt các thành phần Cluster API cho AWS, nhà cung cấp bootstrap kubeadm, và nhà cung cấp control-plane kubeadm, đồng thời biến cụm Kind thành cụm quản lý, sử dụng lệnh `clusterctl init`:
```
clusterctl init --infrastructure aws
```

