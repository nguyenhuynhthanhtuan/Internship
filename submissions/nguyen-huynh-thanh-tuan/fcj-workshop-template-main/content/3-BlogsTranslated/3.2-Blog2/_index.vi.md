---
title: "Amazon RDS + AI + MCP"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

## 3.2 Amazon RDS + AI + MCP: Khi việc đọc log không còn là "ác mộng"

Trong quá trình học AWS, tôi thường đọc các bài trên AWS Blog để xem cách AWS giải quyết các bài toán thực tế, thay vì chỉ học lý thuyết về từng dịch vụ riêng lẻ.

Một bài viết khiến tôi khá ấn tượng là cách AWS kết hợp **Amazon CloudWatch**, **Amazon Bedrock**, **Kiro CLI** và **MCP (Model Context Protocol)** để hỗ trợ phân tích log của Amazon RDS bằng ngôn ngữ tự nhiên. Điều khiến tôi chú ý không chỉ là công nghệ AI, mà là cách các thành phần được ghép lại thành một quy trình vận hành hợp lý.

### 3.2.1 Quy trình hoạt động

Theo cách làm thông thường, người vận hành thường phải thực hiện nhiều bước thủ công:

- Mở CloudWatch
- Tìm đúng Log Group
- Viết CloudWatch Logs Insights query
- Chạy nhiều câu lệnh để lọc dữ liệu
- Đọc và tổng hợp hàng nghìn dòng log

Với hướng tiếp cận trong bài viết, AI có thể hỗ trợ phần lớn các bước trên. Luồng xử lý có thể hình dung như sau:

```text
Kiro CLI -> MCP Server -> CloudWatch -> RDS Logs -> AI tổng hợp kết quả -> Trả lời bằng ngôn ngữ tự nhiên
```

Cách làm này giúp giảm đáng kể thao tác thủ công khi cần kiểm tra tình trạng hệ thống hoặc điều tra sự cố liên quan đến database.

![Kiến trúc phân tích log RDS bằng Kiro, MCP và CloudWatch](/Internship/images/3-BlogsTranslated/blog-diagram-03.jpeg)

### 3.2.2 Điều tôi học được

Theo tôi, giá trị lớn nhất của bài viết không nằm ở việc "AI đọc log", mà nằm ở cơ chế kiểm soát quyền truy cập khi đưa AI vào hạ tầng cloud.

AI không truy cập trực tiếp vào tài nguyên AWS. Thay vào đó, **MCP Server** đóng vai trò trung gian. AI chỉ gọi các tool được MCP Server cho phép, CloudWatch vẫn là nơi truy xuất log, còn quyền truy cập vẫn được quản lý bằng IAM.

Điều này giúp việc tích hợp AI vào hạ tầng cloud trở nên an toàn và có kiểm soát hơn. AI không trở thành một thành phần có quyền tự do trong hệ thống, mà hoạt động trong phạm vi các công cụ và quyền hạn đã được định nghĩa.

### 3.2.3 Vì sao hướng tiếp cận này đáng chú ý?

Nếu làm việc với hệ thống có hàng chục hoặc hàng trăm database, lượng log phát sinh mỗi ngày sẽ rất lớn. Trong nhiều trường hợp, người vận hành không thiếu dữ liệu; điều thiếu là khả năng nhanh chóng tìm đúng thông tin cần thiết.

Việc có thể đặt các câu hỏi bằng ngôn ngữ tự nhiên sẽ giúp rút ngắn đáng kể thời gian điều tra:

- Database nào có nhiều lỗi kết nối nhất hôm nay?
- Có truy vấn nào chạy quá 10 giây trong 24 giờ qua không?
- Thời điểm CPU tăng cao có xuất hiện lỗi bất thường không?

Thay vì phải tự viết query, lọc log và tổng hợp kết quả, người vận hành có thể nhận được câu trả lời đã được phân tích sơ bộ, sau đó tiếp tục kiểm chứng bằng dữ liệu gốc trên CloudWatch.

### 3.2.4 Góc nhìn cá nhân

Tôi xem đây là một ví dụ rõ về cách AI đang thay đổi hoạt động quản trị hệ thống. Trước đây, AI thường được nhắc đến trong chatbot hoặc sinh nội dung. Hiện tại, AI đang dần trở thành một trợ lý vận hành, hỗ trợ kỹ sư cloud xử lý những công việc lặp lại như đọc log, phân tích cảnh báo và tổng hợp dữ liệu.

Từ bài viết này, tôi thấy kỹ năng của Cloud Engineer trong tương lai có thể không chỉ dừng ở việc biết sử dụng dịch vụ AWS. Người kỹ sư cũng cần biết cách kết hợp AI với các dịch vụ cloud sao cho an toàn, có kiểm soát và thật sự giúp tăng hiệu quả vận hành.

**Nguồn tham khảo:** [Amazon RDS log analysis using natural language queries with Kiro and MCP](https://aws.amazon.com/vi/blogs/database/amazon-rds-log-analysis-natural-language-queries-with-kiro-and-mcp/)
