---
title: "Tạo EC2 Keypair"
date: "2025-07-29"
weight: 3
chapter: false
pre: " <b> 2.3 </b> "
---

1. **Tạo SSH key pair**:
   Chạy lệnh sau để tạo SSH key pair có tên `workshop-key` trong vùng `us-east-1`:
   ```
   aws ec2 create-key-pair --key-name workshop-key --query 'KeyMaterial' --output text > workshop-key.pem
   ```

2. **Xác minh key pair trong AWS Console**:
   - Đăng nhập vào [AWS Management Console](https://aws.amazon.com/console/).
   - Điều hướng đến **EC2** → **Key Pairs**.
   - Tìm key pair `workshop-key` trong vùng `us-east-1`.

![keypair](/images/2.prerequisite/017-crekey.png)

