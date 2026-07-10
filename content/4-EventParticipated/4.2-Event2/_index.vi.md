---
title: "Event 2"
date: 2026-05-23
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Bài Thu Hoạch: AWS FIRST CLOUD AI JOURNEY COMMUNITY DAY

### Thông Tin Sự Kiện

| Thông tin | Chi tiết |
|-----------|----------|
| **Tên sự kiện** | AWS FIRST CLOUD AI JOURNEY COMMUNITY DAY |
| **Thời gian** | 09:00, ngày 23/05/2026 |
| **Địa điểm** | Tầng 26, tòa nhà Bitexco, số 02 đường Hải Triều, phường Sài Gòn, TP. Hồ Chí Minh |
| **Vai trò** | Người tham dự |

---

### Danh Sách Diễn Giả

- **Tinh Truong**
- **Anh Pham**
- **Thinh Nguyen**
- **Mai Nguyen**, **Uyen Le**, **Thao Nguyen** *(nhóm)*
- **Duc Dao**
- **Vy Lam**

---

### Chương Trình Sự Kiện

| Thời gian | Chủ đề | Diễn giả |
|-----------|--------|----------|
| 09:00 – 09:30 | Context Is Everything: Making AI Actually Work for You | Tinh Truong |
| 09:30 – 09:45 | Friendly AI Assistant with Amazon QuickSight Q | Anh Pham |
| 09:45 – 10:25 | From Edge To Origin: CloudFront as Your Foundation | Thinh Nguyen |
| 10:25 – 10:55 | 36 hrs with LotusHacks – Building UTMorpho from Idea to Reality | Mai Nguyen, Uyen Le, Thao Nguyen |
| 10:55 – 11:00 | ☕ Break | - |
| 11:00 – 11:30 | Non-Determinism of "Deterministic" LLM Settings | Duc Dao |
| 11:30 – 12:00 | Enterprise-Grade Multi-Agent System: The Case of Startup Credit Scoring | Vy Lam |

---

### Nội Dung Chi Tiết Từng Phần

#### 1. Context Is Everything: Making AI Actually Work for You
*Diễn giả: Tinh Truong*

Phần trình bày tập trung vào một sự thật mà nhiều người dùng AI thường bỏ qua: **vấn đề không nằm ở mô hình AI, mà nằm ở chất lượng ngữ cảnh bạn cung cấp**.

**Tại sao AI thường đưa ra kết quả không như ý?**
- AI không thể tự suy luận mục tiêu của bạn - nó chỉ hoạt động dựa trên những gì bạn cung cấp.
- Câu trả lời sai hướng, dài dòng hoặc bỏ lỡ ràng buộc đều xuất phát từ thiếu ngữ cảnh, không phải do mô hình kém.

**Ngữ cảnh tốt gồm những gì?**
- **Mục tiêu**: Bạn muốn đạt kết quả gì?
- **Tình huống**: Bạn là ai, dự án đang ở giai đoạn nào?
- **Ràng buộc**: Công nghệ sử dụng, phong cách, ngân sách, định dạng.
- **Bằng chứng liên quan**: Mã nguồn, tài liệu, ví dụ thực tế.

**Ba sai lầm phổ biến khi dùng AI:**
- Sao chép nguyên cả tài liệu dài vào chat - làm loãng tín hiệu và tốn token.
- Cung cấp thông tin hiển nhiên mà AI đã biết - lãng phí không gian ngữ cảnh hữu ích.
- Đặt câu lệnh mơ hồ - sẽ nhận lại câu trả lời mơ hồ.

**Sự tiến hóa của cách dùng AI:**
`Prompt → Context → Memory (Bộ não AI thứ hai)` - một hệ thống lưu trữ và học hỏi từ ghi chú, dự án của bạn để ngày càng trả lời chính xác hơn.

**Framework chuẩn bị trước khi hỏi AI:** Mục tiêu + Thông tin liên quan + Ràng buộc + Tiêu chí thành công.

> *"Tương lai không phải là cuộc chiến giữa con người và AI, mà là giữa những người biết làm việc với AI và những người không biết."*

---

#### 2. Friendly AI Assistant with Amazon QuickSight Q
*Diễn giả: Anh Pham*

**Amazon Quick Suite** - bộ công cụ AI đại diện (agentic AI) giúp doanh nghiệp chuyển từ dữ liệu sang hành động nhanh chóng, không cần kỹ năng lập trình.

**Các thành phần chính:**
- **Quick Chat Agent**: Trợ lý AI để khám phá và phân tích dữ liệu qua hội thoại.
- **Quick Flows**: Tạo luồng công việc thông minh bằng ngôn ngữ tự nhiên - không cần code.
- **Quick Spaces**: Không gian cộng tác chung, biến insight cá nhân thành tri thức của cả đội.
- **Quick Sight**: Xây dựng dashboard và báo cáo từ dữ liệu thô bằng ngôn ngữ tự nhiên.

**Hạ tầng kỹ thuật:**
- Kết nối hơn 40 nguồn dữ liệu (database, data warehouse, file upload...).
- Tích hợp Amazon Bedrock và tìm kiếm web.
- Thực hiện hàng nghìn hành động trên ứng dụng bên thứ ba qua API.
- Đảm bảo quản trị và bảo mật dữ liệu theo tiêu chuẩn doanh nghiệp.

**Ví dụ điển hình - PM Assistant:**
Tự động tạo biên bản họp → gửi email thông báo → lên lịch cuộc họp tiếp theo, tất cả không cần thao tác thủ công.

---

#### 3. From Edge To Origin: CloudFront as Your Foundation
*Diễn giả: Thinh Nguyen*

**Amazon CloudFront** được trình bày không chỉ như một CDN thông thường mà là **nền tảng bảo vệ và tối ưu hóa toàn diện** cho ứng dụng.

**Hạ tầng toàn cầu:**
Hơn 700 điểm hiện diện (PoPs) tại hơn 50 quốc gia, kết nối trực tiếp với các ISP lớn qua mạng cáp quang nội bộ của AWS.

**Tin tức nổi bật - Mô hình giá cố định (Flat-rate Pricing)** *(ra mắt 19/11/2025)*:
Giải quyết rủi ro hóa đơn tăng đột biến khi bị tấn công DDoS hoặc khi nội dung viral. Ba gói dịch vụ: Free/Pro, Business, Premium - mỗi gói đã bao gồm CloudFront, WAF, Anti-DDoS, Route 53 và S3.

**Bảo mật nhiều lớp:**
- Chặn DDoS ngay tại Edge - không có độ trễ 3-4 phút như giải pháp thông thường.
- SYN Proxy chống SYN Flood.
- Origin Cloaking (VPC Origin / OAC) ẩn hoàn toàn hạ tầng khỏi internet.
- Geo restrictions, Signed URLs/Cookies, TLS 1.3, mTLS.

**Tối ưu hiệu suất và chi phí:**
- Miễn phí truyền dữ liệu từ Origin AWS đến CloudFront.
- Cache đa tầng với Request Collapsing - gom hàng triệu request thành một request duy nhất.
- Hỗ trợ HTTP/3 (QUIC), nén dữ liệu giảm đến 82%, Persistent Connections và Edge Computing (CloudFront Functions / Lambda@Edge).

---

#### 4. 36 hrs with LotusHacks – Building UTMorpho from Idea to Reality
*Diễn giả: Mai Nguyen, Uyen Le, Thao Nguyen*

Câu chuyện về hành trình 36 giờ xây dựng **UTMorpho** - một công cụ AI tạo và chỉnh sửa giao diện người dùng (UI) theo mô hình WYSIWYG.

**UTMorpho giải quyết vấn đề gì?**
- Thay vì phải re-prompt AI mỗi khi muốn đổi màu hay kích thước nút, người dùng thao tác trực tiếp trên canvas.
- Thay đổi chỉ tác động đến phần được chọn - không làm hỏng phần còn lại.
- Smart diffing giúp các chỉnh sửa nhỏ tiêu tốn ít token, tiết kiệm chi phí.

**Hành trình xây dựng:**
1. Rời khỏi địa điểm hackathon để có không gian suy nghĩ tự do - ý tưởng đến từ chính sự ức chế khi dùng AI tools hàng ngày.
2. Phân chia nhiệm vụ nhanh dựa trên sự ăn ý của nhóm.
3. Xây dựng backend → AI calls đầu tiên → UI generator → inline editor → state sync.
4. Đối mặt giới hạn token, cắt giảm tính năng để tập trung vào demo cốt lõi.
5. Pitch 5 phút trước ban giám khảo.

**Bài học:**
- Ý tưởng tốt nhất đến từ nỗi đau thực tế của chính bản thân.
- Sự ăn ý trong đội thay thế được cho hàng chục cuộc họp.
- Khoảng cách và áp lực vừa đủ kích thích sáng tạo hơn là ép buộc.
- Xem AI (Claude, Bedrock) như đồng đội, không chỉ là công cụ.

---

#### 5. Non-Determinism of "Deterministic" LLM Settings
*Diễn giả: Duc Dao*

Một chủ đề ít được nói đến nhưng cực kỳ quan trọng với ai xây dựng sản phẩm bằng LLM: **Temperature = 0 không đảm bảo kết quả luôn giống nhau**.

**Cơ chế hoạt động:**
LLM sinh token theo từng bước - tính điểm logit → softmax → lấy mẫu. Với temp=0, lý thuyết sẽ luôn chọn token có xác suất cao nhất (argmax). Nhưng thực tế không như vậy.

**Bằng chứng thực nghiệm** *(nghiên cứu trên GPT-3.5 Turbo, GPT-4o, Llama-3, Mixtral)*:
- Độ chính xác chênh lệch tới **15%** giữa các lần chạy giống hệt nhau.
- Khoảng cách kết quả tốt nhất - tệ nhất lên tới **70%**.
- Tỷ lệ khớp văn bản chính xác gần bằng 0% ở các task khó.

**Nguyên nhân:**
- **Kỹ thuật**: Phép toán floating-point trên GPU không có tính kết hợp → sai số làm tròn nhỏ → đủ để lật kết quả argmax.
- **Thương mại**: API providers gộp request của nhiều người dùng vào một batch để tối ưu - thứ tự trong batch ảnh hưởng kết quả tính toán.

**Chiến lược giảm thiểu:**
- Majority voting: chạy nhiều lần, chọn câu trả lời phổ biến nhất.
- Dùng Structured Output (JSON mode / function calling) để thu hẹp không gian đầu ra.
- Self-hosted: kiểm soát hoàn toàn hạ tầng và tham số.
- Thiết kế hệ thống chấp nhận biến thiên ngay từ đầu.

> 💡 *Mẹo thực hành: Dùng temp=0.1 thay vì 0 để tránh vòng lặp lặp lại vô tận trong khi vẫn giữ sự ổn định cao.*

> ⚠️ *Không tin tuyệt đối vào temp=0 cho ứng dụng quan trọng (y tế, pháp lý, tài chính). LLM về bản chất là xác suất - hãy thiết kế hệ thống theo đúng bản chất đó.*

---

#### 6. Enterprise-Grade Multi-Agent System: The Case of Startup Credit Scoring
*Diễn giả: Vy Lam*

Phần trình bày về việc xây dựng hệ thống đa tác nhân (Multi-Agent System) để giải quyết bài toán **chấm điểm tín dụng startup** - lĩnh vực mà ngân hàng truyền thống thường thất bại.

**Vấn đề cốt lõi:**
Hệ thống tín dụng truyền thống yêu cầu 3+ năm báo cáo tài chính và tài sản thế chấp vật chất. Trong khi đó, startup thường chỉ có 6-18 tháng hoạt động, tài sản chủ yếu là IP và dữ liệu phi cấu trúc - đánh giá đòi hỏi chuyên môn sâu ở nhiều lĩnh vực cùng lúc.

**Tại sao cần Multi-Agent thay vì Single Agent?**
- Single Agent dễ bị loãng chuyên môn, giới hạn ngữ cảnh, thiếu cơ chế kiểm tra chéo.
- **Hội đồng tín dụng ảo** với các tác nhân chuyên biệt:
  - Manager: Điều phối chung
  - Chuyên gia tài chính: Đánh giá dòng tiền, burn rate
  - Chuyên gia thị trường: TAM/SAM/SOM, cạnh tranh
  - Người đánh giá đội ngũ: Hồ sơ sáng lập
  - Chuyên gia rủi ro & tuân thủ: Chính sách và giảm thiểu rủi ro

**6 trụ cột tiêu chuẩn doanh nghiệp:** Bảo mật, Quản trị dữ liệu, Mạng (VPC), Vận hành, Yếu tố con người, Tuân thủ (SOC 2, GDPR, PCI DSS).

**ROI ấn tượng:**
| Chỉ số | Trước | Sau |
|--------|-------|-----|
| Thời gian xử lý hồ sơ | 2–3 tuần | 2–4 giờ *(giảm 95%)* |
| Chi phí mỗi quyết định | ~100 triệu VNĐ | <5 triệu VNĐ |
| Tỷ lệ phê duyệt | 15–20% | 35–45% |

**Triển khai trên AWS:** Amazon Bedrock, AgentCore, Docker (ECR), API Gateway, VPC isolation, IAM least-privilege.

---

### Bài Học Rút Ra

#### Về AI và cách dùng hiệu quả
- **Ngữ cảnh là vua**: Một prompt rõ ràng, có đủ mục tiêu và ràng buộc luôn cho kết quả tốt hơn bất kỳ mô hình mạnh nào được dùng sai cách.
- **Đừng tin tuyệt đối vào "determinism"**: LLM hoạt động theo xác suất - thiết kế hệ thống phải chấp nhận sự biến thiên đó.
- **Agentic AI** không chỉ trả lời câu hỏi mà còn thực hiện hành động - đây là bước nhảy vọt cần nắm bắt sớm.

#### Về xây dựng sản phẩm
- Ý tưởng tốt nhất đến từ nỗi đau thực tế - UTMorpho là bằng chứng rõ ràng nhất.
- Từ POC đến sản phẩm thực: cần đầu tư vào bảo mật, quản trị, vận hành - không chỉ logic nghiệp vụ.
- AI là đồng đội, không chỉ là công cụ - cách bạn cộng tác với AI quyết định hiệu quả thực sự.

#### Về hành trình học tập
- Community Day là minh chứng: cộng đồng học hỏi cùng nhau tạo ra giá trị mà không tài liệu nào có thể thay thế được.
- Mỗi diễn giả chia sẻ từ kinh nghiệm thực tế - không có lý thuyết suông, toàn là bài học từ việc tự tay làm.

---

### Cảm Nhận Của Bản Thân

Community Day lần này khác hẳn một buổi hội thảo thông thường. Từ câu chuyện về ngữ cảnh AI cho đến hành trình hackathon 36 giờ, từ bài học về tính bất định của LLM đến hệ thống multi-agent cấp doanh nghiệp - tất cả đều được trình bày bởi những người đã thực sự làm, thực sự trải qua.

Điều mình mang về không chỉ là kiến thức kỹ thuật, mà là **cách nhìn**: AI không phải là thứ thần kỳ hay đáng sợ - nó là công cụ mạnh mẽ mà hiệu quả của nó phụ thuộc gần như hoàn toàn vào người dùng.

---

### Hình Ảnh Sự Kiện

![AWS First Cloud AI Journey Community Day](/images/event2.jpg)
