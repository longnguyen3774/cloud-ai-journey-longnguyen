---
title: "Ngày 5"
date: 2026-05-06
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

> **Ngày 5 - Thứ 4, 06/05/2026:** Infrastructure as Code với AWS CloudFormation, data engineering pipeline với AWS Glue & Amazon Athena, và thực hành khởi tạo cơ sở dữ liệu quan hệ với Amazon RDS.

---

### Mục tiêu trong ngày

- Thành thạo **AWS CloudFormation** như dịch vụ Infrastructure as Code gốc của AWS cho việc triển khai có thể lặp lại và kiểm soát.
- Hiểu stack **AWS Glue + Amazon Athena** để xây dựng pipeline phân tích data lake tiết kiệm chi phí.
- Thực hành: Khởi tạo **cơ sở dữ liệu quan hệ PostgreSQL hoặc MySQL** bằng Amazon RDS trong VPC tùy chỉnh với cấu hình security group đúng đắn.

---

### Lý thuyết: AWS CloudFormation - Infrastructure as Code

**AWS CloudFormation** là dịch vụ **IaC (Infrastructure as Code)** gốc của AWS - định nghĩa toàn bộ môi trường AWS trong một template JSON hoặc YAML và triển khai nhất quán, có thể lặp lại và an toàn.

**Cấu trúc template:**

| Section | Mục đích |
|---------|---------|
| `AWSTemplateFormatVersion` | Khai báo phiên bản định dạng template |
| `Parameters` | Các giá trị đầu vào (environment, instance type, v.v.) - làm template có thể tái sử dụng |
| `Resources` | Các tài nguyên AWS thực sự sẽ được tạo (section bắt buộc duy nhất) |
| `Outputs` | Các giá trị xuất ra (ARNs, endpoints) cho cross-stack references |
| `Mappings` | Bảng tra cứu key-value (ví dụ: AMI IDs theo region) |
| `Conditions` | Tạo tài nguyên có điều kiện (ví dụ: chỉ tạo NAT Gateway trong prod) |

**Các khái niệm quan trọng:**
- **Stacks:** Nhóm logic các tài nguyên AWS từ một template - được tạo, cập nhật và xóa cùng nhau như một đơn vị.
- **Change Sets:** Xem trước thay đổi hạ tầng trước khi áp dụng - thấy chính xác những gì sẽ được thêm, sửa đổi hoặc xóa. *Luôn dùng Change Sets trong production.*
- **Stack Sets:** Triển khai cùng một template trên nhiều tài khoản và regions AWS cùng lúc - quan trọng cho tổ chức đa tài khoản.
- **Drift Detection:** Phát hiện khi tài nguyên bị sửa đổi thủ công bên ngoài CloudFormation - xác định độ lệch cấu hình so với source of truth.
- **Nested Stacks:** Chia template lớn thành các module nhỏ hơn, có thể tái sử dụng - tham chiếu child stacks từ parent stack template.

**Ví dụ template CloudFormation đơn giản:**
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Ngày 5 Thực Hành - S3 Bucket với Versioning'

Parameters:
  Environment:
    Type: String
    AllowedValues: [dev, staging, prod]
    Default: dev

Resources:
  DataBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub 'fcaj-data-${Environment}-${AWS::AccountId}'
      VersioningConfiguration:
        Status: Enabled
      Tags:
        - Key: Environment
          Value: !Ref Environment

Outputs:
  BucketName:
    Value: !Ref DataBucket
    Export:
      Name: !Sub '${AWS::StackName}-BucketName'
  BucketArn:
    Value: !GetAtt DataBucket.Arn
    Export:
      Name: !Sub '${AWS::StackName}-BucketArn'
```

> **Bài học:** CloudFormation tạo ra **hạ tầng có thể tái tạo và kiểm soát version** - nền tảng của GitOps và CI/CD pipelines cho môi trường cloud. Mọi thao tác thủ công trên Console là một khoảng trống trong audit trail hạ tầng của bạn.

---

### Lý thuyết: AWS Glue & Amazon Athena - Data Engineering

#### AWS Glue

**AWS Glue** là dịch vụ **ETL (Extract, Transform, Load)** fully managed để xây dựng data pipeline mà không cần quản lý hạ tầng - xương sống của data engineering serverless trên AWS.

**Các thành phần chính:**
- **Data Catalog:** Kho metadata tập trung - lưu schema bảng, thông tin partition và vị trí dữ liệu. Đóng vai trò như Hive Metastore cho hệ sinh thái AWS.
- **Glue Crawlers:** Tự động phát hiện dữ liệu trong S3, RDS, DynamoDB và các nguồn khác - suy luận schema, phát hiện partition mới và cập nhật Data Catalog liên tục.
- **ETL Jobs:** Script PySpark, Spark Streaming hoặc Python Shell để transform dữ liệu thô thành định dạng có cấu trúc, sẵn sàng phân tích.
- **Glue Studio:** Công cụ xây dựng ETL job trực quan - giao diện kéo thả cho người không lập trình thiết kế transformation pipeline.
- **Glue DataBrew:** Profiling và làm sạch dữ liệu có hướng dẫn - công cụ no-code cho data analysts khám phá, trực quan hóa và làm sạch dataset.

#### Amazon Athena

**Amazon Athena** là **engine truy vấn SQL serverless, tương tác** để phân tích dữ liệu trực tiếp trên **Amazon S3** - không cần tải dữ liệu, không quản lý cluster, không cần provisioning hạ tầng.

**Khả năng quan trọng:**
- Hỗ trợ **ANSI SQL** chuẩn - nếu bạn biết SQL, bạn có thể truy vấn data lake ngay lập tức.
- Truy vấn dữ liệu dạng CSV, JSON, Parquet, ORC, Avro, v.v. - trực tiếp từ S3.
- **Định giá theo truy vấn:** Chỉ tính phí dữ liệu được quét (thường $5/TB). Tối ưu chi phí bằng cách:
  - Dùng **định dạng columnar** (Parquet, ORC) - chỉ đọc cột cần thiết.
  - **Phân vùng (partitioning)** dữ liệu - truy vấn chỉ quét các partition liên quan.
- **Tích hợp AWS Glue Data Catalog:** Athena dùng Data Catalog của Glue làm lớp metadata - không cần quản lý schema riêng biệt.
- **Federated Queries:** Truy vấn dữ liệu trên RDS, DynamoDB, Redshift và nguồn on-premises từ một truy vấn Athena duy nhất - được hỗ trợ bởi Lambda-based connectors.
- Kết quả truy vấn tự động lưu vào **S3 output bucket** được chỉ định.

**Luồng phân tích data lake điển hình:**
```
Dữ liệu thô (CSV/JSON) → S3
         ↓
Glue Crawler → phát hiện schema → cập nhật Data Catalog
         ↓
Glue ETL Job → transform thành Parquet + phân vùng theo ngày → S3
         ↓
Athena → Truy vấn SQL trên dữ liệu Parquet tối ưu
         ↓
QuickSight / Notebooks → Trực quan hóa & phân tích
```

> **Bài học:** Kết hợp Glue + Athena tạo ra stack phân tích data lake hiệu quả, tiết kiệm chi phí - nạp dữ liệu thô vào S3, catalog bằng Glue, transform sang định dạng columnar, rồi truy vấn ngay bằng Athena. Không có server, không có cluster, không có chi phí hạ tầng upfront.

---

### Thực hành: Khởi tạo Cơ sở dữ liệu Quan hệ với Amazon RDS

**Mục tiêu:** Provisioning cơ sở dữ liệu PostgreSQL được quản lý trong VPC tùy chỉnh với cấu hình bảo mật đúng đắn.

#### Bước 1: Tạo DB Subnet Group

DB Subnet Group cho RDS biết nó có thể đặt database instances vào subnet nào. Yêu cầu ít nhất 2 subnet ở các Availability Zones khác nhau (bắt buộc cho triển khai Multi-AZ).

1. Vào **RDS Console** → **Subnet Groups** → **Create DB Subnet Group**.
2. Chọn VPC tùy chỉnh và ít nhất 2 subnet ở các AZ khác nhau.

#### Bước 2: Cấu hình Security Group cho RDS

Tạo security group chuyên dụng chỉ cho phép traffic database từ application servers (không phải từ internet công cộng):

- Cho phép inbound `PostgreSQL (port 5432)` hoặc `MySQL (port 3306)` **chỉ từ security group của application EC2** - không phải từ `0.0.0.0/0`.
- Đây là **security group chaining** - best practice cho kiểm soát truy cập database.

#### Bước 3: Khởi chạy RDS Instance

| Tham số | Giá trị |
|---------|--------|
| Engine | PostgreSQL 15 (hoặc MySQL 8.0) |
| Template | Free Tier |
| Instance class | `db.t3.micro` |
| Storage | 20 GB gp2 (General Purpose SSD) |
| Multi-AZ | Tắt (Free Tier) |
| VPC | VPC tùy chỉnh |
| Public Access | Không |
| Backup Retention | 7 ngày |

#### Bước 4: Kết nối đến cơ sở dữ liệu

```bash
# Dùng psql (PostgreSQL)
psql -h <rds-endpoint> -U admin -d postgres

# Dùng mysql client
mysql -h <rds-endpoint> -u admin -p
```

#### Bước 5: Dọn dẹp sau thực hành

```bash
# Xóa RDS instance (tắt final snapshot để tránh chi phí)
aws rds delete-db-instance \
  --db-instance-identifier my-day5-db \
  --skip-final-snapshot

# Đợi xóa xong, sau đó xóa DB Subnet Group
aws rds delete-db-subnet-group \
  --db-subnet-group-name my-day5-subnet-group
```

> **Bài học:** Amazon RDS loại bỏ toàn bộ overhead quản trị cơ sở dữ liệu - patching, backup, failover và quản lý replica đều được xử lý tự động. Điều này giúp developer tập trung hoàn toàn vào data modeling và logic ứng dụng thay vì công việc DBA.

---

### Bài học rút ra

- **CloudFormation** là nền tảng của khả năng tái tạo hạ tầng - thao tác Console thủ công thì ổn khi khám phá nhưng không bao giờ nên làm trong production mà không có thay đổi template tương ứng.
- **Change Sets** là lưới an toàn trước khi áp dụng bất kỳ cập nhật CloudFormation nào - chúng ngăn ngừa sự ngạc nhiên kiểu "tôi không biết điều đó sẽ bị xóa".
- Stack **Glue + Athena** chứng minh triết lý của AWS về data engineering serverless: chỉ trả cho những gì sử dụng, không có cluster ngồi chờ qua đêm.
- **Định dạng columnar (Parquet)** là tối ưu hóa có tác động lớn nhất cho Athena - có thể giảm dữ liệu được quét (và chi phí) 10x hoặc hơn so với CSV thô.
- **Security group chaining** cho RDS (chỉ cho phép traffic từ security group application cụ thể) là mô hình bảo mật database đúng đắn - không bao giờ expose database ports ra internet.

---

*Nguồn tài liệu chính: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
