---
title: "Ngày 1"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

> **Ngày 1 - Thứ 2, 20/04/2026:** Kích hoạt tài khoản, tích lũy AWS credit và hoàn thành 5 nhiệm vụ thực hành.

---

### Mục tiêu trong ngày

- Tạo và thiết lập tài khoản AWS, nhận **$100 credit khởi động** từ AWS.
- Hoàn thành **5 Tasks** thực hành để tích lũy thêm **$100 credit** (+$20 mỗi task).
- Thực hành trực tiếp với 5 dịch vụ AWS cốt lõi: EC2, Bedrock, Budgets, Lambda, RDS.
- Nắm vững các khái niệm cơ bản về bảo mật và quản lý chi phí AWS.

---

### Task 1: Launch EC2 Instance - +$20 Credit

**Mục tiêu:** Tạo và quản lý máy ảo (Virtual Machine) trên AWS Cloud.

**Amazon EC2 (Elastic Compute Cloud)** là dịch vụ điện toán cốt lõi của AWS, cho phép khởi tạo máy chủ ảo với nhiều cấu hình phần cứng và hệ điều hành linh hoạt.

**Các bước thực hiện:**
1. Truy cập **AWS Console** → Widget "Explore AWS" → Chọn task **Launch an instance using EC2**.
2. Nhấn **Start activity** để bắt đầu.
3. Đặt tên Instance: `Test Instance` → Chọn AMI phù hợp.
4. Giữ nguyên cấu hình phần cứng mặc định.
5. Tạo Key Pair mới: Tên `first-kp` | Loại **RSA** | Định dạng **.pem**.
6. Tạo Security Group với các quy tắc mặc định.
7. Review cấu hình → **Launch Instance**.
8. **Dọn dẹp:** Terminate instance sau khi hoàn thành để tránh phát sinh chi phí.

> **Bài học:** Luôn dọn dẹp tài nguyên ngay sau khi thực hành. Một EC2 instance bỏ chạy qua đêm có thể tiêu hết lượng credit của cả một tuần mà không hay biết.

---

### Task 2: Amazon Bedrock Playground - +$20 Credit

**Mục tiêu:** Trải nghiệm AI/ML với các Foundation Models trên AWS.

**Amazon Bedrock** là dịch vụ fully-managed cung cấp quyền truy cập vào các foundation model hàng đầu (Claude, Llama, Titan,...) thông qua một API thống nhất - mà không cần quản lý bất kỳ hạ tầng nào bên dưới.

**Các bước thực hiện:**
1. Truy cập **Bedrock Console** → Chọn task **Use a foundation model in Amazon Bedrock**.
2. Chọn model: **Claude 3 Haiku** (cân bằng tốt giữa hiệu suất và chi phí).
3. Điền thông tin use case và submit, đợi phê duyệt. Nếu gặp lỗi authorization, tạo **Support Case** với AWS (Category: Bedrock Allowlisting).
4. Sau khi được phê duyệt: Viết prompt test → **Run** → Review kết quả → **Finish**.

> **Bài học:** Một số dịch vụ AI của AWS yêu cầu phê duyệt use case trước khi sử dụng - đây là cơ chế kiểm soát sử dụng có trách nhiệm (Responsible AI). Viết mô tả use case rõ ràng và hợp lệ sẽ giúp quá trình phê duyệt diễn ra nhanh hơn đáng kể.

---

### Task 3: Set up AWS Budgets - +$20 Credit

**Mục tiêu:** Thiết lập hệ thống giám sát và cảnh báo chi phí.

**AWS Budgets** cho phép đặt ngưỡng chi phí và nhận thông báo tự động khi sắp vượt mức cho phép - ngăn ngừa hóa đơn bất ngờ cuối tháng.

**Các bước thực hiện:**
1. Truy cập **Budgets Console** → Chọn task **Set up a cost budget using AWS Budgets**.
2. Nhấn **Start activity** → Cấu hình thông số budget.
3. Nhập địa chỉ email nhận thông báo → **Create budget**.
4. Budget được tạo thành công với thông báo email tự động.

> **Bài học:** Thiết lập budget ngay từ Ngày 1 là bắt buộc trong môi trường thực tập với AWS credit có giới hạn. Một tài nguyên cấu hình sai bỏ chạy qua đêm có thể tiêu hết credit của cả một tuần.

---

### Task 4: Create Lambda Web App - +$20 Credit

**Mục tiêu:** Xây dựng serverless web application trên AWS.

**AWS Lambda** là dịch vụ compute serverless - chạy code mà không cần quản lý server, chỉ trả tiền cho thời gian thực thi thực tế. Không có idle capacity, không over-provisioning.

**Các bước thực hiện:**
1. Truy cập **Lambda Console** → Chọn task **Create a web app using AWS Lambda**.
2. Nhấn **Start activity** → Chọn **Use a blueprint** → **Getting started with Lambda HTTP**.
3. Đặt tên function: `http-function-url-tutorial`.
4. Tick **Acknowledgment** → **Create function** thành công.
5. **Dọn dẹp:** Delete function sau khi hoàn thành.

> **Bài học:** Kiến trúc serverless loại bỏ hoàn toàn nhu cầu provisioning server - phù hợp cho các workload có traffic không đều. Một Lambda function không nhận request nào thì chi phí bằng $0.

---

### Task 5: Create RDS Database - +$20 Credit

**Mục tiêu:** Thiết lập managed relational database trên AWS.

**Amazon RDS (Relational Database Service)** quản lý toàn bộ vòng đời của database: backup tự động, vá lỗi OS, scaling và failover - giúp developer tập trung vào thiết kế schema và logic ứng dụng thay vì công việc DBA.

**Các bước thực hiện:**
1. Truy cập **RDS Console** → Chọn task **Create an Amazon RDS Database**.
2. Chọn **Easy create** → Engine: **Aurora (PostgreSQL Compatible)**.
3. **Create database** → Đợi trạng thái chuyển sang **Available**.
4. **Dọn dẹp (theo đúng thứ tự):** Xóa instance `database-1-instance-1` trước, sau đó xóa cluster `database-1`.

> **Lưu ý quan trọng:** Phải xóa instance trước rồi mới xóa cluster - cố xóa cluster trước sẽ gặp lỗi dependency và bị chặn lại.

---

### Bài học rút ra

- **$200 AWS Credit** tích lũy thành công: $100 khởi động từ AWS + $100 từ 5 tasks thực hành.
- **5 dịch vụ AWS cốt lõi** được thực hành trực tiếp: EC2, Bedrock, Budgets, Lambda, RDS.
- Hiểu chính sách **Responsible AI** của AWS - quyền truy cập model không tự động, cần phê duyệt use case.
- Hình thành thói quen **"dọn dẹp sau mỗi buổi thực hành"** - kỷ luật tiết kiệm chi phí quan trọng nhất trong môi trường credit có giới hạn.
- Nhận ra rằng các guided tasks trên AWS Console là cách tuyệt vời để khám phá dịch vụ lần đầu với rủi ro thấp nhất.

---

*Nguồn tài liệu chính: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
