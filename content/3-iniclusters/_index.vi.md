---
title : "Khởi tạo Cluster API quản lý cluster"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

#### 1️⃣ Fork và clone repository

Bạn cần fork repository mẫu [`eks-ec2-clusterapi-gitops`](https://github.com/aws-samples/eks-ec2-clusterapi-gitops) về tài khoản GitHub của mình, sau đó clone về máy :

```
cd ./eks-ec2-clusterapi-gitops
```

Repository này chứa các manifest và cấu hình cần thiết để triển khai cụm EKS và Cluster EC2 bằng Cluster API kết hợp GitOps.

---

#### 2️⃣ Tạo cụm Kind làm cụm quản lý

Cluster API cần một cụm Kubernetes để đóng vai trò cụm quản lý (management cluster). Trong bước này, bạn sử dụng **Kind** để tạo cụm Kubernetes chạy trên Docker:

```
kind create cluster
kubectl cluster-info --context kind-kind
```

Lệnh `kubectl cluster-info` giúp xác nhận rằng cụm Kind đã được tạo thành công và có thể truy cập.

---

#### 3️⃣ Khởi tạo IAM cho Cluster API Provider for AWS

Cluster API sử dụng một công cụ tên là `clusterawsadm` để tạo các tài nguyên IAM cần thiết trên AWS. Công cụ này sẽ tạo một CloudFormation stack để cấp quyền cho cụm quản lý có thể tạo các cụm EKS:

```
clusterawsadm bootstrap iam create-cloudformation-stack --region us-east-1
```

CloudFormation stack này bao gồm các role và policy cần thiết để Cluster API có thể thao tác với AWS.

---

#### 4️⃣ Định nghĩa các biến môi trường cần thiết

Trước khi khởi tạo Cluster API, bạn cần thiết lập các biến môi trường để cung cấp thông tin xác thực và cấu hình:

```
export AWS_B64ENCODED_CREDENTIALS=$(clusterawsadm bootstrap credentials encode-as-profile)
export AWS_REGION=us-east-1
export EKS=true
export EXP_MACHINE_POOL=true
export CAPA_EKS_IAM=true
```

- `AWS_B64ENCODED_CREDENTIALS`: Mã hóa thông tin xác thực AWS để Cluster API sử dụng.
- `EKS=true`: Kích hoạt chế độ triển khai EKS.
- `EXP_MACHINE_POOL=true`: Cho phép sử dụng MachinePool (tính năng mở rộng node group).
- `CAPA_EKS_IAM=true`: Kích hoạt IAM integration cho EKS.

---

#### 5️⃣ Khởi tạo Cluster API với nhà cung cấp (provider) AWS

Cuối cùng, bạn sử dụng lệnh `clusterctl init` để cài đặt các thành phần Cluster API vào cụm Kind, biến nó thành cụm quản lý:

```
clusterctl init --infrastructure aws
```

Lệnh này sẽ cài đặt:
- **Infrastructure provider**: `cluster-api-provider-aws`
- **Bootstrap provider**: `kubeadm`
- **Control plane provider**: `kubeadm`

Sau bước này, cụm Kind đã sẵn sàng để tạo và quản lý các cụm EKS trên AWS.
