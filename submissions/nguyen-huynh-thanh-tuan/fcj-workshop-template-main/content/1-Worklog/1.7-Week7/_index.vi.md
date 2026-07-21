---
title: "Tuần 7"
date: 2026-05-28
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

**Mốc thời gian:** 28/5 → 3/6 (5 ngày làm việc)
## Ngày 1  - 28/5: Tổng quan về Amazon CloudWatch


**Công việc đã thực hiện:** TTôi tìm hiểu Amazon CloudWatch và cách AWS thực hiện giám sát ứng dụng, hạ tầng và các dịch vụ trên nền tảng Cloud. Trong quá trình thực hành, tôi nghiên cứu các thành phần của CloudWatch như Metrics, Logs, Log Groups, Log Streams, Dashboards và Alarms. Tôi thực hành theo dõi các chỉ số hệ thống như mức sử dụng CPU, hoạt động mạng và log của ứng dụng để hiểu cách CloudWatch hỗ trợ việc giám sát và xử lý sự cố.


**Kiến thức đã học:** Tôi hiểu rằng Amazon CloudWatch cung cấp khả năng giám sát tập trung, cho phép thu thập log, theo dõi hiệu năng hệ thống và tạo cảnh báo khi phát hiện các trạng thái bất thường.

**Kết quả đạt được:** Tôi nắm được cấu trúc cơ bản của một project SAM và vai trò của `template.yaml`.

**Khó khăn và bài học:** Cần đặt tên resource, parameter và output nhất quán ngay từ đầu.

## Ngày 2 - 29/5: AWS CloudTrail và theo dõi hoạt động hệ thống


**Công việc đã thực hiện:** Tôi tìm hiểu AWS CloudTrail và cách dịch vụ này ghi nhận các hoạt động API cũng như hành động của người dùng trong tài khoản AWS. Trong quá trình học, tôi nghiên cứu Event History, Management Events, các thay đổi trên tài nguyên và cách CloudTrail hỗ trợ xác định người thực hiện thao tác trên các tài nguyên AWS.

**Kiến thức đã học:** Tôi hiểu rằng CloudTrail đóng vai trò quan trọng trong việc kiểm tra bảo mật, giám sát tuân thủ và điều tra các thay đổi bất thường trong môi trường AWS.

**Kết quả đạt được:** Tôi biết cách đọc một số lỗi cơ bản từ SAM/CloudFormation.

**Khó khăn và bài học:** YAML rất nhạy với indent, nên cần format rõ và không sửa vội trong template lớn.

## Ngày 3 - 30/5: AWS Config và quản lý cấu hình tài nguyên

**Công việc đã thực hiện:** Tôi tìm hiểu AWS Config và cách dịch vụ này liên tục theo dõi cấu hình của các tài nguyên AWS. Trong quá trình thực hành, tôi nghiên cứu Configuration History, các Config Rules và cách AWS Config kiểm tra mức độ tuân thủ của tài nguyên dựa trên các tiêu chuẩn bảo mật và vận hành được thiết lập trước. Tôi cũng tìm hiểu cách AWS Config hỗ trợ quản lý Governance và Compliance trong môi trường Cloud.

**Kiến thức đã học:** Tôi hiểu rằng AWS Config giúp theo dõi sự thay đổi cấu hình của tài nguyên và đảm bảo hệ thống luôn duy trì trạng thái bảo mật, tuân thủ theo yêu cầu.

**Kết quả đạt được:** Tôi hiểu hơn vì sao local code chạy được nhưng Lambda có thể lỗi import nếu build sai.

**Khó khăn và bài học:** Cần kiểm tra dependency và runtime version trước khi deploy.

## Ngày 4 - 2/6: AWS Systems Manager và quản lý tài nguyên
**Công việc đã thực hiện:** Tôi học `sam deploy --guided`, stack name, region, capabilities và CloudFormation outputs. Tôi chú ý cách SAM upload artifact lên S3, tạo/ cập nhật CloudFormation stack và trả output như API endpoint. Tôi cũng ghi lại rằng sau mỗi thay đổi lớn cần biết stack đang ở trạng thái nào để tránh deploy chồng chéo.

**Kiến thức đã học:** Deploy bằng SAM giúp quy trình rõ ràng hơn, nhưng vẫn phải hiểu CloudFormation stack bên dưới.

**Kết quả đạt được:** Tôi nắm được luồng validate, build, deploy và kiểm output.

**Khó khăn và bài học:** Nếu deploy fail, cần đọc event của CloudFormation chứ không chỉ nhìn lỗi cuối cùng trong terminal.

## Ngày - 3/6: AWS X-Ray và tổng kết giám sát hệ thống

**Công việc đã thực hiện:** Tôi tìm hiểu AWS X-Ray và cách công nghệ Distributed Tracing hỗ trợ phân tích hiệu năng ứng dụng cũng như phát hiện lỗi trong các hệ thống phức tạp. Trong quá trình nghiên cứu, tôi tìm hiểu các khái niệm như Trace, Segment, Service Map và cách X-Ray kết hợp với các dịch vụ như Lambda, API Gateway để theo dõi luồng xử lý của request.

**Kiến thức đã học:**Tôi hiểu rằng AWS X-Ray giúp lập trình viên xác định điểm nghẽn hiệu năng và xử lý lỗi bằng cách trực quan hóa quá trình giao tiếp giữa các thành phần trong ứng dụng.

**Kết quả đạt được:** Tôi có một quy trình deploy cá nhân để áp dụng cho giai đoạn IRMS sau này.

**Khó khăn và bài học:** Không nên deploy theo trí nhớ; nên ghi lệnh và điều kiện kiểm tra rõ ràng.
