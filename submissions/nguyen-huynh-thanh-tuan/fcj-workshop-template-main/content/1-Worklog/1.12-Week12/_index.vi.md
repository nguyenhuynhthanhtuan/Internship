---
title: "Tuần 12"
date: 2026-07-02
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

**Mốc thời gian:** 2/7 → 12/7 (5 ngày làm việc)

> Dự án IRMS được thực hiện bởi nhóm 5 thành viên. Các ghi chú dưới đây tập trung vào phần đóng góp cá nhân của tôi trong quá trình phối hợp với nhóm.

## Ngày 1 - 2/7: Hoàn thiện triển khai dự án IRMS

**Công việc đã thực hiện:** Tôi phối hợp với các thành viên trong nhóm để rà soát phiên bản hoàn thiện của hệ thống Incident Response Management System (IRMS) trước khi hoàn thành báo cáo thực tập. Tôi kiểm tra toàn bộ luồng hoạt động của hệ thống, bao gồm xác thực người dùng, quản lý sự cố, lưu trữ bằng chứng, xử lý thông báo và giám sát hệ thống. Tôi cũng đánh giá mức độ phù hợp giữa các chức năng đã triển khai với yêu cầu ban đầu và kiến trúc hệ thống đã thiết kế.

Tôi kiểm tra sự kết nối giữa các dịch vụ AWS như Cognito, API Gateway, Lambda, DynamoDB, S3, EventBridge, SNS, CloudWatch và CloudFront. Những vấn đề còn tồn tại trong quá trình kiểm thử được trao đổi với các thành viên trong nhóm để điều chỉnh trước khi hoàn thiện tài liệu cuối cùng.

**Kiến thức đã học:**Việc rà soát hệ thống cuối cùng là cần thiết để đảm bảo kết quả triển khai phù hợp với kiến trúc đã đề ra và mục tiêu của dự án.


**Kết quả đạt được:** Các luồng chính gồm authentication, Incident CRUD, Timeline và Evidence Upload ổn định hơn để tiếp tục test end-to-end.

**Khó khăn và bài học:** CORS và JWT là hai lỗi dễ gây nhầm lẫn, nên cần tách lỗi xác thực, lỗi route và lỗi backend khi debug.

## Ngày 2 - 4/7: Viết và tổ chức báo cáo thực tập

**Công việc đã thực hiện:** Tôi bắt đầu hoàn thiện báo cáo thực tập bằng cách tổng hợp quá trình đào tạo AWS, các giai đoạn phát triển dự án, quá trình triển khai kỹ thuật, kiểm thử hệ thống và kết quả đạt được. Tôi xây dựng cấu trúc báo cáo dựa trên tiến trình thực tế của kỳ thực tập, bao gồm quá trình tìm hiểu dịch vụ AWS, nghiên cứu kiến trúc Serverless, phát triển hệ thống IRMS và đánh giá hoạt động của hệ thống.

Tôi cũng rà soát hình ảnh minh họa, sơ đồ kiến trúc và phần giải thích kỹ thuật để đảm bảo báo cáo thể hiện rõ phần đóng góp cá nhân của tôi trong quá trình làm việc cùng nhóm năm thành viên.


Tôi kiểm tra `npm run build`, thư mục `dist/`, asset path và lỗi console trên trình duyệt. Sau khi upload frontend lên S3 và truy cập qua CloudFront, tôi test lại login, tạo incident, cập nhật trạng thái, upload evidence, xem timeline và gọi report. Khi gặp trường hợp trình duyệt vẫn lấy bundle cũ, tôi tạo CloudFront invalidation cho `/*` và dùng hard refresh để đối chiếu.

```bash
npm run build
aws s3 sync dist/ s3://<frontend-bucket> --delete
aws cloudfront create-invalidation --distribution-id <distribution-id> --paths "/*"
```

**Kiến thức đã học:** Một báo cáo kỹ thuật không chỉ mô tả những gì đã triển khai mà còn cần trình bày quá trình học tập, khó khăn, giải pháp và kết quả đạt được.

**Kết quả đạt được:** Frontend có thể chạy demo ổn định hơn qua CloudFront và gọi được các API chính.

**Khó khăn và bài học:** Nếu không invalidate cache, người xem có thể vẫn thấy giao diện hoặc JavaScript cũ dù source đã sửa.

## Ngày 3 - 7/7: Tổng hợp kết quả và chuẩn bị nội dung báo cáo cuối kỳ

**Công việc đã thực hiện:** Tôi tổng hợp các kết quả chính đạt được trong quá trình thực hiện dự án thực tập và chuẩn bị nội dung cho buổi báo cáo cuối kỳ. Nội dung trình bày tập trung vào kiến trúc AWS Serverless, các dịch vụ AWS được sử dụng, luồng hoạt động của hệ thống, các chức năng đã triển khai, kết quả kiểm thử và những kinh nghiệm rút ra.

Tôi rà soát vai trò của từng dịch vụ AWS trong hệ thống IRMS, bao gồm Cognito cho xác thực người dùng, Lambda xử lý logic backend, DynamoDB lưu trữ dữ liệu, S3 quản lý file bằng chứng, SNS/EventBridge xử lý luồng thông báo và CloudWatch phục vụ giám sát hệ thống.

**Kiến thức đã học:** Việc trình bày một dự án kỹ thuật cần giải thích được cả kiến trúc hệ thống và giá trị thực tế mà hệ thống mang lại.

**Kết quả đạt được:** Nội dung Workshop dễ đọc hơn, đồng bộ hơn giữa tiếng Việt và tiếng Anh, và phản ánh đúng phần AWS mà tôi tham gia triển khai.

**Khó khăn và bài học:** Tài liệu copy từ quá trình làm việc thường bị dính câu, sai format hoặc thiếu context, nên phải kiểm tra từng trang thay vì sửa một lần chung chung.

## - 10/7: Rà soát báo cáo cuối cùng và điều chỉnh theo phản hồi

**Công việc đã thực hiện:** Tôi kiểm tra lại toàn bộ báo cáo thực tập đã hoàn thiện và điều chỉnh nội dung dựa trên phản hồi từ các thành viên trong nhóm và người hướng dẫn. Tôi rà soát tính chính xác của phần mô tả dịch vụ AWS, tiến độ dự án, sơ đồ kiến trúc và phần mô tả đóng góp cá nhân.

Tôi cũng kiểm tra lại định dạng, tài liệu tham khảo, hình ảnh và các thuật ngữ kỹ thuật để đảm bảo báo cáo có nội dung rõ ràng và chuyên nghiệp. Những phần giải thích chưa rõ được chỉnh sửa lại nhằm mô tả chính xác hơn quá trình triển khai và các bài học đạt được.

**Kiến thức đã học:** Phản hồi đóng vai trò quan trọng trong việc cải thiện tài liệu kỹ thuật và đảm bảo báo cáo cuối cùng phản ánh đúng kết quả của dự án.

**Kết quả đạt được:** Báo cáo nhất quán hơn về timeline, kiến trúc, vai trò cá nhân và cách gọi tên dịch vụ AWS.

**Khó khăn và bài học:** Khi sửa nhiều file song ngữ, cần kiểm tra cả ý nghĩa lẫn format, không chỉ dịch từng câu.

## Ngày 5 - 12/7: Báo cáo cuối kỳ và tổng kết quá trình thực tập

**Công việc đã thực hiện:** Tôi tham gia buổi báo cáo cuối kỳ và trình bày những kết quả đạt được trong suốt quá trình thực tập. Nội dung trình bày bao gồm kiến thức AWS đã học, quá trình phát triển dự án IRMS, kiến trúc Serverless được triển khai và những đóng góp cá nhân trong nhóm.

Sau khi nhận được nhận xét từ giảng viên và đại diện doanh nghiệp, tôi tổng kết lại toàn bộ quá trình thực tập, bao gồm kỹ năng kỹ thuật, khả năng làm việc nhóm, giải quyết vấn đề và xây dựng tài liệu kỹ thuật. Tôi cũng xác định những kiến thức cần tiếp tục cải thiện trong lĩnh vực Cloud Computing và An ninh mạng.


**Kiến thức đã học:** Kỳ thực tập giúp tôi hiểu rõ hơn cách công nghệ điện toán đám mây được áp dụng trong các dự án thực tế, đồng thời cải thiện khả năng làm việc với các dịch vụ AWS trong môi trường nhóm.

**Kết quả đạt được:** Báo cáo phản ánh đúng tiến độ thực tế, vai trò cá nhân trong nhóm và trạng thái hoàn thành dự án đến ngày 21/07/2026.

**Khó khăn và bài học:** Càng về cuối càng dễ sửa một chỗ làm lệch chỗ khác, nên cần kiểm tra theo checklist thay vì chỉ nhìn bằng mắt.
