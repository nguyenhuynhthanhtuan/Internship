---
title: "Shift-left Security với Amazon Bedrock"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

## 3.1 Shift-left Security với Amazon Bedrock: Tự động hóa Threat Modeling bằng Generative AI

Trong phát triển phần mềm và kiến trúc cloud, **Shift-left Security** là một hướng tiếp cận quan trọng của DevSecOps. Thay vì chỉ phát hiện lỗ hổng sau khi hệ thống đã triển khai, cách tiếp cận này đưa các hoạt động bảo mật vào ngay từ giai đoạn thiết kế. Một kỹ thuật cốt lõi để thực hiện điều đó là **Threat Modeling**.

Tuy nhiên, Threat Modeling theo cách truyền thống thường cần nhiều buổi họp, chuyên gia bảo mật và quá trình phân tích thủ công kéo dài từ vài ngày đến cả tuần. Điều này gây khó khăn khi áp dụng ở quy mô lớn, đặc biệt trong các môi trường phát triển hiện đại có tốc độ triển khai nhanh.

Để giải quyết bài toán này, AWS giới thiệu kiến trúc tham chiếu **Threat Designer**, kết hợp **Generative AI** và **Amazon Bedrock** để tự động hóa quá trình xây dựng Threat Model.

![Shift-left Security và Security Debt](/Internship/images/3-BlogsTranslated/blog-diagram-01.jpeg)

### 3.1.1 Giải pháp

Threat Designer là một ứng dụng web hỗ trợ tự động hóa Threat Modeling bằng các **Foundation Models (FM)** trên Amazon Bedrock.

Thay vì yêu cầu kỹ sư bảo mật tự phân tích sơ đồ kiến trúc và đối chiếu với các framework bảo mật, hệ thống sử dụng khả năng **Multimodal AI** để đọc hiểu nhiều loại đầu vào cùng lúc:

- Sơ đồ kiến trúc hệ thống
- Mô tả giải pháp
- Các giả định triển khai

Từ các thông tin đó, hệ thống xây dựng **Threat Catalog** gồm các mối đe dọa, kịch bản tấn công và biện pháp giảm thiểu tương ứng.

![Kiến trúc Threat Designer trên AWS](/Internship/images/3-BlogsTranslated/blog-diagram-02.jpeg)

Khác với các công cụ Threat Modeling truyền thống thường dựa trên rule hoặc template cố định, Foundation Models có khả năng suy luận theo ngữ cảnh, hiểu mối quan hệ giữa các thành phần trong hệ thống và phát hiện cả những kịch bản tấn công chưa được định nghĩa sẵn. Toàn bộ giải pháp được xây dựng trên kiến trúc serverless, giúp giảm chi phí vận hành, tự động mở rộng và hạn chế việc quản lý hạ tầng.

### 3.1.2 Quy trình hoạt động

Kiến trúc của Threat Designer được xây dựng dựa trên sự phối hợp của nhiều dịch vụ AWS:

1. **Tiếp nhận yêu cầu:** Người dùng truy cập ứng dụng web triển khai trên AWS Amplify và đăng nhập thông qua Amazon Cognito. Sau đó, người dùng tải lên sơ đồ kiến trúc, mô tả giải pháp và các giả định cần thiết. Các file này được lưu trữ an toàn trên Amazon S3.
2. **Kích hoạt xử lý:** Yêu cầu từ giao diện web được chuyển tới Amazon API Gateway, sau đó kích hoạt AWS Lambda backend thông qua proxy integration. Lambda tiếp nhận request, lưu thông tin ban đầu và khởi chạy agent xử lý bất đồng bộ.
3. **Phân tích bằng Agent AI:** Agent được triển khai trên AWS Lambda và được điều phối bởi LangGraph, cho phép chia quá trình Threat Modeling thành nhiều bước nhỏ như xử lý hình ảnh, nhận diện tài sản, phân tích luồng dữ liệu và liệt kê mối đe dọa.
4. **Cơ chế Agentic Workflow:** Sau mỗi lần phân tích, agent đánh giá chất lượng kết quả bằng cơ chế gap analysis. Hệ thống hỗ trợ manual mode, trong đó người dùng chỉ định số vòng lặp, và auto mode, trong đó agent tự phát hiện phần còn thiếu rồi tiếp tục hoàn thiện Threat Catalog.

Ở từng bước, agent sử dụng Foundation Model trên Amazon Bedrock để suy luận dựa trên hình ảnh và mô tả hệ thống. Trong quá trình phân tích, AI có thể tham khảo các framework bảo mật như **MITRE ATT&CK** và **OWASP** để xây dựng Threat Catalog đầy đủ hơn.

Trạng thái thực thi được lưu trong **Amazon DynamoDB**, bao gồm agent execution state và Threat Catalog. Nhờ vậy, agent có thể hoạt động bất đồng bộ và tiếp tục xử lý ngay cả khi quá trình phân tích kéo dài nhiều phút.

### 3.1.3 Góc nhìn cá nhân

Sau khi đọc bài viết **Accelerate Threat Modeling with Generative AI** trên AWS Machine Learning Blog, tôi thấy điểm đáng chú ý nhất của Threat Designer không chỉ nằm ở việc sử dụng một mô hình AI mạnh, mà còn ở cách AWS thiết kế **Agentic Workflow** để xử lý một bài toán vốn phụ thuộc nhiều vào kinh nghiệm của chuyên gia bảo mật.

Trước đây, tôi nghĩ Threat Modeling là công việc rất khó tự động hóa vì AI phải hiểu kiến trúc hệ thống, luồng dữ liệu, ranh giới tin cậy và bối cảnh triển khai của từng ứng dụng. Tuy nhiên, với khả năng phân tích đa phương thức của Foundation Models trên Amazon Bedrock và cơ chế tự đánh giá thông qua LangGraph, AI không chỉ trả lời câu hỏi mà còn tham gia vào quá trình phân tích, kiểm tra và hoàn thiện kết quả qua nhiều vòng lặp.

Đây cũng là ví dụ rõ ràng cho xu hướng **Agentic AI** trong an toàn thông tin. Thay vì tạo ra một kết quả duy nhất, AI có thể tự kiểm tra, phát hiện phần còn thiếu và tiếp tục cải thiện kết quả trước khi trả về cho người dùng.

Theo tôi, Generative AI sẽ không thay thế kỹ sư bảo mật trong tương lai gần, nhưng có thể trở thành công cụ hỗ trợ rất hiệu quả. Những công việc tốn thời gian như Threat Modeling, phân tích tài liệu hoặc rà soát kiến trúc có thể được AI xử lý trước, giúp chuyên gia tập trung nhiều hơn vào đánh giá và ra quyết định bảo mật.

### 3.1.4 Kết luận

Threat Designer không nhằm thay thế chuyên gia an toàn thông tin mà đóng vai trò như một công cụ hỗ trợ Threat Modeling, giúp tăng tốc quá trình phân tích và chuẩn hóa kết quả đánh giá. Bằng cách kết hợp kiến trúc serverless của AWS với Amazon Bedrock và Agentic Workflow, quá trình xây dựng Threat Model có thể được rút ngắn từ nhiều ngày xuống khoảng 5-15 phút, tùy độ phức tạp của hệ thống.

Đây là một hướng tiếp cận đáng chú ý đối với các đội ngũ DevSecOps, đặc biệt khi Generative AI ngày càng được ứng dụng sâu hơn vào quy trình phát triển phần mềm và an toàn thông tin.

**Nguồn tham khảo:** [Accelerate Threat Modeling with Generative AI](https://aws.amazon.com/blogs/machine-learning/accelerate-threat-modeling-with-generative-ai/)
