---
title : "Kubectl (CLI cho Kubernet)"
date : "2025-07-29"
weight : 1
chapter : false
pre : " <b> 2.2.1 </b> "
---

Kubernetes cung cấp một công cụ dòng lệnh để giao tiếp với mặt điều khiển của cụm Kubernetes, sử dụng API của Kubernetes.
Công cụ này được đặt tên là Kubectl

## Cài đặt

- **Chạy đoạn lệnh dưới đây:**
```
curl -LO "https://dl.k8s.io/release/v1.33.3/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

## Kiểm tra cài đặt
    kubectl version --client 
