---
title : "Eksctl (CLI cho Amazon EKS)"
date : "2025-07-29"
weight : 2
chapter : false
pre : " <b> 2.2.2 </b> "
---

eksctl là một công cụ dòng lệnh dùng để tạo và quản lý các cụm Kubernetes trên Amazon EKS. Công cụ này đơn giản hóa các tác vụ quản lý cụm bằng cách sử dụng API của AWS.

## Cài đặt

1. **Tải xuống file nhị phân**  
   - Truy cập [trang phát hành eksctl](https://github.com/weaveworks/eksctl/releases).  
   - Tải phiên bản mới nhất cho Windows (`eksctl_Windows_amd64.zip` cho 64-bit).  
   - Giải nén file ZIP để lấy file **eksctl.exe**.

2. **Di chuyển đến thư mục PATH**  
   - Di chuyển file **eksctl.exe** đến một thư mục có trong biến PATH của hệ thống như `C:\Program Files\eksctl`.  
   - Nếu cần, tạo thư mục mới này.

3. **Thêm vào biến môi trường PATH**  
   - Mở **Edit the system environment variables** trên Windows.  
   - Trong **System Variables**, tìm và chỉnh sửa biến **Path**.  
   - Thêm đường dẫn đến thư mục chứa **eksctl.exe** (`C:\Program Files\eksctl`).  
   - Lưu thay đổi.

![eksctl](/images/2.prerequisite/012-eksctl.png)

## Kiểm tra cài đặt 
   Chạy lệnh sau trong Command Prompt hoặc PowerShell:  
   ```
   eksctl version
   ```

![eksctl](/images/2.prerequisite/013-eksctl.png)