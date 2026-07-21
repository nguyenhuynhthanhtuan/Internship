---
title: "Event 2"
date: 2026-07-04
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

## DATA DRIVEN, AI RISEN

- **Thời gian:** 27/06/2026
- **Địa điểm:** Tổ chức trực tiếp tại tầng 26, tòa nhà Bitexco, đồng thời phát sóng livestream trên YouTube
- **Vai trò:** Người tham dự (online)

## Mục đích của sự kiện

- Chia sẻ kinh nghiệm và góc nhìn thực tế từ môi trường doanh nghiệp.
- Trình bày các giải pháp ứng dụng AI vào Cloud operations, Voice AI, HR và bảo mật hệ thống AI.
- Giúp người tham dự hình dung cách AI tạo giá trị trong các hệ thống production hiện đại.

## Danh sách diễn giả

- Steve Trần - Founder của Cloud Thinker.
- Hiếu Nghị - Renova Cloud.
- Kiệt - Student Video Group / AWS Study Builder.
- Trung - Founder và CEO của R AI.
- Bảo và Nguyên Nguyễn - Cloud Engineer đến từ Cloud Kinetics.
- Trường (Gwen) và Minh Anh - Noventis.
- Toàn Nguyễn - AWS Security Builder.

## Nội dung nổi bật

### AI trong vận hành Cloud Infrastructure

Cloud Thinker giới thiệu cách AI hỗ trợ kỹ sư vận hành hạ tầng trong điều tra incident, tự động hóa FinOps, kiểm thử bảo mật và code review. Điều này liên hệ trực tiếp với các nội dung monitoring, log và incident handling trong dự án IRMS.

### Voice AI dành riêng cho tiếng Việt

Các diễn giả trình bày kiến trúc Speech-to-Text -> LLM -> Text-to-Speech để xử lý tốt hơn đặc thù tiếng Việt. Hệ thống còn có khả năng interrupt, nhận diện ngữ cảnh và tool calling.

### DevOps AI Agent

Cloud Kinetics chia sẻ về agent tự động tổng hợp logs, traces, metric và đề xuất root cause/mitigation plan. Tôi thấy đây là hướng rất gần với bài toán observability và incident response.

### AI trong quản trị nhân sự

Noventis trình bày cách AI hỗ trợ đọc JD, phân tích CV, chấm điểm kỹ năng và đề xuất mức lương dựa trên dữ liệu.

### Bảo mật kết nối giữa AI và máy chủ

Phần này nhấn mạnh rủi ro khi kết nối AI với hệ thống bên thứ ba qua public internet. Hướng tiếp cận an toàn hơn là đặt MCP server trong private subnet và dùng VPC Connection/ALB/Route 53 Resolver.

## Những gì học được

- AI không thay thế hoàn toàn con người, mà khuếch đại năng lực của người có nền tảng tốt.
- Một hệ thống AI hiệu quả cần observability tốt: log, metric, trace và lịch sử thay đổi rõ ràng.
- Bảo mật kết nối giữa AI và hệ thống backend là vấn đề quan trọng, đặc biệt khi AI có quyền thực hiện hành động.

## Ứng dụng vào công việc

- Viết CV bám sát JD hơn vì nhiều hệ thống HR đã dùng AI để screening.
- Khi xây dựng hệ thống như IRMS, cần lưu log, metric và history đủ rõ để hỗ trợ debug và phân tích sự cố.
- Có thể thử nghiệm AI workflow/agent để tự động hóa một số tác vụ lặp lại, nhưng vẫn cần con người kiểm chứng kết quả.

## Trải nghiệm trong event

Sự kiện giúp tôi nhìn thấy AI trong môi trường doanh nghiệp không còn là ý tưởng xa vời mà đã đi vào vận hành cloud, bảo mật, HR và voice agent. Các live demo như mô phỏng lỗi ECS rồi dùng AI debug hoặc gọi trực tiếp cho Voice Agent làm nội dung trở nên rất thực tế và dễ hình dung.
