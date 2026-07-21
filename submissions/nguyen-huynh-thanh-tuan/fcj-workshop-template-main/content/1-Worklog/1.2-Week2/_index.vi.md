---
title: "Tuần 2"
date: 2026-04-23
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

**Mốc thời gian:** 23/4 → 29/4 (5 ngày làm việc)

## Ngày 1 - 23/4: Tìm hiểu Amazon EC2

**Công việc đã thực hiện:** Tôi bắt đầu các bài thực hành AWS bằng việc tìm hiểu Amazon EC2 và những thành phần cốt lõi của dịch vụ này. Tôi nghiên cứu các loại EC2 Instance, Amazon Machine Image (AMI), vòng đời của EC2 Instance và các tùy chọn tính phí. Trong quá trình thực hành, tôi khởi tạo EC2 Instance đầu tiên, cấu hình các thiết lập cơ bản và làm quen với giao diện AWS Management Console để quản lý máy chủ trên nền tảng đám mây.

**Kiến thức đã học:** Tôi hiểu rằng Amazon EC2 cung cấp các máy chủ ảo có khả năng mở rộng trên AWS và việc lựa chọn loại EC2 Instance phù hợp cần dựa trên yêu cầu về hiệu năng, khối lượng công việc và chi phí.

**Kết quả đạt được:** Tôi triển khai thành công một EC2 Instance và nắm được quy trình cơ bản để tạo, quản lý và xóa máy chủ trên AWS.

**Khó khăn và bài học:** Quá trình khởi tạo EC2 có nhiều tùy chọn cấu hình nên tôi nhận thấy cần kiểm tra cẩn thận từng bước trước khi triển khai để tránh các sai sót không cần thiết.

## Ngày 2 - 24/4: Amazon EBS và kết nối EC2

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon Elastic Block Store (EBS) và cách các ổ đĩa lưu trữ được gắn vào EC2 Instance. Tôi thực hành tạo và gắn EBS Volume, tìm hiểu các loại Volume cũng như mối quan hệ giữa EC2 và bộ nhớ lưu trữ lâu dài. Bên cạnh đó, tôi thực hành kết nối đến máy chủ Linux thông qua SSH và máy chủ Windows thông qua Remote Desktop Protocol (RDP).


**Kiến thức đã học:** Tôi hiểu rằng Amazon EBS cung cấp bộ nhớ dạng Block Storage có tính lưu trữ lâu dài, độc lập với vòng đời của EC2 Instance, phù hợp để lưu hệ điều hành, cơ sở dữ liệu và dữ liệu ứng dụng.


**Kết quả đạt được:** Tôi hiểu được cách EC2 hoạt động cùng với EBS và thực hiện thành công việc kết nối đến cả máy chủ Linux và Windows trên AWS.

**Khó khăn và bài học:** Việc kết nối đến máy chủ yêu cầu cấu hình chính xác Key Pair, Security Group và các thiết lập mạng. Chỉ cần thiếu một trong các thành phần này thì sẽ không thể truy cập vào máy chủ.

## Ngày 3 - 27/4: Quản lý IAM User và IAM Role

**Công việc đã thực hiện:** Tôi thực hành tạo IAM User, phân quyền thông qua IAM Group và IAM Policy, đồng thời tạo IAM Role để cấp quyền cho các dịch vụ AWS. Trong quá trình thực hành, tôi so sánh sự khác nhau giữa IAM User và IAM Role, đồng thời tìm hiểu nguyên tắc phân quyền tối thiểu (Least Privilege) khi cấp quyền truy cập.

**Kiến thức đã học:** Tôi hiểu rằng IAM là nền tảng quản lý danh tính và quyền truy cập của AWS, đồng thời việc cấp quyền cần dựa trên đúng nhu cầu sử dụng thay vì cấp quá nhiều quyền.


**Kết quả đạt được:** Tôi tạo thành công IAM User và IAM Role, đồng thời hiểu rõ hơn cách các dịch vụ AWS sử dụng IAM Role để truy cập tài nguyên một cách an toàn.


**Khó khăn và bài học:** Việc quản lý quyền truy cập có thể trở nên phức tạp nếu cấu hình Policy không chính xác. Tôi rút ra bài học là chỉ nên cấp quyền tối thiểu cần thiết và chỉ mở rộng quyền khi thực sự cần.


## Ngày 4 - 28/4: Security Group và Amazon VPC

**Công việc đã thực hiện:** Tôi tìm hiểu các kiến thức cơ bản về Amazon VPC, bao gồm Subnet, Route Table, Internet Gateway và Security Group. Tôi thực hành tạo một môi trường VPC cơ bản, cấu hình các quy tắc Inbound và Outbound cho EC2 Instance, đồng thời tìm hiểu cách Security Group bảo vệ tài nguyên trên AWS bằng cách kiểm soát lưu lượng mạng ra vào.


**Kiến thức đã học:** Tôi hiểu rằng Security Group hoạt động như một tường lửa ảo dành cho EC2 Instance, trong khi Amazon VPC cung cấp một môi trường mạng riêng biệt và an toàn để triển khai các tài nguyên trên nền tảng AWS.


**Kết quả đạt được:** Tôi cấu hình thành công môi trường VPC cơ bản và hiểu được cách các thiết lập về mạng và bảo mật phối hợp với nhau để kiểm soát quyền truy cập đến các tài nguyên AWS.


**Khó khăn và bài học:** Các cấu hình sai về mạng hoặc Security Group có thể khiến EC2 Instance không thể truy cập được mặc dù máy chủ vẫn đang hoạt động. Tôi học được cách kiểm tra lần lượt Route Table, Subnet và các quy tắc của Security Group khi xử lý sự cố.

## Ngày 5 - 29/4: Tổng kết các bài thực hành trong tuần

**Công việc đã thực hiện:** Tôi tổng hợp lại toàn bộ các bài Lab đã thực hiện trong tuần, bao gồm Amazon EC2, Amazon EBS, IAM User, IAM Role, Security Group, Key Pair và Amazon VPC. Tôi ghi lại quy trình triển khai, các bước kết nối máy chủ cũng như những cấu hình bảo mật đã thực hiện trong quá trình học. Đồng thời, tôi cũng tổng hợp các lỗi cấu hình thường gặp và cách khắc phục để làm tài liệu tham khảo cho các bài thực hành sau.

**Kiến thức đã học:** Tôi nhận thấy rằng việc triển khai hạ tầng trên AWS không chỉ đơn thuần là tạo tài nguyên mà còn phải cấu hình đầy đủ về quản lý danh tính, mạng, lưu trữ và bảo mật để hệ thống hoạt động ổn định.

**Kết quả đạt được:** Tôi củng cố được kiến thức về các dịch vụ hạ tầng cốt lõi của AWS và hoàn thiện bộ ghi chú phục vụ cho các bài Lab nâng cao trong những tuần tiếp theo.

**Khó khăn và bài học:** Việc tổng hợp lại kiến thức ngay sau mỗi bài Lab giúp tôi ghi nhớ các khái niệm tốt hơn và dễ dàng phát hiện những lỗi đã gặp trong quá trình thực hành. Tôi nhận thấy việc ghi chép tài liệu đầy đủ là rất cần thiết khi làm việc với nhiều dịch vụ AWS.