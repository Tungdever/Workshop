---
title : "Clusterawsadm"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 2.2.4 </b> "
---

clusterctl là một công cụ dòng lệnh dùng để quản lý các cụm Kubernetes thông qua Cluster API. Công cụ này đơn giản hóa việc tạo, nâng cấp và xóa các cụm bằng cách tương tác với các nhà cung cấp Cluster API.

## Cài đặt
- **Chạy đoạn lệnh dưới đây:**
```
curl -LO https://github.com/kubernetes-sigs/cluster-api-provider-aws/releases/download/v2.8.4/clusterawsadm-linux-amd64
chmod +x clusterawsadm-linux-amd64
sudo install -o root -g root -m 0755 clusterawsadm-linux-amd64 /usr/local/bin/clusterawsadm
```

## Xác minh cài đặt  
   Kiểm tra để đảm bảo phiên bản bạn đã cài đặt là mới nhất:  
   ```
   clusterctl version
   ```  
