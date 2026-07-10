---
title: "Ngày 2"
date: 2026-04-22
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

> **Ngày 2 - Thứ 4, 22/04/2026:** Thiết lập hệ thống giám sát chi phí đa lớp, tìm hiểu advanced analytics và học lý thuyết nền tảng AWS.

---

### Mục tiêu trong ngày

- Thiết lập hệ thống **Basic Monitoring** toàn diện với 3 mức cảnh báo.
- Nghiên cứu và cấu hình **Advanced Analytics** sử dụng Custom CloudWatch Metrics và Resource Tagging.
- Xây dựng **Emergency Cost Control Protocol** để phản ứng nhanh khi phát sinh chi phí bất thường.
- Học lý thuyết nền tảng AWS: Cloud Computing, Hạ tầng toàn cầu, IAM, Networking, Storage, Database, Compute.

---

### Basic Monitoring - Hệ thống kiểm soát chi phí

#### 1. AWS Budgets - Ba mức ngưỡng cảnh báo

| Budget | Ngưỡng | Kích hoạt cảnh báo |
|--------|--------|-------------------|
| Budget 1 | $50/tháng | Cảnh báo tại 80% ($40) |
| Budget 2 | $25/tháng | Cảnh báo tại 50% ($12.5) |
| Budget 3 | $10/ngày | Cảnh báo tại 100% ($10) |

Cơ chế ba lớp đảm bảo cảnh báo sớm trước khi đạt đến giới hạn credit - Budget 3 đóng vai trò như một hàng rào hàng ngày, phát hiện tài nguyên "chạy quá mức" trong vòng 24 giờ.

#### 2. CloudWatch Billing Alarms - Thông báo leo thang

| Ngưỡng | Kênh thông báo |
|--------|---------------|
| $25 | Email cảnh báo |
| $50 | Email + SMS |
| $75 | Email + SMS + Slack |

Cấu hình kênh leo thang đảm bảo rằng nếu email bị bỏ sót, SMS và Slack sẽ cung cấp lớp cảnh báo thứ hai và thứ ba.

#### 3. AWS Cost Explorer - Báo cáo hàng ngày

- Kích hoạt **daily cost reports** để theo dõi chi phí từng ngày thay vì chờ đến cuối tháng mới biết.
- Cấu hình **service-level breakdown** để xác định dịch vụ AWS nào đang tiêu thụ nhiều credit nhất.
- Theo dõi **top 5 cost drivers** để nhanh chóng xác định tài nguyên tốn kém nhất.

---

### Advanced Analytics

#### Custom CloudWatch Metrics

Tạo các metric tùy chỉnh để theo dõi các chỉ số business-specific vượt ra ngoài các metric mặc định của AWS. Đặc biệt hữu ích để theo dõi các sự kiện cấp ứng dụng (số lượng API calls, tỷ lệ lỗi, độ sâu hàng đợi) song song với các metric hạ tầng.

#### Resource Tagging Strategy

Áp dụng chính sách tag nhất quán cho mọi tài nguyên AWS:

| Tag Key | Giá trị ví dụ | Mục đích |
|---------|--------------|---------|
| `Project` | `FCAJ-Internship` | Theo dõi chi phí theo dự án |
| `Environment` | `dev` / `prod` | Tách chi phí dev và production |
| `Owner` | `HuynhThaiLinh` | Trách nhiệm cá nhân |

Tagging nhất quán cho phép báo cáo phân bổ chi phí hiển thị chính xác tiền đang đi đâu - theo team, dự án và môi trường.

---

### Emergency Cost Control Protocol

#### Kiểm tra tài nguyên khẩn cấp

Khi phát hiện chi phí bất thường, bước đầu tiên là kiểm tra toàn bộ tài nguyên đang chạy:
```bash
# Liệt kê tất cả EC2 instances đang chạy trên mọi region
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[].Instances[].{ID:InstanceId,Type:InstanceType,Region:Placement.AvailabilityZone}'

# Kiểm tra tất cả RDS instances đang hoạt động
aws rds describe-db-instances --query 'DBInstances[].{ID:DBInstanceIdentifier,Status:DBInstanceStatus}'
```

#### Quy trình tắt khẩn cấp

Một runbook được tài liệu hóa để tắt các tài nguyên không thiết yếu nhằm ngăn chặn chi phí leo thang - cực kỳ quan trọng trong môi trường học tập, nơi một GPU instance hoặc NAT Gateway bị bỏ quên có thể tiêu hết credit trong vài giờ.

---

### Lý thuyết nền tảng: Tổng quan Cloud Computing

**Lợi ích của điện toán đám mây:**
- **Elasticity:** Mở rộng hoặc thu nhỏ tài nguyên theo nhu cầu - không cần over-provision cho traffic đỉnh.
- **Pay-as-you-go:** Chỉ trả tiền cho những gì thực sự sử dụng, khi sử dụng.
- **Phạm vi toàn cầu:** Triển khai hạ tầng trên 30+ region toàn cầu chỉ trong vài phút.

**Mô hình dịch vụ:**
| Mô hình | Ví dụ AWS | Bạn quản lý gì |
|---------|-----------|----------------|
| **IaaS** | EC2, VPC | OS, runtime, ứng dụng |
| **PaaS** | Elastic Beanstalk, RDS | Chỉ logic ứng dụng |
| **SaaS** | Amazon Chime, WorkMail | Không gì cả - chỉ cần dùng |

---

### Lý thuyết nền tảng: Hạ tầng toàn cầu AWS

- **Regions (~30+):** Các khu vực địa lý riêng biệt. Dữ liệu không rời khỏi region trừ khi được cấu hình rõ ràng.
- **Availability Zones (AZs):** 2–6 data center vật lý riêng biệt trong một Region, kết nối qua liên kết độ trễ thấp. Chạy tài nguyên trên nhiều AZ đảm bảo **High Availability**.
- **Edge Locations (~400+):** Điểm phân phối nội dung (CDN) của CloudFront - phục vụ nội dung được cache từ vị trí gần người dùng nhất, giảm latency.

---

### Lý thuyết nền tảng: Bảo mật AWS & IAM

- **Root Account:** Tài khoản master với quyền không giới hạn - chỉ dùng cho thiết lập ban đầu, sau đó bảo vệ bằng MFA và "khóa đi".
- **IAM Users:** Danh tính cá nhân cho con người hoặc ứng dụng - không bao giờ chia sẻ credentials.
- **IAM Groups:** Tập hợp logic các users - gắn policies cho group, không gắn cho từng user riêng lẻ.
- **IAM Policies:** Tài liệu JSON định nghĩa những hành động nào được phép hoặc từ chối trên tài nguyên nào.
- **Nguyên tắc Least Privilege:** Chỉ cấp quyền tối thiểu cần thiết - không hơn.
- **MFA (Multi-Factor Authentication):** Bắt buộc cho Root account; khuyến nghị mạnh mẽ cho mọi IAM user có quyền cao.

---

### Lý thuyết nền tảng: Tổng quan các dịch vụ cốt lõi

**Amazon S3 (Simple Storage Service):**
- Object storage với độ bền **99.999999999% (11 nines)**.
- Các lớp lưu trữ: S3 Standard → S3 Standard-IA → S3 Glacier → S3 Glacier Deep Archive (hiệu quả chi phí tăng dần, tốc độ truy xuất giảm dần).

**Amazon EC2 (Elastic Compute Cloud):**
- Điện toán linh hoạt với hàng chục loại instance được tối ưu cho các workload khác nhau (general purpose, compute optimized, memory optimized, GPU).
- Các quyết định chính: Loại instance, AMI, vị trí VPC/Subnet, Security Group, Key Pair.

**Amazon RDS:** Cơ sở dữ liệu quan hệ được quản lý - tự động backup, vá lỗi, failover Multi-AZ.

**AWS Lambda:** Compute serverless event-driven - không quản lý server, tự động mở rộng.

---

### Nội dung đã học trong ngày

| Ngày | Chủ đề |
|------|--------|
| 22/4 | Compute Essentials with Amazon EC2 |
| 22/4 | Instance Profiling with IAM Roles for EC2 |
| 22/4 | Cloud Development with AWS Cloud9 |
| 22/4 | Static Website Hosting with Amazon S3 |
| 22/4 | Database Essentials with Amazon RDS |

---

### Bài học rút ra

- Hệ thống giám sát **ba lớp** (Budgets → CloudWatch Alarms → Cost Explorer) cung cấp phòng thủ theo chiều sâu chống lại chi phí bất ngờ.
- **Resource Tagging** là nền tảng của trách nhiệm chi phí - không có tags, không thể biết dự án hay người nào gây ra đỉnh chi phí.
- Hiểu **Shared Responsibility Model** làm rõ AWS quản lý gì so với trách nhiệm của mình - bảo mật "của" đám mây (AWS) so với bảo mật "trong" đám mây (mình).
- Tư duy về **Emergency Protocol** quan trọng không kém kỹ năng kỹ thuật - biết cách dừng nhanh một tình huống chi phí mất kiểm soát là kỹ năng thực tế production thực sự.

---

*Nguồn tài liệu chính: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
