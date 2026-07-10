---
title: "Ngày 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

> **Ngày 3 - Thứ 2, 04/05/2026:** Nền tảng AWS CLI, NoSQL với DynamoDB, CloudFront & Lambda@Edge, và thực hành triển khai web server trên EC2 với VPC tùy chỉnh.

---

### Mục tiêu trong ngày

- Thành thạo **AWS CLI** để quản lý tài nguyên cloud bằng lập trình trực tiếp từ terminal.
- Hiểu các khái niệm **cơ sở dữ liệu NoSQL** với Amazon DynamoDB và khi nào nên chọn nó thay vì CSDL quan hệ.
- Học kiến trúc **CDN** với CloudFront và điện toán biên với Lambda@Edge.
- Triển khai một **web server** hoàn chỉnh trên EC2 trong VPC tùy chỉnh - bao gồm thiết lập networking từ đầu.

---

### Lý thuyết: AWS CLI (Command Line Interface)

**AWS CLI** là công cụ dòng lệnh thống nhất để tương tác với tất cả dịch vụ AWS trực tiếp từ terminal - cho phép tự động hóa, scripting và quản lý lập trình các tài nguyên cloud mà không cần vào Console.

**Các nội dung đã học:**
- Cài đặt và cấu hình AWS CLI bằng `aws configure` (Access Key ID, Secret Access Key, Region mặc định, Output format).
- Chạy các lệnh phổ biến: `aws ec2 describe-instances`, `aws s3 ls`, `aws iam list-users`.
- Dùng flag `--query` (cú pháp JMESPath) và `--output` (json, table, text) để lọc và định dạng kết quả.
- Tạo **named profiles** để quản lý đa tài khoản: `aws configure --profile my-profile`.
- Dùng flag `--dry-run` để kiểm tra quyền mà không thực sự thực thi lệnh.

```bash
# Ví dụ: Liệt kê tất cả S3 buckets
aws s3 ls

# Ví dụ: Mô tả các EC2 instances đang chạy trong region cụ thể
aws ec2 describe-instances \
  --region ap-southeast-1 \
  --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[].Instances[].{ID:InstanceId,Type:InstanceType}'
```

> **Bài học:** AWS CLI là công cụ không thể thiếu cho kỹ sư DevOps - mọi tài nguyên có thể quản lý trên Console đều có thể tự động hóa qua CLI. Thành thạo nó là bước đầu tiên để xây dựng deployment pipeline.

---

### Lý thuyết: NoSQL với Amazon DynamoDB

**Amazon DynamoDB** là cơ sở dữ liệu NoSQL serverless, fully managed, cung cấp hiệu suất mili giây đơn ở mọi quy mô - không cần lập kế hoạch capacity, không có maintenance windows.

**Các khái niệm cốt lõi:**
- **Mô hình dữ liệu:** Tables, Items (hàng), Attributes (cột) - không cần schema cố định.
- **Primary Keys:**
  - *Chỉ Partition Key* (đơn giản): ví dụ `UserId`
  - *Partition Key + Sort Key* (kết hợp): ví dụ `UserId` + `OrderDate` cho dữ liệu chuỗi thời gian
- **Chế độ đọc/ghi:**
  - *Provisioned*: Capacity cố định, workload có thể đoán trước, rẻ hơn ở quy mô ổn định
  - *On-Demand*: Tự động mở rộng ngay lập tức, trả theo request - lý tưởng cho traffic không đoán trước
- **DynamoDB Streams:** Ghi nhận thay đổi cấp item theo thứ tự - hỗ trợ kiến trúc event-driven (ví dụ: trigger Lambda mỗi khi dữ liệu thay đổi).
- **Global Tables:** Sao chép đa region, đa master để có latency dưới 10ms ở bất kỳ đâu trên thế giới.
- **DAX (DynamoDB Accelerator):** Cache in-memory giảm latency đọc từ mili giây xuống **micro giây**.

> **Bài học:** Chọn DynamoDB khi cần mở rộng ngang, schema linh hoạt và latency mili giây ổn định - hoàn hảo cho bảng xếp hạng game, session store, telemetry IoT và giỏ hàng. Chọn RDS khi cần join phức tạp, ACID transactions và schema nghiêm ngặt.

---

### Lý thuyết: CloudFront & Lambda@Edge

**Amazon CloudFront** là **CDN (Content Delivery Network)** toàn cầu của AWS - cache và phân phối nội dung từ các **Edge Locations** gần người dùng nhất, giảm đáng kể latency cho assets tĩnh, APIs và media streaming.

**Lambda@Edge** mở rộng AWS Lambda để chạy tại các edge location của CloudFront - cho phép tùy chỉnh request/response mà không cần định tuyến traffic về origin server.

**Các khái niệm quan trọng:**
- **Distributions:** Cấu hình phân phối CloudFront trỏ đến các **origins** (S3, ALB, EC2, custom HTTP endpoints).
- **Behaviors:** Các quy tắc ánh xạ URL pattern đến origin và cấu hình cache cụ thể.
- **Cache policies & Origin request policies:** Kiểm soát chi tiết những gì được cache và những gì được chuyển tiếp đến origin.
- **Ép buộc HTTPS:** Buộc toàn bộ traffic dùng HTTPS qua chứng chỉ SSL tùy chỉnh bằng AWS Certificate Manager (ACM).
- **Triggers của Lambda@Edge:**
  - *Viewer Request*: Sửa đổi request trước khi CloudFront kiểm tra cache
  - *Origin Request*: Sửa đổi những gì chuyển tiếp đến origin (cache miss)
  - *Origin Response*: Sửa đổi phản hồi từ origin trước khi được cache
  - *Viewer Response*: Sửa đổi phản hồi cuối cùng gửi đến client
- **Use cases:** Viết lại URL, A/B testing tại edge, xác thực JWT không cần backend, tối ưu hóa ảnh.

---

### Thực hành: Triển khai Web Server trên EC2 với VPC tùy chỉnh

#### Bước 1: Tạo VPC và Networking

| Tài nguyên | Cấu hình |
|------------|---------|
| VPC | CIDR: `10.0.0.0/16` |
| Public Subnet | CIDR: `10.0.1.0/24`, AZ: `ap-southeast-1a` |
| Internet Gateway | Gắn vào VPC |
| Route Table | Route `0.0.0.0/0` → Internet Gateway |

Tạo VPC tùy chỉnh từ đầu thay vì dùng VPC mặc định giúp hiểu sâu hơn cách networking AWS thực sự hoạt động.

#### Bước 2: Khởi chạy EC2 Instance làm Web Server

1. Vào **EC2 Console** → **Launch Instance**.
2. Chọn **Amazon Linux 2023 AMI** (miễn phí với free tier).
3. Loại instance: `t2.micro`.
4. Đặt instance trong VPC và public subnet vừa tạo.
5. Cấu hình **Security Group**: Cho phép inbound `HTTP (port 80)` và `SSH (port 22)`.
6. Thêm **User Data script** để tự động cài đặt và khởi động Apache khi boot:
   ```bash
   #!/bin/bash
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd
   echo "<h1>Hello from AWS EC2 - Ngày 3!</h1>" > /var/www/html/index.html
   ```
7. Khởi chạy instance và truy cập public IP để xác minh web server hoạt động.

#### Bước 3: Dọn dẹp sau thực hành

```bash
# Terminate EC2 instance
aws ec2 terminate-instances --instance-ids <instance-id>

# Tách và xóa Internet Gateway
aws ec2 detach-internet-gateway --internet-gateway-id <igw-id> --vpc-id <vpc-id>
aws ec2 delete-internet-gateway --internet-gateway-id <igw-id>

# Xóa Subnet, Route Table, rồi VPC (theo đúng thứ tự)
aws ec2 delete-subnet --subnet-id <subnet-id>
aws ec2 delete-route-table --route-table-id <rt-id>
aws ec2 delete-vpc --vpc-id <vpc-id>
```

> ⚠️ Luôn dọn dẹp toàn bộ tài nguyên sau mỗi buổi thực hành để tránh chi phí bất ngờ.

> **Bài học:** Hiểu networking VPC - subnets, route tables và Internet Gateways - là nền tảng để triển khai bất kỳ tài nguyên nào có thể truy cập internet trên AWS. Hầu hết mọi kiến trúc production đều bắt đầu với một VPC được thiết kế tốt.

---

### Bài học rút ra

- **AWS CLI** biến các thao tác Console thủ công thành các lệnh có thể lặp lại và viết script - thiết yếu cho tự động hóa.
- **DynamoDB vs. RDS** không phải là tốt hơn/tệ hơn - mà là việc khớp mô hình database với access pattern cụ thể.
- **CloudFront + Lambda@Edge** cho phép điện toán biên mạnh mẽ: bảo mật, cá nhân hóa và tối ưu hiệu suất mà không cần round-trips đến origin.
- Xây dựng VPC từ đầu (không dùng VPC mặc định) bộc lộ cơ chế thực sự của AWS networking - subnets, routing tables và phụ thuộc internet gateway.
- Pattern **User Data script** rất mạnh - cho phép cấu hình server hoàn toàn tự động khi khởi chạy mà không cần SSH thủ công vào.

---

*Nguồn tài liệu chính: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
