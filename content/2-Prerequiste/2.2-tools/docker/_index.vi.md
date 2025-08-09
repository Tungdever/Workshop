---
title : "Docker"
date : "2025-07-29"
weight : 6
chapter : false
pre : " <b> 2.2.6 </b> "
---

Kubernetes cung cấp một công cụ dòng lệnh để giao tiếp với mặt điều khiển của cụm Kubernetes, sử dụng API của Kubernetes.
Công cụ này được đặt tên là Kubectl

## Cài đặt

- Tải [Download Binary](https://dl.k8s.io/v1.33.3/bin/windows/amd64/kubectl.exe)
- Di chuyển đến một thư mục: Di chuyển **kubectl.exe** đến một thư mục như `C:\Program Files\kubectl` hoặc một thư mục khác
- Thêm đường dẫn thư mục vào **system environment variable** sử dụng kubectl từ bất cứ đâu

![kublectl](/images/2.prerequisite/010-kubectl.png)

## Kiểm tra cài đặt
    kubectl version --client 

![kublectl](/images/2.prerequisite/011-kubectl.png)