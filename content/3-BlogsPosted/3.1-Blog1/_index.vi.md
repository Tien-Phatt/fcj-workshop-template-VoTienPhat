---
title: "[Cloud Architecture] Xây dựng kiến trúc Microservices với Amazon SQS, Amazon SNS và AWS Lambda"
date: 2026-07-11
weight: 1
chapter: false
pre: "<b>3.1.</b>"
---

Trong quá trình phát triển các hệ thống hiện đại, **Microservices** đang dần trở thành một trong những kiến trúc được nhiều doanh nghiệp lựa chọn nhờ khả năng mở rộng, triển khai độc lập và dễ bảo trì.

Tuy nhiên, khi số lượng dịch vụ tăng lên, việc để các service gọi trực tiếp lẫn nhau lại tạo ra nhiều vấn đề như phụ thuộc chặt chẽ, khó mở rộng và khó xử lý khi xảy ra lỗi.

AWS cung cấp nhiều dịch vụ giúp giải quyết bài toán này, trong đó **Amazon SQS (Simple Queue Service)**, **Amazon SNS (Simple Notification Service)** và **AWS Lambda** là bộ ba thường được sử dụng để xây dựng các hệ thống giao tiếp bất đồng bộ (*Asynchronous Communication*).

Trong bài viết này, chúng ta sẽ cùng tìm hiểu cách kết hợp ba dịch vụ này để xây dựng một kiến trúc Microservices linh hoạt, dễ mở rộng và có khả năng chịu lỗi tốt hơn.

---

## 1. Bài toán

Giả sử chúng ta đang xây dựng một hệ thống thương mại điện tử.

Sau khi khách hàng nhấn nút **Đặt hàng**, hệ thống cần thực hiện nhiều công việc khác nhau:

- Lưu đơn hàng.
- Thanh toán.
- Cập nhật kho hàng.
- Gửi email xác nhận.
- Gửi thông báo đến ứng dụng.
- Cập nhật hệ thống phân tích dữ liệu.

Nếu tất cả các bước trên đều được thực hiện đồng thời trong một request, người dùng sẽ phải chờ rất lâu để nhận phản hồi.

Ngoài ra, nếu chỉ một dịch vụ gặp lỗi, toàn bộ quy trình có thể bị ảnh hưởng.

Đây là vấn đề mà rất nhiều hệ thống Microservices gặp phải khi các thành phần phụ thuộc trực tiếp vào nhau.

---

## 2. Giải pháp với Amazon SQS và Amazon SNS

Thay vì để các dịch vụ gọi trực tiếp lẫn nhau, chúng ta có thể sử dụng kiến trúc giao tiếp bất đồng bộ.

Trong mô hình này:

- Service xử lý đơn hàng chỉ cần ghi nhận yêu cầu và gửi một message vào hàng đợi Amazon SQS.
- Các dịch vụ phía sau sẽ lần lượt lấy message từ hàng đợi để xử lý.
- Khi hoàn thành, Amazon SNS sẽ phát thông báo đến các hệ thống cần nhận thông tin.

Cách tiếp cận này giúp các thành phần hoạt động độc lập và giảm đáng kể sự phụ thuộc giữa các dịch vụ.

---

## 3. Kiến trúc tổng thể


![Kiến trúc Microservices](/fcj-workshop-template-VoTienPhat/images/3-BlogsPosted/Blog1.jpg)

Hệ thống được xây dựng với các thành phần chính:

- Amazon API Gateway tiếp nhận yêu cầu từ người dùng.
- AWS Lambda xử lý nghiệp vụ ban đầu và ghi message vào Amazon SQS.
- Amazon SQS đóng vai trò Message Queue, lưu trữ các yêu cầu cần xử lý.
- AWS Lambda Consumers đọc message từ SQS để thực hiện từng tác vụ.
- Amazon SNS phát thông báo đến nhiều dịch vụ khác nhau.
- Amazon DynamoDB lưu trữ dữ liệu đơn hàng.
- Amazon CloudWatch giám sát toàn bộ hệ thống.

Kiến trúc này giúp hệ thống hoạt động theo mô hình **Event-Driven** và có thể mở rộng dễ dàng khi lượng người dùng tăng lên.

---

## 4. Luồng hoạt động

Quy trình xử lý diễn ra như sau:

1. Người dùng gửi yêu cầu đặt hàng thông qua API Gateway.
2. AWS Lambda tiếp nhận yêu cầu, kiểm tra dữ liệu và lưu thông tin đơn hàng ban đầu.
3. Lambda gửi message vào Amazon SQS.
4. Amazon SQS lưu trữ message cho đến khi một Lambda Consumer sẵn sàng xử lý.
5. Lambda Consumer thực hiện:
   - Xử lý thanh toán.
   - Cập nhật tồn kho.
   - Lưu dữ liệu vào DynamoDB.
6. Sau khi hoàn tất, Lambda phát sự kiện đến Amazon SNS.
7. Amazon SNS gửi thông báo đồng thời đến:
   - Email xác nhận.
   - Ứng dụng di động.
   - Hệ thống Analytics.
   - Dashboard giám sát.

Toàn bộ quy trình được thực hiện bất đồng bộ, giúp giảm thời gian phản hồi cho người dùng và tăng khả năng mở rộng của hệ thống.

---

## 5. Vai trò của từng dịch vụ AWS

### Amazon SQS

Amazon SQS là dịch vụ Message Queue được quản lý hoàn toàn bởi AWS.

Nó giúp lưu trữ các yêu cầu xử lý theo hàng đợi, đảm bảo message không bị mất ngay cả khi dịch vụ phía sau tạm thời gặp sự cố.

Ngoài ra, SQS còn hỗ trợ **Dead Letter Queue (DLQ)**, giúp lưu lại các message xử lý thất bại để kiểm tra sau này.

### Amazon SNS

Amazon SNS hoạt động theo mô hình **Publish/Subscribe**.

Chỉ với một lần Publish, hệ thống có thể gửi thông báo đồng thời đến nhiều Subscriber khác nhau như Email, SMS, Lambda hoặc HTTP Endpoint.

Điều này giúp việc mở rộng hệ thống trở nên đơn giản hơn mà không cần thay đổi logic của ứng dụng.

### AWS Lambda

Lambda xử lý từng nghiệp vụ độc lập mà không cần quản lý máy chủ.

Mỗi Lambda chỉ tập trung vào một chức năng cụ thể, giúp mã nguồn dễ bảo trì và tái sử dụng.

### Amazon DynamoDB

DynamoDB lưu trữ dữ liệu đơn hàng với khả năng mở rộng tự động và hiệu năng cao.

### Amazon CloudWatch

CloudWatch thu thập log, metrics và cảnh báo, giúp đội ngũ vận hành dễ dàng theo dõi trạng thái của toàn bộ hệ thống.

---

## 6. Lợi ích của kiến trúc

Việc kết hợp Amazon SQS, Amazon SNS và AWS Lambda mang lại nhiều lợi ích:

- Giảm sự phụ thuộc giữa các Microservices.
- Tăng khả năng mở rộng của hệ thống.
- Xử lý bất đồng bộ giúp cải thiện thời gian phản hồi.
- Hỗ trợ Retry và Dead Letter Queue khi xảy ra lỗi.
- Dễ dàng bổ sung thêm các dịch vụ mới mà không cần thay đổi kiến trúc hiện tại.

Đây cũng là một trong những mô hình được AWS khuyến nghị khi xây dựng các ứng dụng Cloud-Native và Serverless.

---

## Góc nhìn cá nhân

Điều mình thấy thú vị ở kiến trúc này là cách AWS tách biệt rõ ràng giữa việc truyền tải dữ liệu và xử lý nghiệp vụ.

Amazon SQS đảm nhiệm vai trò lưu trữ và phân phối message một cách an toàn, trong khi Amazon SNS giúp phát thông báo đến nhiều hệ thống cùng lúc.

AWS Lambda chỉ tập trung xử lý đúng phần logic nghiệp vụ mà nó được giao.

Cách tiếp cận này không chỉ giúp hệ thống hoạt động ổn định hơn mà còn tạo điều kiện để mở rộng từng thành phần một cách độc lập khi lưu lượng truy cập tăng lên.

---

## Kết luận

Microservices không chỉ là việc chia nhỏ ứng dụng thành nhiều dịch vụ, mà còn là cách các dịch vụ giao tiếp với nhau một cách hiệu quả.

Sự kết hợp giữa Amazon SQS, Amazon SNS và AWS Lambda giúp xây dựng một kiến trúc bất đồng bộ có khả năng mở rộng cao, giảm sự phụ thuộc giữa các thành phần và tăng khả năng chịu lỗi của hệ thống.

Đối với các ứng dụng thương mại điện tử, tài chính, IoT hoặc xử lý dữ liệu theo thời gian thực, đây là một mô hình rất đáng cân nhắc khi triển khai trên AWS.

Hy vọng bài viết sẽ giúp anh/chị và các bạn có thêm một góc nhìn về cách AWS xây dựng các hệ thống Microservices hiện đại.

---

## Nguồn tham khảo

- Amazon SQS Documentation: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html
- Amazon SNS Documentation: https://docs.aws.amazon.com/sns/
- AWS Lambda Documentation: https://docs.aws.amazon.com/lambda/
- AWS Well-Architected Framework: https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html
- AWS Serverless Lens: https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/welcome.html