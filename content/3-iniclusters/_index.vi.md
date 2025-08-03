---
title : "Khởi tạo Cluster API quản lý cluster"
date : "2025-07-29"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
### 1. Triển khai Cụm Quản lý
Cụm quản lý là một cụm EKS chứa các thành phần Cluster API để quản lý các cụm workload.

- Thiết lập môi trường cho PowerShell:
  ```
  $Env:AWS_ACCESS_KEY_ID = "<access-key>"
  $Env:AWS_SECRET_ACCESS_KEY = "<secret-key>"
  $credentials = "[default]`naws_access_key_id = $Env:AWS_ACCESS_KEY_ID`naws_secret_access_key = $Env:AWS_SECRET_ACCESS_KEY`n"
  $Env:AWS_B64ENCODED_CREDENTIALS = [Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($credentials))
  $Env:AWS_REGION = "us-east-1"
  $Env:CLUSTER_API_STATE_BUCKET = "capi-workshop-state"
  ```
  
- Tạo cụm quản lý với eksctl:
  ```
  eksctl create cluster --name management-cluster --region us-east-1 --version 1.33 --nodegroup-name standard-workers --node-type t3.medium --nodes 3 --nodes-min 1 --nodes-max 4 --managed 
  ```

Nếu gặp lỗi timeout trong quá trình tạo cụm, thử chuyển sang DNS công cộng:

- Mở **Control Panel** → **Network and Sharing Center** → **Change adapter settings**.
- Nhấp chuột phải vào kết nối đang hoạt động (Wi-Fi/Ethernet) → **Properties**.
- Chọn **Internet Protocol Version 4 (TCP/IPv4)** → **Properties**.
Thiết lập:
- Preferred DNS: `8.8.8.8` (Google)
- Alternate DNS: `8.8.4.4` (Google) hoặc `1.1.1.1` (Cloudflare)

{{% notice info %}}
Bạn cũng có thể thử tắt tạm thời **Windows Firewall** nếu thay đổi DNS không khắc phục được vấn đề.
{{% /notice %}}

{{% notice info %}}
Lệnh này có thể mất 10-15 phút để hoàn thành.
{{% /notice %}}

**Sau khi tạo thành công**

![inicluster](/images/2.prerequisite/018-inicluster.png)
![inicluster](/images/2.prerequisite/019-inicluster.png)

**Kiểm tra với EKS và CloudFormation**

![inicluster](/images/2.prerequisite/020-inicluster.png)
![inicluster](/images/2.prerequisite/021-inicluster.png)

**Cập nhật ngữ cảnh kubectl**:
  Sau khi cụm được tạo, cập nhật tệp kubeconfig để cho phép **kubectl** tương tác với cụm:
  ```
  aws eks update-kubeconfig --name management-cluster --region us-east-1
  ```
![inicluster](/images/2.prerequisite/037-inicluster.png)

### 2. Khởi tạo Cluster API
Cluster API là một dự án Kubernetes cung cấp các API khai báo để quản lý vòng đời cụm. Ở đây, chúng ta khởi tạo Cluster API với nhà cung cấp AWS.

- **Khởi tạo Cluster API với nhà cung cấp AWS**:
  ```
  clusterctl init --infrastructure aws
  ```
![inicluster](/images/2.prerequisite/038-inicluster.png)

### 3. Kiểm tra Khởi tạo
Kiểm tra xem các thành phần Cluster API và nhà cung cấp AWS đã được triển khai đúng chưa.

- **Kiểm tra pods trong namespace `capi-system`**:
- **Kiểm tra pods trong namespace `capi-system` and `capa-system`**:

```
kubectl get pods -n capi-system
```
![inicluster](/images/2.prerequisite/040-inicluster.png)
```
kubectl get pods -n capa-system
```
![inicluster](/images/2.prerequisite/039-inicluster.png)
