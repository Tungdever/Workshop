---
title : "Tự động nâng cấp và mở rộng"
date : "2025-07-29"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
## Nâng cấp cụm Kubernetes

Việc nâng cấp cụm Kubernetes được quản lý bởi Cluster API là một quy trình khai báo. Bạn chỉ cần cập nhật phiên bản Kubernetes trong các tệp manifest của cụm, sau đó Argo CD sẽ tự động đồng bộ và triển khai thay đổi.

1. **Chọn phiên bản Kubernetes mục tiêu**  
   Ví dụ: `v1.32.0`. Đảm bảo phiên bản này tương thích với hạ tầng hiện tại và được Cluster API hỗ trợ.

2. **Cập nhật tệp manifest của cụm**

#### Với cụm EC2 (`aws-ec2.yaml`):

- Tìm và cập nhật trường `version` trong tài nguyên `KubeadmControlPlane` và `MachineDeployment`.
- Đảm bảo `MachineDeployment` sử dụng chiến lược cập nhật cuốn chiếu (rolling update) để tránh downtime:
  ```
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  ```
  Cấu hình này đảm bảo chỉ một node mới được thêm vào tại một thời điểm, và không có node hiện tại nào bị tắt trong quá trình nâng cấp.

- Commit và push thay đổi lên Git repository.
- Argo CD sẽ phát hiện thay đổi và tự động triển khai. Bạn có thể theo dõi trạng thái đồng bộ trong giao diện Argo CD.

#### Với cụm EKS (`capi-eks.yaml`):

- Tìm và cập nhật trường `version` trong tài nguyên `AWSManagedControlPlane`.
- Commit và push thay đổi.
- Argo CD sẽ đồng bộ và áp dụng bản nâng cấp cho cụm EKS.

{{% notice note %}}
Note: Phương pháp nâng cấp này đảm bảo tính nhất quán, dễ kiểm soát và hoàn toàn tự động thông qua GitOps.
{{% /notice %}}

---

## Tự động mở rộng cụm (Autoscaling)

Tự động mở rộng cho phép cụm Kubernetes điều chỉnh số lượng node dựa trên mức sử dụng tài nguyên, giúp tối ưu chi phí và hiệu năng.

### Tự động mở rộng cho cụm EC2

#### 1. Tạo manifest Cluster Autoscaler

Tạo tệp `management/cluster-autoscaler.yaml` để định nghĩa deployment cho Cluster Autoscaler:

- Sử dụng image chính thức: `registry.k8s.io/autoscaling/cluster-autoscaler:v1.32.0`
- Nhắm đến nhóm node EC2: `--nodes=1:10:capi-ec2-md-0`
- Đặt tên cụm: `--cluster-name=capi-ec2`
- Bao gồm các flag để cân bằng, ghi log và xử lý node an toàn

Deployment này cho phép autoscaler mở rộng nhóm node từ 1 đến 10 tùy theo nhu cầu.

#### 2. Cấp quyền RBAC

Tạo tệp `management/cluster-autoscaler-rbac.yaml` để cấp quyền cho autoscaler:

- Tạo ServiceAccount: `cluster-autoscaler`
- Tạo ClusterRole: cho phép đọc và theo dõi pod, node, machine deployment,...
- Tạo ClusterRoleBinding: liên kết role với service account

Nếu không có các quyền này, autoscaler sẽ không thể giám sát hoặc điều chỉnh tài nguyên trong cụm.

#### 3. Triển khai qua Argo CD

Tạo manifest `Application` của Argo CD (`management/argocd-autoscaler-app.yaml`) để tự động triển khai autoscaler và cấu hình RBAC:

```
kubectl apply -f management/argocd-autoscaler-app.yaml
```

Sau khi áp dụng, Argo CD sẽ quản lý autoscaler và giữ nó đồng bộ với Git repo.

---

### Tự động mở rộng cho cụm EKS

Với cụm EKS, việc mở rộng thường được xử lý thông qua **Managed Node Groups** do AWS quản lý.

#### Cấu hình Managed Node Group

Trong manifest EKS (`AWSManagedMachinePool`), định nghĩa thông số mở rộng:

```
spec:
  instanceType: t3.medium
  scaling:
    maxSize: 5
    minSize: 1
```

Cấu hình này cho phép AWS tự động điều chỉnh số lượng node từ 1 đến 5 dựa trên tải công việc.

#### Lựa chọn khác: Dùng Cluster Autoscaler cho nhóm node không quản lý

Nếu bạn sử dụng nhóm node không được quản lý trong EKS, có thể triển khai Cluster Autoscaler thủ công giống như với cụm EC2.

