---
title : "Tạo Workload Cluster"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

## Tạo cụm Kubernetes chạy trên EC2 và EKS

### Tạo SSH Key

Cluster API cần SSH key để truy cập vào các node EC2. Bạn tạo hai key riêng biệt cho từng vùng:

```
aws ec2 create-key-pair --key-name capi-eks --region ap-southeast-2 --query 'KeyMaterial' --output text > capi-eks.pem
aws ec2 create-key-pair --key-name capi-ec2 --region us-east-1 --query 'KeyMaterial' --output text > capi-ec2.pem
```

{{% notice note %}}
Lưu ý: EC2 và EKS được triển khai ở hai vùng khác nhau nên cần tạo key tương ứng.
{{% /notice %}}

---

### Định nghĩa biến môi trường

```
export AWS_CONTROL_PLANE_MACHINE_TYPE=t3.medium
export AWS_NODE_MACHINE_TYPE=t3.medium
```

Các biến này xác định loại máy EC2 sẽ được sử dụng cho control plane và worker node.

---

### Tạo manifest cụm EC2

```
export AWS_SSH_KEY_NAME=capi-ec2
export AWS_REGION=us-east-1 
clusterctl generate cluster capi-ec2 --kubernetes-version v1.31.0 --control-plane-machine-count=3 --worker-machine-count=3 > ./capi-cluster/aws-ec2/aws-ec2.yaml
```

Lệnh này tạo file YAML định nghĩa cụm Kubernetes chạy trên EC2, bao gồm 3 node control plane và 3 node worker.

---

### Tạo manifest cụm EKS

```
export AWS_SSH_KEY_NAME=capi-eks
export AWS_REGION=ap-southeast-2
clusterctl generate cluster capi-eks --flavor eks-managedmachinepool --kubernetes-version v1.31.0 --worker-machine-count=3 > ./capi-cluster/aws-eks/capi-eks.yaml
```

Sử dụng flavor `eks-managedmachinepool` để triển khai EKS với node group được quản lý.

---

## Cài đặt Argo CD trên cụm quản lý

```
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Sau khi cài đặt, lấy mật khẩu đăng nhập lần đầu:

```
kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

Truy cập giao diện web Argo CD:

```
kubectl port-forward svc/argocd-server 8080:80
```

- Username: `admin`
- Password: mật khẩu vừa lấy

Kết nối Git repo chứa cấu hình cụm bằng cách vào **Settings > Repositories > Connect repo**.

---

## Phân quyền cho Argo CD

Để Argo CD có thể quản lý các tài nguyên Cluster API, bạn cần cấp quyền `cluster-admin` cho service account của nó:

```
kubectl apply -f ./management/argo-cluster-role-binding.yaml
```

---

## Tạo cụm thông qua Argo CD

Argo CD sử dụng manifest kiểu `Application` để triển khai cụm:

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

Sau khi cập nhật file YAML, commit và push lên Git:

```
git add .
git commit -m "Update Argo CD app manifest"
git push
```

Áp dụng manifest để tạo cụm EC2:

```
kubectl apply -f ./management/argocd-ec2-app.yaml
```

Tương tự, tạo cụm EKS:

```
kubectl apply -f ./management/argocd-eks-app.yaml
```

---

## Kiểm tra trạng thái cụm

```
clusterctl describe cluster capi-eks
clusterctl describe cluster capi-ec2
```

Nếu cụm EC2 chưa sẵn sàng (`READY=False`), có thể do thiếu CNI. Cài đặt Calico để các node worker có thể tham gia cụm:

```
clusterctl get kubeconfig capi-ec2 > capikubeconfig-ec2
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

---

## Lấy kubeconfig cho cụm EKS

Khác với EC2, kubeconfig của cụm EKS được lưu dưới dạng secret trong cụm quản lý:

```
kubectl --namespace=default get secret capi-eks-user-kubeconfig -o jsonpath={.data.value} | base64 --decode > capikubeconfig-eks
```
