---
title: "Ngày 6"
date: 2026-06-04
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

> **Ngày 6 - Thứ 6, 04/06/2026:** Chạy thử nghiệm kiến trúc data pipeline đơn giản bằng mã IaC, kiểm thử cục bộ với Floci và LocalStack (ministack), và học chuyên sâu về các dịch vụ streaming và analytics - EMR, Kinesis, Firehose, Lake Formation, Redshift và QuickSight.

---

### Mục tiêu trong ngày

- Xây dựng và kiểm thử một **data pipeline end-to-end đơn giản** bằng Infrastructure as Code (AWS CDK).
- Sử dụng **Floci** và **LocalStack (ministack)** là các emulator AWS cục bộ để xác thực kiến trúc mà không phát sinh chi phí AWS thực.
- Hiểu vai trò và use case của **Amazon EMR**, **Amazon Kinesis**, **Kinesis Data Streams**, **Amazon Data Firehose**, **AWS Lake Formation**, **Amazon Redshift** và **Amazon QuickSight** trong một nền tảng dữ liệu hiện đại.

---

### Thực hành: Thử nghiệm Data Pipeline đơn giản bằng IaC

#### Tổng quan kiến trúc

Mục tiêu là thiết kế và kiểm thử cục bộ một data pipeline nhỏ bao phủ toàn bộ hành trình của dữ liệu - từ ingestion đến storage đến query:

```
[Nguồn dữ liệu / Event Generator]
         ↓
[Kinesis Data Streams]  ← thu thập dữ liệu thời gian thực
         ↓
[Amazon Data Firehose]  ← đệm dữ liệu + chuyển đổi định dạng + giao nhận
         ↓
[Amazon S3]             ← lưu trữ data lake thô
         ↓
[AWS Glue Crawler]      ← phát hiện schema tự động
         ↓
[Amazon Athena]         ← truy vấn SQL trực tiếp trên dữ liệu thô
         ↓
[Amazon Redshift]       ← kho dữ liệu có cấu trúc
         ↓
[Amazon QuickSight]     ← dashboard & trực quan hóa
```

Toàn bộ kiến trúc được định nghĩa bằng **AWS CDK (TypeScript)** - nghĩa là toàn bộ hạ tầng có thể deploy, xóa và deploy lại chỉ bằng một lệnh duy nhất.

#### Kiểm thử cục bộ với Floci và LocalStack

Thay vì deploy lên AWS thực ngay, kiến trúc được xác thực trước bằng hai emulator mã nguồn mở:

**Floci:**
- Emulator AWS cục bộ nhẹ, được xây dựng để khởi động nhanh và sử dụng ít tài nguyên.
- **Nhanh hơn 138 lần** và **tiết kiệm bộ nhớ hơn 11 lần** so với LocalStack Community.
- Dùng để giả lập S3, Lambda và DynamoDB cục bộ cho vòng lặp phát triển nhanh - viết code, test ngay, không rủi ro billing.
- Lý tưởng cho giai đoạn đầu xác thực kiến trúc trước khi cam kết với hạ tầng thực.

**LocalStack (ministack):**
- Emulator AWS toàn diện hơn, hỗ trợ nhiều dịch vụ hơn.
- Dùng ở đây để giả lập Kinesis Data Streams và Firehose cục bộ.
- Chạy dưới dạng Docker container: `docker run -p 4566:4566 localstack/localstack`.
- Cho phép kiểm thử toàn bộ luồng pipeline - produce events → Kinesis → Firehose → S3 → Athena query - hoàn toàn trên máy cục bộ.

**Tại sao nên test cục bộ trước?**
- Chi phí bằng không trong quá trình lặp - không rủi ro vô tình để Kinesis shard hoặc Redshift cluster chạy.
- Vòng phản hồi nhanh hơn - deploy và test trong vài giây, không phải vài phút.
- Xác thực logic IaC trước khi chạm vào tài khoản AWS production hoặc ngay cả dev.

**Cấu trúc CDK stack đã kiểm thử:**
```typescript
// CDK snippet đơn giản hóa - data pipeline stack
const stream = new kinesis.Stream(this, 'DataStream', {
  streamName: 'fcaj-data-stream',
  shardCount: 1,
});

const bucket = new s3.Bucket(this, 'DataLakeBucket', {
  bucketName: 'fcaj-data-lake',
  removalPolicy: cdk.RemovalPolicy.DESTROY,
});

const firehose = new kinesisFirehose.DeliveryStream(this, 'FirehoseStream', {
  sourceStream: stream,
  destinations: [new destinations.S3Bucket(bucket)],
});
```

Sau khi xác thực cục bộ với Floci/LocalStack, chính code CDK đó deploy giống hệt lên AWS thực - không cần viết lại.

---

### Lý thuyết: Amazon EMR (Elastic MapReduce)

**Amazon EMR** là nền tảng big data được quản lý, chạy các framework mã nguồn mở như **Apache Spark**, **Apache Hive**, **Hadoop**, **Presto** và **Flink** trên các cluster EC2 có thể mở rộng - hoặc serverless thông qua **EMR Serverless**.

**Khi nào dùng EMR:**
- Xử lý **hàng terabyte đến petabyte** dữ liệu - batch ETL ở quy mô khổng lồ.
- Chạy các **Spark ML pipeline** phức tạp trên cluster phân tán.
- Xử lý big data tiết kiệm chi phí bằng **Spot Instances** (rẻ hơn đến 90% so với On-Demand).
- Team đã quen với hệ sinh thái Spark/Hadoop và cần hạ tầng được quản lý trên cloud.

**Các chế độ triển khai EMR:**
| Chế độ | Phù hợp nhất cho |
|--------|----------------|
| **EMR on EC2** | Kiểm soát đầy đủ cấu hình cluster, job chạy lâu dài |
| **EMR Serverless** | Spark/Hive serverless - không quản lý cluster, tự động scale về 0 |
| **EMR on EKS** | Chạy Spark trên Kubernetes clusters |

**Các khái niệm EMR quan trọng:**
- **Primary node:** Điều phối cluster - quản lý lên lịch job và phân bổ tài nguyên.
- **Core nodes:** Chạy task VÀ lưu dữ liệu trong HDFS - phải luôn có.
- **Task nodes:** Chỉ chạy task - không lưu trữ, có thể là Spot Instances an toàn (không mất dữ liệu nếu bị thu hồi).
- **Bootstrap actions:** Script tùy chỉnh chạy trên mọi node khi cluster khởi động - cài dependencies, cấu hình cài đặt.
- **EMR Steps:** Đơn vị công việc được submit lên cluster (ví dụ: "chạy Spark job này").

> **Bài học:** EMR Serverless là điểm khởi đầu đúng đắn cho hầu hết workload - nó loại bỏ hoàn toàn việc quản lý cluster và scale về 0 khi không hoạt động, tiết kiệm chi phí hơn nhiều so với duy trì cluster chạy 24/7.

---

### Lý thuyết: Amazon Kinesis - Streaming Data Thời Gian Thực

**Amazon Kinesis** là nhóm dịch vụ để thu thập, xử lý và phân tích dữ liệu streaming thời gian thực ở quy mô lớn.

#### Kinesis Data Streams

**Kinesis Data Streams** là dịch vụ streaming dữ liệu thời gian thực có thể mở rộng lớn và bền vững - hãy tưởng tượng nó như một message bus thông lượng cao, độ trễ thấp cho dữ liệu.

**Các khái niệm cốt lõi:**
- **Shards:** Đơn vị capacity. Mỗi shard xử lý **1 MB/s write** và **2 MB/s read**.
- **Partition Key:** Xác định record đi vào shard nào - chọn cẩn thận để tránh "hot shards" khi một shard nhận traffic không cân đối.
- **Sequence Number:** Định danh duy nhất cho mỗi record trong một shard - đảm bảo thứ tự trong một shard.
- **Retention Period:** Dữ liệu lưu **24 giờ mặc định**, có thể mở rộng lên **365 ngày**.
- **Consumers:** Ứng dụng đọc từ stream - Lambda, Kinesis Data Analytics, Firehose, custom EC2/ECS apps.

**Use cases:** Phân tích thời gian thực, thu thập log và event data, phân tích clickstream, thu thập telemetry IoT.

#### Amazon Data Firehose

**Amazon Data Firehose** (trước đây là Kinesis Data Firehose) là dịch vụ fully managed **tải dữ liệu streaming** đáng tin cậy vào các data store - S3, Redshift, OpenSearch, Splunk - với đệm, nén và chuyển đổi tùy chọn tích hợp.

**Tính năng quan trọng:**
- **Nguồn:** Kinesis Data Streams, Amazon MSK, Direct PUT (HTTP endpoint), CloudWatch Logs.
- **Đích:** Amazon S3, Amazon Redshift, Amazon OpenSearch, HTTP endpoints, Splunk.
- **Buffering:** Tích lũy dữ liệu trước khi giao nhận - có thể cấu hình theo **kích thước** (1–128 MB) hoặc **thời gian** (60–900 giây).
- **Lambda transformation:** Tùy chọn gọi Lambda function để transform records inline trước khi giao nhận - chuyển đổi định dạng, lọc, làm giàu dữ liệu.
- **Format conversion:** Tự động chuyển đổi JSON sang Parquet hoặc ORC để lưu trữ S3 tiết kiệm chi phí (không cần ETL job thủ công).
- **Fully managed:** Không có shards, không lập kế hoạch capacity - chỉ cấu hình nguồn → đích và nó tự động mở rộng.

> **Phân biệt quan trọng:** Kinesis Data Streams = độ trễ thấp, logic consumer tùy chỉnh, toàn quyền kiểm soát. Firehose = pipeline giao nhận không cần quản lý vào data store, với đệm tích hợp.

---

### Lý thuyết: AWS Lake Formation

**AWS Lake Formation** là dịch vụ managed giúp **thiết lập, bảo mật và quản lý data lake dễ dàng hơn** - cung cấp một nơi duy nhất để định nghĩa và thực thi kiểm soát truy cập chi tiết trên toàn bộ tài nguyên data lake.

**Khả năng cốt lõi:**
- **Quyền tập trung:** Định nghĩa ai có thể truy cập **database, bảng, cột và hàng** nào trên S3, Glue, Athena và Redshift Spectrum từ một nơi.
- **Column-level security:** Giới hạn truy cập đến các cột cụ thể - ví dụ analyst có thể thấy dữ liệu bán hàng nhưng không thấy cột PII.
- **Row-level filtering:** Giới hạn hàng dựa trên danh tính hoặc thuộc tính của người yêu cầu.
- **Tích hợp Data Catalog:** Hoạt động trực tiếp với AWS Glue Data Catalog - cùng metadata catalog được Athena và Glue sử dụng.
- **Chia sẻ dữ liệu cross-account:** Chia sẻ an toàn tài sản dữ liệu được quản trị trên các tài khoản AWS mà không cần sao chép dữ liệu.
- **Governed Tables:** Định dạng bảng Lake Formation gốc với nén tự động, ACID transactions và row-level security.

**Mô hình quyền Lake Formation:**
Thay vì quản lý hàng chục S3 bucket policies và IAM policies độc lập, Lake Formation cung cấp mô hình **grant/revoke** thống nhất:
```
GRANT SELECT ON DATABASE analytics_db TO IAM ROLE data-analyst-role
GRANT SELECT ON TABLE sales.orders (order_id, amount, date) TO IAM ROLE reporting-role
  -- lưu ý: cột customer_name KHÔNG được cấp → tự động ẩn cột
```

> **Bài học:** Lake Formation là lớp quản trị đúng đắn cho bất kỳ nền tảng dữ liệu đa team nào - nó ngăn ngừa "permission sprawl" xảy ra khi mỗi team tự quản lý S3 bucket policies của mình.

---

### Lý thuyết: Amazon Redshift

**Amazon Redshift** là **kho dữ liệu đám mây (data warehouse)** fully managed của AWS - được thiết kế cho các truy vấn phân tích phức tạp (OLAP) trên dữ liệu có cấu trúc ở quy mô petabyte, sử dụng columnar storage và massively parallel processing (MPP).

**Các khái niệm cốt lõi:**
- **Columnar storage:** Dữ liệu được lưu theo cột thay vì theo hàng - tăng tốc đáng kể các truy vấn tổng hợp chỉ chạm vào một vài cột trong nhiều cột.
- **MPP (Massively Parallel Processing):** Truy vấn được phân phối trên nhiều compute nodes và xử lý song song.
- **Leader node:** Điều phối lập kế hoạch truy vấn và phân phối công việc cho compute nodes.
- **Compute nodes:** Thực thi các đoạn truy vấn và trả kết quả về leader node.
- **Redshift Spectrum:** Truy vấn dữ liệu trực tiếp trên S3 (Parquet, ORC, CSV) mà không cần tải vào Redshift - chỉ trả tiền cho dữ liệu được quét.
- **RA3 nodes:** Tách biệt storage (S3-backed) với compute - mở rộng mỗi cái độc lập.

**Khi nào chọn Redshift thay vì Athena:**
| | Athena | Redshift |
|-|--------|----------|
| **Vị trí dữ liệu** | Luôn trong S3 | Được tải vào Redshift (hoặc Spectrum cho S3) |
| **Độ phức tạp truy vấn** | SQL đơn giản đến trung bình | Truy vấn phân tích phức tạp, JOINs ở quy mô lớn |
| **Mô hình chi phí** | Theo truy vấn (tính per TB quét) | Theo giờ cluster (hoặc Serverless per RPU) |
| **Phù hợp nhất** | Khám phá ad-hoc, truy vấn không thường xuyên | Workload phân tích lặp lại, concurrency cao |
| **Hiệu suất** | Giây đến phút | Dưới giây cho truy vấn cache/tối ưu |

**Redshift Serverless:** Tùy chọn managed - không provisioning cluster, tự động mở rộng capacity (RPUs) theo workload, tính tiền per giây compute được sử dụng.

> **Bài học:** Redshift là lựa chọn đúng khi bạn có một tập hợp các truy vấn phân tích định kỳ được xác định rõ với nhiều người dùng đồng thời - nó xử lý tối ưu hóa (distribution keys, sort keys, materialized views) làm cho các truy vấn phức tạp nhanh ở quy mô lớn.

---

### Lý thuyết: Amazon QuickSight

**Amazon QuickSight** là dịch vụ **Business Intelligence (BI) và trực quan hóa dữ liệu** cloud-native của AWS - kết nối với các nguồn dữ liệu, xây dựng dashboard tương tác và chia sẻ insights trong tổ chức.

**Tính năng quan trọng:**
- **SPICE (Super-fast, Parallel, In-memory Calculation Engine):** Engine dữ liệu in-memory pre-aggregate dữ liệu cho tương tác dashboard dưới giây - không cần re-query nguồn mỗi khi tương tác.
- **Nguồn dữ liệu:** Amazon S3 (qua Athena), Redshift, RDS, Aurora, DynamoDB, Salesforce và 50+ connectors.
- **ML Insights tích hợp sẵn:** Phát hiện bất thường, dự báo và tường thuật ngôn ngữ tự nhiên (tóm tắt văn bản tự động về xu hướng biểu đồ) - không cần chuyên môn data science.
- **Amazon Q in QuickSight:** Đặt câu hỏi bằng ngôn ngữ tự nhiên ("Hiển thị doanh số theo vùng quý trước") và QuickSight tự động tạo biểu đồ.
- **Embedded analytics:** Nhúng QuickSight dashboards trực tiếp vào ứng dụng web của bạn bằng Embedding SDK.
- **Mô hình định giá:** Giá per-session cho readers (tính per phiên 30 phút, tối đa $5/user/tháng) so với giá per-author cho người tạo dashboard.

**QuickSight trong data pipeline:**
QuickSight nằm ở cuối của data pipeline - đọc từ Athena (cho khám phá ad-hoc dữ liệu S3 thô) hoặc trực tiếp từ Redshift (cho truy vấn phân tích hiệu suất cao trên dữ liệu được tuyển chọn).

---

### Toàn bộ pipeline: Cách các dịch vụ kết nối với nhau

| Lớp | Dịch vụ | Vai trò |
|-----|---------|---------|
| **Ingestion** | Kinesis Data Streams | Thu thập event thời gian thực |
| **Delivery** | Amazon Data Firehose | Đệm + chuyển đổi định dạng + giao nhận vào S3 |
| **Storage** | Amazon S3 | Data lake thô và đã xử lý |
| **Catalog** | AWS Glue Data Catalog | Phát hiện schema và metadata |
| **Governance** | AWS Lake Formation | Kiểm soát truy cập tập trung |
| **Processing** | Amazon EMR (Spark) | Batch ETL transformation quy mô lớn |
| **Warehouse** | Amazon Redshift | Kho phân tích hiệu suất cao, có tuyển chọn |
| **Ad-hoc Query** | Amazon Athena | SQL trên dữ liệu S3 thô mà không cần tải |
| **Visualization** | Amazon QuickSight | Dashboard, báo cáo và ML insights |

---

### Bài học rút ra

- **Test cục bộ trước** (Floci + LocalStack) là kỷ luật tiết kiệm cả thời gian lẫn tiền bạc - xác thực kiến trúc IaC cục bộ trước khi chạm vào AWS thực.
- **Kinesis Data Streams vs. Firehose** là điểm nhầm lẫn phổ biến: Streams = độ trễ thấp + consumer tùy chỉnh; Firehose = giao nhận không cần quản lý vào data store.
- **Lake Formation** là câu trả lời cho "làm thế nào để quản trị truy cập data lake trên nhiều team" - nó thay thế sự hỗn loạn của các S3 bucket policies riêng lẻ.
- **Redshift vs. Athena** là về pattern workload: ad-hoc trên S3 = Athena, truy vấn lặp lại concurrency cao trên dữ liệu có cấu trúc = Redshift.
- **SPICE engine của QuickSight** là thứ làm cho dashboard cảm giác tức thì - pre-aggregation là chìa khóa cho BI tương tác ở quy mô lớn.
- Nhìn thấy tất cả các dịch vụ này kết nối trong một sơ đồ pipeline duy nhất làm cho hệ sinh thái nền tảng dữ liệu AWS trở nên trực quan hơn nhiều so với việc học từng dịch vụ riêng lẻ.

---

*Nguồn tài liệu chính: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
