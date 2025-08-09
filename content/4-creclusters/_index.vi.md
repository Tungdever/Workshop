---
title : "Tạo Workload Cluster"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
## Tạo Manifest cho Cụm Kubernetes  

1. **Tạo khóa SSH**:  
```
aws ec2 create-key-pair --key-name capi-eks --region ap-southeast-2 --query 'KeyMaterial' --output text > capi-eks.pem
aws ec2 create-key-pair --key-name capi-ec2 --region us-east-1 --query 'KeyMaterial' --output text > capi-ec2.pem
```

2. **Xác định các biến môi trường**:  
```
export AWS_CONTROL_PLANE_MACHINE_TYPE=t3.medium
export AWS_NODE_MACHINE_TYPE=t3.medium
```

3. **Tạo Manifest cho Cụm Kubernetes trên EC2**:  
```
export AWS_SSH_KEY_NAME=capi-ec2
export AWS_REGION=us-east-1 
clusterctl generate cluster capi-ec2 --kubernetes-version v1.31.0 --control-plane-machine-count=3 --worker-machine-count=3 > ./capi-cluster/aws-ec2/aws-ec2.yaml
```

4. **Tạo Manifest cho Cụm Kubernetes trên EKS**:  
```
export AWS_SSH_KEY_NAME=capi-eks
export AWS_REGION=ap-southeast-2
clusterctl generate cluster capi-eks --flavor eks-managedmachinepool --kubernetes-version v1.31.0 --worker-machine-count=3 > ./capi-cluster/aws-eks/capi-eks.yaml
```

## Cài đặt Argo CD trên Cụm Quản lý Kubernetes  

**Cài đặt Argo CD trong cụm quản lý Kind**:  
```
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**Lấy mật khẩu đăng nhập lần đầu cho giao diện web Argo CD**:  
```
kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

**Cấu hình xác thực Argo CD với Git**:  

- **Đăng nhập vào cổng web của Argo CD**:  
   ```
   kubectl port-forward svc/argocd-server 8080:80
   ```
  - Tên người dùng: admin  
  - Mật khẩu: `mật khẩu đăng nhập lần đầu`  
  ![workload](/images/4.workload/001-workload.png) 

- Điều hướng đến Cài đặt > Kho lưu trữ > Kết nối kho lưu trữ:  
  - Chọn phương thức kết nối: HTTP/HTTPS  
  - Loại: Git  
  - URL kho lưu trữ: URL của kho lưu trữ đã fork  
  - Chọn `bỏ qua xác minh máy chủ`  
  ![workload](/images/4.workload/003-workload.png)  
  ![workload](/images/4.workload/002-workload.png)  
- Kết nối  

### Tạo Cụm Workload

**Để ủy quyền cho Argo CD quản lý các đối tượng Cluster API, tạo ClusterRoleBinding để cấp quyền cần thiết cho tài khoản dịch vụ `argocd-application-controller`, sử dụng vai trò `cluster-admin` cho đơn giản**:  
```
kubectl apply -f ./management/argo-cluster-role-binding.yaml
```

```yaml
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

#### Tạo Cụm Workload cho Kubernetes chạy trên EC2:  

Để tạo cụm EKS mới hoặc cụm Kubernetes chạy trên EC2, trước tiên định nghĩa một ứng dụng theo cách khai báo, đại diện cho tập hợp các manifest Kubernetes để triển khai cụm mới. Trong hướng dẫn này, tất cả cấu hình ứng dụng Argo CD được lưu trong thư mục “Management” của kho lưu trữ đã sao chép. Ví dụ, dưới đây là tệp manifest ứng dụng để tạo cụm Kubernetes chạy trên EC2:  

```yaml
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
    repoURL: '[cập nhật URL kho lưu trữ Git tương ứng]' #Chỉ định kho lưu trữ nguồn để lấy cấu hình cụm
    targetRevision: HEAD
  project: default #Có thể đặt tên dự án tại đây
  syncPolicy:
    automated:
      prune: true
      allowEmpty: true
```

Sau khi sửa đổi tệp YAML ứng dụng Argo CD như trên, commit và đẩy các thay đổi lên kho lưu trữ nguồn:  
```
git push .
git add .
git commit -m “cập nhật tệp manifest ứng dụng Argo CD” 
```

Chạy lệnh sau để tạo một ứng dụng mới nhằm tạo cụm Kubernetes chạy trên EC2:  
```
kubectl apply -f ./management/argocd-ec2-app.yaml
```

Sử dụng ứng dụng Argo CD để cung cấp cụm EKS. Đầu tiên, tạo một ứng dụng và cập nhật tệp YAML liên quan để đặt `repoURL` thành kho lưu trữ Git của bạn. Sau đó, commit và đẩy các thay đổi lên kho lưu trữ nguồn.  
```
kubectl apply -f ./management/argocd-eks-app.yaml
```

**Kiểm tra trạng thái của các cụm**:  
```
clusterctl describe cluster capi-eks
clusterctl describe cluster capi-ec2
```

Cụm EC2 hiển thị trạng thái READY của các node công việc là "False" do không có CNI, khiến các node không tham gia vào cụm. Để minh họa, triển khai Calico CNI vào cụm EC2, có thể tự động hóa bằng Argo CD.  

**Lấy tệp kubeconfig cho cụm mới tạo trên EC2**:  
```
clusterctl get kubeconfig capi-ec2 > capikubeconfig-ec2
```

**Triển khai Calico CNI**:  
```
kubectl --kubeconfig=./capikubeconfig-ec2 apply -f https://docs.projectcalico.org/v3.21/manifests/calico.yaml
```

Calico CNI được triển khai thành công, kiểm tra lại trạng thái cụm.  

Khi tạo cụm EKS, hệ thống tự động tạo và lưu trữ các tệp kubeconfig dưới dạng bí mật (secret) trong cụm quản lý. Hành vi này khác với việc tạo cụm không được quản lý bằng nhà cung cấp AWS, nơi kubeconfig không được lưu trữ theo cách này. Bí mật chứa kubeconfig sẽ được đặt tên là `[tên-cụm]-user-kubeconfig`, trong đó thay `[tên-cụm]` bằng tên thực tế của cụm.  

Để lấy kubeconfig người dùng cho cụm có tên ‘capi-eks’, chạy lệnh sau:  
```
kubectl --namespace=default get secret capi-eks-user-kubeconfig -o jsonpath={.data.value} | base64 --decode > capikubeconfig-eks
```