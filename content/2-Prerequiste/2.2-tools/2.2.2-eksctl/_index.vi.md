---
title : "Eksctl (CLI cho Amazon EKS)"
date : "2025-07-29"
weight : 2
chapter : false
pre : " <b> 2.2.2 </b> "
---

eksctl là một công cụ dòng lệnh dùng để tạo và quản lý các cụm Kubernetes trên Amazon EKS. Công cụ này đơn giản hóa các tác vụ quản lý cụm bằng cách sử dụng API của AWS.

## Cài đặt

- **Chạy đoạn lệnh dưới đây:**
```
curl --silent --location "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin/
sudo chmod +x /usr/local/bin/eksctl
```

## Kiểm tra cài đặt 
   Chạy lệnh sau trong Command Prompt hoặc PowerShell:  
   ```
   eksctl version
   ```