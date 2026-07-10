---
title: "Ngày 4"
date: 2026-05-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

> **Ngày 4 - Thứ 3, 05/05/2026:** Serverless compute với AWS Lambda, Infrastructure as Code với AWS CDK, công cụ developer AWS Toolkit for VS Code, và thực hành quản lý file S3 với Lifecycle Policies.

---

### Mục tiêu trong ngày

- Hiểu **mô hình thực thi event-driven của AWS Lambda** và khi nào nên dùng kiến trúc serverless.
- Học **AWS CDK** như một công cụ Infrastructure as Code hiện đại sử dụng ngôn ngữ lập trình thực sự.
- Khám phá **AWS Toolkit for VS Code** để phát triển và kiểm thử Lambda functions mà không cần rời editor.
- Hiểu **EBS Data Lifecycle Manager** để tự động hóa quản lý snapshot.
- Thực hành: Tạo S3 buckets, upload files qua CLI và cấu hình **Lifecycle Policies** để tự động hóa phân tầng lưu trữ tối ưu chi phí.

---

### Lý thuyết: AWS Lambda - Serverless Compute

**AWS Lambda** là dịch vụ compute serverless, event-driven - bạn chỉ cần viết code, Lambda xử lý mọi thứ còn lại: provisioning server, vá lỗi OS, scaling và high availability.

**Mô hình thực thi:**
- **Function → Handler → Runtime**: Code chạy trong một handler function trong runtime được hỗ trợ (Node.js, Python, Java, Go, Ruby, .NET).
- **Loại invocation:** Đồng bộ (API Gateway, direct invoke) vs. Bất đồng bộ (S3 events, SNS) vs. Event source mapping (SQS, DynamoDB Streams, Kinesis).

**Tính năng quan trọng:**
- **Triggers:** API Gateway, S3 events, DynamoDB Streams, SQS, CloudWatch Events/EventBridge, SNS, Cognito, ALB.
- **Concurrency:**
  - *Reserved concurrency*: Đảm bảo số lượng thực thi đồng thời tối đa cho một function.
  - *Provisioned concurrency*: Pre-warm các Lambda instances để loại bỏ cold start cho workload nhạy cảm về latency.
- **Layers:** Đóng gói các thư viện và dependencies dùng chung thành Lambda Layers - tái sử dụng được trên nhiều functions, giữ deployment packages gọn gàng.
- **Environment Variables + Secrets Manager:** Lưu trữ cấu hình an toàn - không bao giờ hardcode credentials vào Lambda code.
- **Lambda URLs:** HTTPS endpoint trực tiếp cho Lambda mà không cần API Gateway - đơn giản hơn cho single-function APIs.

**Chi phí:**
- Tính theo **số lượng request** + **thời gian thực thi** (memory × thời gian, tính theo bước 1ms).
- Memory: 128 MB đến 10 GB.
- Timeout tối đa: **15 phút**.
- **Free Tier:** 1 triệu request và 400.000 GB-seconds mỗi tháng - Lambda cực kỳ tiết kiệm chi phí cho workload traffic thấp đến trung bình.

> **Bài học:** Mô hình event-driven của Lambda là xương sống của các kiến trúc serverless hiện đại trên AWS - nó tích hợp tự nhiên với hầu hết mọi dịch vụ AWS. Sự thay đổi tư duy quan trọng là nghĩ theo *sự kiện* thay vì *server luôn chạy*.

---

### Lý thuyết: AWS CDK (Cloud Development Kit)

**AWS CDK** cho phép định nghĩa hạ tầng cloud bằng ngôn ngữ lập trình quen thuộc (TypeScript, Python, Java, C#, Go) - sau đó tổng hợp thành CloudFormation templates bên dưới.

**Các khái niệm cốt lõi:**
- **Constructs:** Các thành phần cơ bản của CDK applications.
  - *L1 Constructs*: Wrapper 1:1 trực tiếp quanh tài nguyên CloudFormation - kiểm soát tối đa, verbose tối đa.
  - *L2 Constructs*: Abstractions cấp cao hơn với defaults hợp lý - ví dụ `aws_s3.Bucket` tự động xử lý tùy chọn encryption, versioning.
  - *L3 Constructs (Patterns)*: Giải pháp hoàn chỉnh, có quan điểm - ví dụ API Gateway backed bởi Lambda với DynamoDB, chỉ trong vài dòng.
- **Stacks:** Đơn vị deployment, ánh xạ 1:1 với một CloudFormation stack.
- **Apps:** Chương trình CDK gốc chứa một hoặc nhiều stacks.

**Quy trình CDK:**
```bash
# Khởi tạo dự án CDK mới
cdk init app --language typescript

# Xem trước CloudFormation template CDK sẽ tạo
cdk synth

# Deploy lên AWS
cdk deploy

# Xóa tất cả tài nguyên
cdk destroy
```

**Ưu điểm CDK so với CloudFormation thuần:**
- **Type safety:** IDE phát hiện lỗi cấu hình trước khi deploy.
- **Autocomplete:** Editor biết thuộc tính nào hợp lệ cho mỗi tài nguyên.
- **Logic:** Dùng điều kiện, vòng lặp và hàm thực sự - không phải `Conditions` và `Mappings` hạn chế của CloudFormation.
- **Constructs tái sử dụng:** Chia sẻ pattern hạ tầng giữa các team dưới dạng npm/PyPI packages.

---

### Lý thuyết: AWS Toolkit for VS Code

**AWS Toolkit for VS Code** là extension IDE cho phép tương tác trực tiếp với dịch vụ AWS từ trong VS Code - giảm việc chuyển đổi ngữ cảnh giữa editor và Console.

**Tính năng đã khám phá:**
- Duyệt, gọi và xem logs của **Lambda functions** trực tiếp từ panel Explorer.
- Upload và download **S3 objects** mà không cần mở AWS Console.
- Kiểm tra cú pháp và hỗ trợ schema cho **CloudFormation** và **CDK** templates.
- Debug Lambda functions **cục bộ** sử dụng **AWS SAM CLI** tích hợp - chạy và bước qua Lambda code trên laptop trước khi deploy.
- Quản lý **AWS credentials và profiles** trong IDE.

---

### Lý thuyết: Amazon EBS Data Lifecycle Manager (DLM)

**Amazon EBS DLM** tự động hóa việc tạo, lưu giữ và xóa **EBS snapshots** - cơ chế backup cho dữ liệu EC2 volume.

**Khả năng quan trọng:**
- **Lifecycle policies:** Lên lịch tạo snapshot (theo giờ, ngày, tuần).
- **Retention rules:** Giữ N snapshots mới nhất HOẶC giữ snapshot trong N ngày.
- **Cross-region copy:** Tự động sao chép snapshot sang region disaster recovery.
- **Fast Snapshot Restore (FSR):** Pre-warm snapshot để volume được khôi phục từ đó có hiệu suất đầy đủ ngay lập tức - quan trọng cho database nơi cold-start I/O suy giảm là không thể chấp nhận.

> **Bài học:** DLM là chiến lược backup "set it and forget it" cho EBS - cấu hình DLM policies cho tất cả volume production từ Ngày 1. Một snapshot bị thiếu chỉ được phát hiện khi thực sự cần dùng.

---

### Thực hành: Quản lý File S3 với Lifecycle Policies

#### 1. Tạo S3 Bucket qua CLI

```bash
aws s3api create-bucket \
  --bucket my-day4-bucket-$(date +%s) \
  --region ap-southeast-1 \
  --create-bucket-configuration LocationConstraint=ap-southeast-1
```

#### 2. Upload Files từ Local Machine

```bash
# Upload một file đơn lẻ
aws s3 cp ./local-file.txt s3://my-day4-bucket/

# Sync toàn bộ thư mục (chỉ upload file đã thay đổi)
aws s3 sync ./local-folder/ s3://my-day4-bucket/data/

# Xác minh sau khi upload
aws s3 ls s3://my-day4-bucket/ --recursive
```

#### 3. Cấu hình S3 Lifecycle Policies

| Quy tắc | Hành động | Thời điểm áp dụng |
|---------|-----------|-------------------|
| Lưu trữ dữ liệu ít dùng | Chuyển sang S3 Standard-IA | Sau **30 ngày** |
| Lưu trữ dữ liệu cũ | Chuyển sang S3 Glacier Instant Retrieval | Sau **90 ngày** |
| Xóa object hết hạn | Xóa vĩnh viễn | Sau **365 ngày** |
| Dọn dẹp multipart upload dang dở | Hủy bỏ | Sau **7 ngày** |

**Áp dụng lifecycle policy qua AWS CLI:**
```bash
aws s3api put-bucket-lifecycle-configuration \
  --bucket my-day4-bucket \
  --lifecycle-configuration file://lifecycle-policy.json
```

#### 4. Dọn dẹp sau thực hành

```bash
# Xóa tất cả objects trước
aws s3 rm s3://my-day4-bucket/ --recursive

# Sau đó xóa bucket
aws s3api delete-bucket --bucket my-day4-bucket
```

> ⚠️ S3 bucket không thể xóa khi còn chứa objects - luôn xóa objects trước.

> **Bài học:** S3 Lifecycle policies là chiến lược không tốn chi phí vận hành để giảm đáng kể chi phí lưu trữ - tự động chuyển dữ liệu lão hóa sang các lớp lưu trữ ngày càng rẻ hơn. Đây là cách tiếp cận đúng đắn cho bất kỳ yêu cầu lưu trữ dữ liệu dài hạn nào.

---

### Bài học rút ra

- **Mô hình event-driven của Lambda** thay đổi cách nghĩ về compute: thay vì quản lý server, nghĩ theo *sự kiện nào kích hoạt code của mình*.
- **AWS CDK** là lựa chọn IaC hiện đại cho team quen với ngôn ngữ lập trình - làm cho hạ tầng có thể tái sử dụng và kiểm thử như application code.
- **AWS Toolkit for VS Code** rút ngắn đáng kể vòng phản hồi của developer - debug Lambda cục bộ là thay đổi cuộc chơi cho tốc độ phát triển.
- **S3 Lifecycle policies** là bắt buộc cho bất kỳ data platform nào lưu trữ dữ liệu lịch sử - không có chúng, chi phí tích lũy âm thầm theo thời gian khi dữ liệu tiếp tục tăng.
- **EBS DLM** là tương đương snapshot của S3 lifecycle policies - tự động hóa từ Ngày 1, không bao giờ lo lắng về backup bị thiếu nữa.

---

*Nguồn tài liệu chính: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
