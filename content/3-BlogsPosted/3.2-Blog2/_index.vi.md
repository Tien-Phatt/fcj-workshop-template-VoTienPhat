---
title: "[Generative AI on AWS] Xây dựng AI AWS Architecture Reviewer – Tự động đánh giá kiến trúc AWS với Amazon Bedrock và Serverless"
date: 2026-07-11
weight: 2
chapter: false
pre: "<b>3.2.</b>"
---


Khi học hoặc làm việc với AWS, chúng ta thường sử dụng các công cụ như **Draw.io** để thiết kế kiến trúc hệ thống trước khi triển khai thực tế. Tuy nhiên, sau khi hoàn thành sơ đồ, một câu hỏi thường gặp là:

> **Liệu kiến trúc này đã tuân thủ AWS Well-Architected Framework hay chưa?**

Thông thường, việc đánh giá sẽ dựa vào kinh nghiệm của Solution Architect hoặc quá trình review thủ công. Điều này khá tốn thời gian, đặc biệt với những sơ đồ có nhiều dịch vụ AWS và mối quan hệ phức tạp.

Từ bài toán đó, nhóm mình xây dựng **AI AWS Architecture Reviewer** – một hệ thống ứng dụng Generative AI trên AWS có khả năng phân tích sơ đồ kiến trúc, nhận diện các dịch vụ AWS, đánh giá theo Well-Architected Framework, ước tính chi phí triển khai và tạo báo cáo tự động dưới dạng PDF.

Trong bài viết này, mình sẽ chia sẻ cách hệ thống được thiết kế, các dịch vụ AWS được sử dụng và lý do lựa chọn kiến trúc Serverless kết hợp Event-Driven.

---

## 1. Bài toán

Trong quá trình học tập và phát triển ứng dụng trên AWS, việc thiết kế sơ đồ kiến trúc là bước gần như không thể thiếu. Tuy nhiên, việc đánh giá chất lượng của kiến trúc lại chưa hề đơn giản.

Một số khó khăn thường gặp gồm:

- Kiến trúc có thể chưa tuân thủ AWS Best Practices.
- Khó phát hiện các điểm nghẽn về hiệu năng hoặc bảo mật.
- Việc review phụ thuộc nhiều vào kinh nghiệm của người thiết kế.
- Ước tính chi phí triển khai thường được thực hiện thủ công.

Ý tưởng của nhóm là xây dựng một hệ thống AI có thể hỗ trợ tự động hóa quá trình này, giúp người dùng nhận được phản hồi nhanh hơn và có thêm cơ sở để cải thiện kiến trúc trước khi triển khai.

---

## 2. Kiến trúc tổng thể

![AI AWS Architecture Reviewer Architecture](/fcj-workshop-template-VoTienPhat/images/3-BlogsPosted/Blog2.jpg)

Hệ thống được xây dựng theo mô hình **Serverless** kết hợp **Event-Driven Architecture**, tận dụng các dịch vụ được quản lý hoàn toàn trên AWS.

Kiến trúc gồm các nhóm thành phần chính:

- **Presentation Layer:** CloudFront và giao diện React giúp người dùng tải lên sơ đồ kiến trúc.
- **Processing Layer:** API Gateway, Lambda, EventBridge và Step Functions xử lý toàn bộ workflow.
- **AI Layer:** Amazon Bedrock phân tích kiến trúc và sinh nội dung đánh giá.
- **Storage Layer:** Amazon S3 lưu trữ sơ đồ và báo cáo, DynamoDB lưu lịch sử đánh giá.
- **Monitoring & Security:** CloudWatch theo dõi hệ thống, IAM kiểm soát quyền truy cập.

Việc chia hệ thống thành nhiều lớp giúp kiến trúc rõ ràng hơn, dễ mở rộng và thuận tiện trong quá trình bảo trì.

---

## 3. Luồng hoạt động của hệ thống

Quy trình xử lý diễn ra theo các bước sau:

1. Người dùng truy cập ứng dụng và tải lên sơ đồ kiến trúc AWS.
2. CloudFront phân phối giao diện React, trong khi API Gateway tiếp nhận yêu cầu từ người dùng.
3. Lambda Upload Service lưu sơ đồ vào Amazon S3 và phát sinh một sự kiện mới.
4. Amazon EventBridge nhận sự kiện và kích hoạt AWS Step Functions để bắt đầu workflow xử lý.
5. Các Lambda trong Step Functions lần lượt thực hiện:
   - Trích xuất thông tin từ sơ đồ.
   - Xây dựng mô hình kiến trúc.
   - Gửi dữ liệu đến Amazon Bedrock để phân tích.
6. Amazon Bedrock đánh giá kiến trúc dựa trên AWS Well-Architected Framework và tạo ra các khuyến nghị cải thiện.
7. Hệ thống tiếp tục ước tính chi phí triển khai, sinh báo cáo PDF và lưu kết quả vào Amazon S3.
8. Cuối cùng, DynamoDB lưu lịch sử đánh giá, còn Amazon SNS gửi thông báo khi quá trình hoàn tất.

Toàn bộ workflow được tự động hóa, giúp giảm đáng kể thao tác thủ công của người dùng.

---

## 4. Các dịch vụ AWS nổi bật

### Amazon Bedrock

Amazon Bedrock đóng vai trò là thành phần AI cốt lõi của hệ thống. Dịch vụ này phân tích sơ đồ kiến trúc, đánh giá theo AWS Well-Architected Framework và đưa ra các khuyến nghị giúp cải thiện tính bảo mật, hiệu năng, khả năng mở rộng và tối ưu chi phí.

### AWS Step Functions

Step Functions điều phối toàn bộ workflow xử lý. Thay vì để các Lambda gọi trực tiếp lẫn nhau, toàn bộ quy trình được mô tả dưới dạng State Machine, giúp việc theo dõi, retry và xử lý lỗi trở nên đơn giản hơn.

### Amazon EventBridge

EventBridge đóng vai trò tiếp nhận và định tuyến sự kiện sau khi người dùng tải sơ đồ lên hệ thống. Điều này giúp các thành phần trong kiến trúc giảm sự phụ thuộc trực tiếp vào nhau, đúng với mô hình Event-Driven mà AWS khuyến nghị.

### AWS Lambda

Mỗi Lambda chỉ đảm nhận một chức năng riêng như upload file, trích xuất dữ liệu, phân tích AI hoặc tạo báo cáo. Cách thiết kế này giúp hệ thống dễ mở rộng và bảo trì hơn.

### Amazon S3

Amazon S3 lưu trữ sơ đồ kiến trúc, báo cáo PDF và các tệp đầu ra của hệ thống với khả năng mở rộng gần như không giới hạn.

### Amazon DynamoDB

Amazon DynamoDB lưu lịch sử đánh giá, cho phép người dùng xem lại các lần phân tích trước đó mà không cần thực hiện lại toàn bộ quy trình.

### Amazon CloudWatch

Amazon CloudWatch thu thập log và metrics của toàn bộ hệ thống, giúp nhóm phát triển dễ dàng theo dõi trạng thái workflow và xử lý sự cố khi cần thiết.

---

## 5. Vì sao nhóm lựa chọn Serverless?

Một trong những quyết định quan trọng của nhóm là lựa chọn kiến trúc **Serverless** thay vì triển khai trên máy chủ truyền thống.

Với Serverless, nhóm không cần quản lý hạ tầng, hệ thống có thể tự động mở rộng theo số lượng yêu cầu và chỉ trả chi phí cho thời gian thực thi thực tế.

Bên cạnh đó, việc kết hợp EventBridge và Step Functions giúp workflow trở nên linh hoạt hơn. Nếu sau này cần bổ sung thêm một bước xử lý, nhóm chỉ cần cập nhật State Machine mà không phải thay đổi toàn bộ kiến trúc.

---

## 6. Giá trị của giải pháp

Hệ thống mang lại một số lợi ích đáng chú ý:

- Tự động hóa quá trình đánh giá kiến trúc AWS.
- Hỗ trợ phát hiện các điểm cần cải thiện theo Well-Architected Framework.
- Tạo báo cáo PDF và lưu lịch sử đánh giá.
- Ước tính chi phí triển khai ngay trong quá trình phân tích.
- Kiến trúc Serverless giúp hệ thống dễ mở rộng và tối ưu chi phí vận hành.

Mặc dù đây là một đồ án học tập, nhưng mô hình này hoàn toàn có thể tiếp tục phát triển thành một công cụ hỗ trợ các nhóm phát triển hoặc kỹ sư Cloud trong thực tế.

---

## Góc nhìn cá nhân

Điều mình thấy thú vị nhất ở dự án này không chỉ là việc ứng dụng Amazon Bedrock để phân tích kiến trúc, mà là cách kết hợp nhiều dịch vụ AWS theo mô hình Event-Driven.

Thay vì để các Lambda gọi trực tiếp lẫn nhau, nhóm sử dụng EventBridge để xử lý sự kiện và Step Functions để điều phối workflow. Điều này giúp hệ thống rõ ràng hơn, giảm sự phụ thuộc giữa các thành phần và thuận tiện khi mở rộng trong tương lai.

Theo mình, đây cũng là một ví dụ khá điển hình về cách kết hợp Generative AI với kiến trúc Serverless nhằm giải quyết một bài toán thực tế trên nền tảng AWS.

---

## Kết luận

AI AWS Architecture Reviewer là một ví dụ cho thấy Generative AI không chỉ hỗ trợ tạo nội dung mà còn có thể tham gia vào quá trình phân tích và đánh giá kiến trúc hệ thống.

Việc kết hợp Amazon Bedrock cùng các dịch vụ Serverless như AWS Lambda, Amazon EventBridge và AWS Step Functions giúp xây dựng một quy trình xử lý tự động, linh hoạt và dễ mở rộng.

Hy vọng bài viết sẽ mang đến cho anh/chị và các bạn một góc nhìn khác về cách ứng dụng Generative AI trong lĩnh vực Cloud Architecture. Nếu mọi người có ý tưởng hoặc kinh nghiệm tương tự, rất mong được cùng trao đổi và học hỏi thêm.

---

## Nguồn tham khảo

- AWS Well-Architected Framework  
  https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html

- Amazon Bedrock Documentation  
  https://docs.aws.amazon.com/bedrock/

- AWS Step Functions Developer Guide  
  https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html

- Amazon EventBridge User Guide  
  https://docs.aws.amazon.com/eventbridge/latest/userguide/what-is-amazon-eventbridge.html

- AWS Lambda Documentation  
  https://docs.aws.amazon.com/lambda/

- Amazon S3 Documentation  
  https://docs.aws.amazon.com/s3/

- Amazon DynamoDB Documentation  
  https://docs.aws.amazon.com/dynamodb/

- Amazon CloudWatch Documentation  
  https://docs.aws.amazon.com/cloudwatch/