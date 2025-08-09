---
title: "Cài đặt và cấu hình AWS CLI"
date: "2025-07-29"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---


### 1. Cài đặt
- **Chạy đoạn lệnh dưới đây:**
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

### 2. Cấu hình
- **Chạy lệnh**:
```
aws configure
```
Nhập các thông tin sau:

- **AWS Access Key ID**: Lấy từ AWS IAM
1. **Đăng nhập vào AWS Management Console**:
   - Truy cập https://aws.amazon.com/console/.
   - Đăng nhập bằng thông tin xác thực tài khoản AWS.
2. **Truy cập IAM**:
   - Trong AWS Management Console, tìm kiếm **IAM**.
   - Chọn **IAM** từ danh sách dịch vụ để mở bảng điều khiển IAM.

![CLI](/images/2.prerequisite/002-awscli.png)
1. **Chọn một IAM user**:
   - Trong menu bảng điều khiển, nhấp vào **Users**.
   - Nhấp vào IAM user.

![CLI](/images/2.prerequisite/003-awscli.png)
4. **Tạo Access Key**:
   - Nhấp vào tab **Security credentials**.
   - Nhấp vào **Create access key**.

![CLI](/images/2.prerequisite/004-awscli.png)
   - Chọn **Command Line Interface (CLI)**.

![CLI](/images/2.prerequisite/005-awscli.png)
   - Thêm thẻ mô tả tùy chọn cho khóa (ví dụ: `workshop-key`).
   - Nhấp vào **Create access key**.

![CLI](/images/2.prerequisite/006-awscli.png)
   - Trên màn hình tiếp theo, bạn sẽ thấy:
     - **Access Key ID**.
     - **Secret Access Key**.

![CLI](/images/2.prerequisite/007-awscli.png)
{{% notice info %}}
Tải xuống tệp `.csv` chứa thông tin xác thực hoặc sao chép chúng vào một nơi an toàn. Secret Access Key chỉ được hiển thị một lần và không thể lấy lại sau đó.
{{% /notice %}}

- **AWS Secret Access Key**: Nhập từ tệp CSV.
- **Default region name**: Ví dụ: `us-east-1`.
- **Default output format**: Nhập `json` hoặc nhấn Enter để sử dụng mặc định.

![CLI](/images/2.prerequisite/008-awscli.png)

Cấu hình được lưu trong: ``%USERPROFILE%\.aws\credentials`` và ``%USERPROFILE%\.aws\config``

Kiểm tra cấu hình bằng cách chạy:

```
aws sts get-caller-identity
```
![CLI](/images/2.prerequisite/009-awscli.png)

### Tài liệu bổ sung
- Tài liệu AWS IAM: https://docs.aws.amazon.com/iam/
- Hướng dẫn cấu hình AWS CLI: https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html  