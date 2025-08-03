---
title : "Clusterctl (CLI cho Cluster API)"
date : "2025-07-29"
weight : 4
chapter : false
pre : " <b> 2.2.4 </b> "
---

clusterctl là một công cụ dòng lệnh dùng để quản lý các cụm Kubernetes thông qua Cluster API. Công cụ này đơn giản hóa việc tạo, nâng cấp và xóa các cụm bằng cách tương tác với các nhà cung cấp Cluster API.

## Cài đặt

1. **Cài đặt file nhị phân clusterctl bằng curl trên Windows sử dụng PowerShell**  
   - Chuyển đến thư mục làm việc nơi bạn muốn tải clusterctl.  
   - Tải phiên bản mới nhất; trên Windows, gõ:  
   ```
   curl.exe -L https://github.com/kubernetes-sigs/cluster-api/releases/download/v1.10.4/clusterctl-windows-amd64.exe -o clusterctl.exe
   ```  
   ![clusterctl](/images/2.prerequisite/014-clusterctl.png)

2. **Thêm vào biến môi trường PATH**  
   - Mở **Edit the system environment variables** trên Windows.  
   - Trong **System Variables**, tìm và chỉnh sửa biến **Path**.  
   - Thêm đường dẫn đến thư mục chứa **clusterctl.exe** (ví dụ: `C:\Program Files\clusterctl`).  
   - Lưu thay đổi.  

   ![clusterctl](/images/2.prerequisite/015-clusterctl.png)

## Xác minh cài đặt  
   Kiểm tra để đảm bảo phiên bản bạn đã cài đặt là mới nhất:  
   ```
   clusterctl version
   ```  
   ![clusterctl](/images/2.prerequisite/016-clusterctl.png)
