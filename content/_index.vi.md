---
title : "Kubernetes Multi-Cluster Management với Cluster API"
date: "2025-07-29"
weight : 1 
chapter : false
---
# Kubernetes Multi-Cluster Management với Cluster API
#### Tổng quan

Workshop này hướng dẫn người tham gia triển khai quản lý đa cụm Kubernetes bằng **Cluster API** trên AWS. Cluster API là một dự án Kubernetes cung cấp các API khai báo để quản lý vòng đời cụm, cho phép tự động hóa việc cung cấp, nâng cấp và mở rộng cụm. 

#### Mục tiêu
- Tự động hóa vòng đời cụm Kubernetes (tạo, mở rộng, nâng cấp và xóa).
- Thiết lập mạng liên cụm để giao tiếp liền mạch.
- Phân phối khối lượng công việc giữa các cụm để đảm bảo tính sẵn sàng cao và khả năng mở rộng.
- Áp dụng các chính sách quản trị và bảo mật để đảm bảo tuân thủ và vận hành an toàn.
- Áp dụng chiến lược quản lý chi phí để tối ưu hóa việc sử dụng tài nguyên AWS.
- Xác định quy trình vận hành cho quản lý đa cụm.

Workshop sử dụng AWS EKS (Elastic Kubernetes Service) với Cluster API để quản lý nhiều cụm, tập trung vào triển khai thực hành. Sau ưorkshop, người tham gia sẽ có một hệ thống đa cụm hoạt động với quản lý vòng đời tự động, mạng an toàn và vận hành tối ưu chi phí.

#### Nội dung
 1. [Giới thiệu ](1-introduce/)   
 2. [Các bước chuẩn bị](2-prerequiste/)
 3. [Khởi tạo cluster quản lý Cluster API](3-iniclusters/)
 4. [Tạo Workload Clusters](4-creclusters/)
 5. [Thiết lập Cross-Cluster Networking](5-networking/)
 6. [Phân phối Workload](6-distribution/)
 7. [Triển khai quản trị và bảo mật](7-security/)
 8. [Quản lý chi phí](8-cost/)
 9. [Quy trình vận hành](9-procedures/)
 10. [Dọn dẹp tài nguyên](10-cleanup/)
