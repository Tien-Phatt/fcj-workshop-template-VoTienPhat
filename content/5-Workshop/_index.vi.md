---
title: "Workshop"
date: 2026-07-03
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# AI AWS Architecture Reviewer

#### Tổng quan

**AI AWS Architecture Reviewer** là một nền tảng web serverless cho phép người dùng upload sơ đồ kiến trúc AWS và nhận kết quả đánh giá kiến trúc có hỗ trợ bởi AI dựa trên **AWS Well-Architected Framework**.

Trong workshop này, hệ thống được triển khai theo từng giai đoạn, bắt đầu từ phát triển frontend, triển khai website React lên Amazon S3, phân phối website bằng Amazon CloudFront và bảo vệ frontend bằng AWS WAF. Sau đó, hệ thống tiếp tục được xây dựng với backend upload bằng Amazon API Gateway và AWS Lambda, lưu trữ sơ đồ kiến trúc bằng Amazon S3, lưu metadata và trạng thái review bằng Amazon DynamoDB, rồi mở rộng sang workflow xử lý tự động bằng Amazon EventBridge và AWS Step Functions.

Ở giai đoạn xử lý AI, hệ thống sử dụng AWS Lambda AI Analyze để đọc sơ đồ đã upload từ Amazon S3 Input Bucket. Lambda AI Analyze gửi diagram sang Amazon Bedrock để Bedrock nhận diện các AWS services, connections, text notes và sinh ra architecture JSON có cấu trúc. Sau đó, architecture JSON được gửi sang AWS Lambda Cost Tool để ước tính chi phí hàng tháng. Kết quả ước tính chi phí cùng với architecture JSON tiếp tục được gửi lại sang Amazon Bedrock để đánh giá tổng thể kiến trúc theo AWS Well-Architected Framework, giải thích chi phí và đề xuất tối ưu.

Sau khi hoàn tất phân tích, AWS Lambda PDF Generator tạo báo cáo PDF từ kết quả đánh giá cuối cùng, lưu báo cáo vào Amazon S3 Report Bucket và cập nhật review history trong Amazon DynamoDB. Amazon CloudWatch được sử dụng để ghi logs, theo dõi metrics và tạo alarms cho các thành phần quan trọng như Lambda, API Gateway, Step Functions, EventBridge và DynamoDB. Khi có lỗi quan trọng ảnh hưởng đến hệ thống, CloudWatch Alarm sẽ gửi cảnh báo đến Amazon SNS Topic. Amazon SNS sau đó phân phối email cảnh báo đến các địa chỉ đã đăng ký và xác nhận subscription. AWS IAM được sử dụng để kiểm soát quyền truy cập giữa các dịch vụ theo nguyên tắc least privilege access. AWS WAF được gắn với CloudFront distribution để bảo vệ frontend khỏi các request có nguy cơ độc hại.

Dự án sử dụng các dịch vụ AWS chính gồm: **Amazon S3, Amazon CloudFront, AWS WAF, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon EventBridge, AWS Step Functions, Amazon Bedrock, Amazon SNS, Amazon CloudWatch và AWS IAM**.

Mục tiêu chính của workshop là ghi lại quá trình triển khai website và backend services, giải thích cách cấu hình từng dịch vụ AWS, đồng thời hoàn thiện workflow đánh giá kiến trúc AWS bằng AI, bao gồm frontend hosting, frontend protection with AWS WAF, diagram analysis, architecture JSON generation, cost estimation, final architecture review, PDF report generation, CloudWatch monitoring, SNS alert notification và security.

---

#### Kiến trúc tổng thể của hệ thống

Kiến trúc của **AI AWS Architecture Reviewer** được thiết kế theo mô hình **serverless event-driven architecture**. Người dùng truy cập ứng dụng web thông qua Amazon CloudFront được bảo vệ bởi AWS WAF. Frontend React được lưu trữ trong Amazon S3 frontend bucket và được phân phối thông qua CloudFront để cải thiện hiệu năng truy cập. AWS WAF được gắn với CloudFront distribution để kiểm tra request, theo dõi các request đáng ngờ và giảm thiểu các request có nguy cơ độc hại trước khi chúng truy cập vào frontend.

Khi người dùng upload sơ đồ kiến trúc AWS, request được gửi từ frontend đến Amazon API Gateway. API Gateway chuyển request đến AWS Lambda Upload Service. Lambda Upload Service kiểm tra file upload, tạo review ID, lưu sơ đồ vào Amazon S3 Input Bucket và ghi metadata ban đầu vào Amazon DynamoDB Review Database.

Sau khi file diagram được lưu vào S3 Input Bucket, Amazon S3 phát sinh Object Created Event. Event này được gửi đến Amazon EventBridge. EventBridge sử dụng rule đã cấu hình để khởi động AWS Step Functions Review Workflow. Step Functions chịu trách nhiệm điều phối toàn bộ quá trình xử lý review, bao gồm extraction, cost estimation, AI review, PDF generation, lưu kết quả và error handling.

Trong Processing Layer, Step Functions gọi AWS Lambda AI Analyze. Lambda AI Analyze đọc uploaded diagram từ S3 Input Bucket, sau đó gửi diagram sang Amazon Bedrock. Ở bước này, Amazon Bedrock đóng vai trò phân tích sơ đồ ban đầu để nhận diện các AWS services, connections, text notes và các thành phần kiến trúc. Kết quả trả về là architecture JSON có cấu trúc.

Sau khi có architecture JSON, Lambda AI Analyze gửi dữ liệu này sang AWS Lambda Cost Tool. Lambda Cost Tool phân tích danh sách AWS services được phát hiện trong architecture JSON và ước tính monthly cost dựa trên các giả định sử dụng ở mức demo. Kết quả cost estimation bao gồm danh sách dịch vụ, giả định sử dụng, chi phí ước tính và các thành phần có khả năng ảnh hưởng lớn đến ngân sách.

Tiếp theo, architecture JSON và cost estimation result được gửi lại sang Amazon Bedrock để thực hiện final AI review. Ở bước này, Amazon Bedrock đánh giá toàn bộ kiến trúc theo AWS Well-Architected Framework, bao gồm các trụ cột Security, Reliability, Performance Efficiency, Cost Optimization, Operational Excellence và Sustainability. Bedrock cũng giải thích chi phí, chỉ ra các điểm rủi ro và đề xuất hướng tối ưu kiến trúc.

Sau khi final AI review hoàn tất, AWS Lambda PDF Generator tạo báo cáo PDF từ kết quả phân tích. Báo cáo được lưu vào Amazon S3 Report Bucket. Lambda PDF Generator cũng cập nhật review history, review status và report information vào Amazon DynamoDB. Amazon SNS không được sử dụng để gửi email khi từng review hoàn tất. Thay vào đó, Amazon SNS được tích hợp với Amazon CloudWatch Alarm để gửi email cảnh báo vận hành khi hệ thống phát sinh lỗi, ví dụ Step Functions failed, Lambda error, API Gateway 5XX, DynamoDB throttling hoặc EventBridge failed invocation.

![AI AWS Architecture Reviewer Workshop](/fcj-workshop-template-VoTienPhat/images/5-Workshop/ai-aws-architecture-reviewer.png)

---

#### Luồng hoạt động chính

Luồng hoạt động của hệ thống gồm các bước sau:

1. User truy cập web application thông qua Amazon CloudFront.
2. AWS WAF được gắn với CloudFront distribution để kiểm tra request và bảo vệ frontend.
3. Amazon CloudFront phân phối React frontend từ Amazon S3 frontend bucket.
4. User submit architecture diagram thông qua frontend.
5. Amazon API Gateway nhận upload request và gửi request đến AWS Lambda Upload Service.
6. AWS Lambda Upload Service validate upload request và lưu uploaded diagram vào Amazon S3 Input Bucket.
7. Amazon S3 publish Object Created Event sau khi diagram được upload thành công.
8. Amazon EventBridge nhận event và khởi động AWS Step Functions Review Workflow.
9. AWS Step Functions điều phối workflow và gọi AWS Lambda AI Analyze để xử lý diagram.
10. AWS Lambda AI Analyze gửi uploaded diagram sang Amazon Bedrock để Bedrock nhận diện AWS services, connections, text notes và sinh architecture JSON.
11. AWS Lambda AI Analyze gửi architecture JSON sang AWS Lambda Cost Tool để ước tính monthly cost.
12. AWS Lambda Cost Tool gửi cost estimation result cùng với architecture JSON sang Amazon Bedrock để đánh giá toàn bộ kiến trúc, giải thích chi phí và đề xuất tối ưu.
13. AWS Lambda PDF Generator tạo PDF report từ kết quả final AI review.
14. AWS Lambda PDF Generator cập nhật review history và review status vào Amazon DynamoDB Review Database.
15. AWS Lambda PDF Generator lưu generated PDF report vào Amazon S3 Report Bucket.
16. Frontend hiển thị kết quả review và cho phép người dùng tải PDF report.
17. Amazon CloudWatch ghi logs, theo dõi metrics và tạo alarms cho các thành phần quan trọng như Lambda, API Gateway, Step Functions, EventBridge và DynamoDB.
18. Khi CloudWatch Alarm phát hiện lỗi quan trọng trong hệ thống, alarm sẽ gửi cảnh báo đến Amazon SNS Topic.
19. Amazon SNS gửi email cảnh báo lỗi đến các địa chỉ email đã đăng ký và xác nhận subscription.

Luồng frontend protection:

```text
User
→ AWS WAF
→ Amazon CloudFront
→ Amazon S3 Frontend Bucket
→ React Application
```

Luồng review chính:

```text
User
→ AWS WAF
→ Amazon CloudFront
→ S3 React Frontend
→ API Gateway
→ Lambda Upload Service
→ S3 Input Bucket
→ EventBridge
→ Step Functions
→ Lambda AI Analyze
→ Amazon Bedrock sinh architecture JSON
→ Lambda Cost Tool ước tính monthly cost
→ Amazon Bedrock đánh giá tổng thể kiến trúc và tối ưu chi phí
→ Lambda PDF Generator
→ S3 Report Bucket
→ DynamoDB
→ Frontend hiển thị kết quả và tải PDF
```

Luồng monitoring và cảnh báo lỗi:

```text
AWS Service Error
→ Amazon CloudWatch Logs / Metrics
→ CloudWatch Alarm
→ Amazon SNS Topic
→ Email Alert
```

---

#### Các dịch vụ AWS sử dụng trong workshop

Các dịch vụ AWS chính được sử dụng trong workshop bao gồm:

- **AWS WAF**: Bảo vệ CloudFront distribution khỏi các request có nguy cơ độc hại. AWS WAF được sử dụng với các managed rule groups như Amazon IP Reputation List, Common Rule Set và Known Bad Inputs Rule Set để theo dõi, phát hiện và giảm thiểu các request đáng ngờ trước khi chúng truy cập vào frontend.
- **Amazon CloudFront**: Phân phối ứng dụng web React đến người dùng với hiệu năng tốt hơn và độ trễ thấp hơn.
- **Amazon S3 Frontend Bucket**: Lưu trữ bản build production của React frontend, bao gồm `index.html` và các static assets.
- **Amazon API Gateway**: Cung cấp các API endpoint cho upload diagram, lấy review history, xem review detail và kiểm tra review status.
- **AWS Lambda Upload Service**: Xử lý upload request, validate file, tạo review ID, lưu diagram vào S3 Input Bucket và ghi metadata vào DynamoDB.
- **Amazon S3 Input Bucket**: Lưu trữ các sơ đồ kiến trúc AWS gốc do người dùng upload.
- **Amazon DynamoDB Review Database**: Lưu metadata, review status, review history, thông tin file upload và đường dẫn báo cáo PDF.
- **Amazon EventBridge**: Nhận S3 Object Created Event và kích hoạt Step Functions Review Workflow.
- **AWS Step Functions**: Điều phối toàn bộ workflow review, bao gồm diagram extraction, cost estimation, final AI review, PDF generation và error handling.
- **AWS Lambda AI Analyze**: Đọc uploaded diagram từ S3 Input Bucket và gửi diagram sang Amazon Bedrock để sinh architecture JSON.
- **Amazon Bedrock**: Được sử dụng trong hai giai đoạn. Giai đoạn đầu dùng để phân tích diagram và sinh architecture JSON. Giai đoạn sau dùng để đánh giá toàn bộ kiến trúc dựa trên architecture JSON và cost estimation result.
- **AWS Lambda Cost Tool**: Nhận architecture JSON, xác định các AWS services xuất hiện trong sơ đồ và ước tính monthly cost.
- **AWS Lambda PDF Generator**: Tạo PDF report từ kết quả final AI review, bao gồm architecture review, cost estimation, cost explanation và optimization recommendations.
- **Amazon S3 Report Bucket**: Lưu trữ các PDF report được tạo ra sau khi review hoàn tất.
- **Amazon SNS**: Nhận cảnh báo từ Amazon CloudWatch Alarm và gửi email thông báo lỗi hệ thống đến các địa chỉ email đã đăng ký và xác nhận subscription. Trong dự án này, SNS không được sử dụng để gửi email khi từng review hoàn tất.
- **Amazon CloudWatch**: Ghi logs, theo dõi metrics, tạo CloudWatch Alarms và hỗ trợ troubleshooting cho Lambda, API Gateway, Step Functions, EventBridge và DynamoDB. Khi alarm phát hiện lỗi quan trọng, CloudWatch gửi cảnh báo đến Amazon SNS Topic.
- **AWS IAM**: Kiểm soát quyền truy cập giữa các dịch vụ theo nguyên tắc least privilege access.

---

#### Trạng thái triển khai hiện tại

Các phần sau đã được triển khai trong dự án:

1. **Phát triển Frontend**
   - Tạo frontend React bằng Vite.
   - Xây dựng giao diện chính của ứng dụng.
   - Xây dựng các trang chính gồm Dashboard, Upload Diagram, Review Progress, Review History, Report Detail và Settings.
   - Chuẩn bị cấu trúc frontend để tích hợp API với backend trên AWS.
   - Xử lý routing bằng React Router.
   - Chuẩn bị giao diện hiển thị review status, review history và review detail.

2. **Triển khai Frontend bằng Amazon S3, Amazon CloudFront và AWS WAF**
   - Tạo S3 bucket để lưu bản build production của React.
   - Build frontend bằng lệnh `npm run build`.
   - Upload nội dung thư mục `dist` lên Amazon S3.
   - Tạo Amazon CloudFront distribution để phân phối website React.
   - Cấu hình Origin Access Control để CloudFront truy cập an toàn vào private S3 bucket.
   - Thêm S3 bucket policy cho phép CloudFront đọc các file frontend.
   - Cấu hình default root object là `index.html`.
   - Cấu hình custom error responses cho React Router:
     - 403 → `/index.html` → 200
     - 404 → `/index.html` → 200
   - Cấu hình AWS WAF cho CloudFront distribution để bảo vệ frontend khỏi các request có nguy cơ độc hại.
   - Sử dụng AWS managed rule groups như Amazon IP Reputation List, Common Rule Set và Known Bad Inputs Rule Set.
   - Tạo CloudFront invalidation sau mỗi lần deploy frontend để tránh việc CloudFront vẫn phục vụ bản build cũ.

3. **Xây dựng Upload Backend**
   - Tạo Amazon S3 Input Bucket để lưu các sơ đồ kiến trúc do người dùng upload.
   - Bật server-side encryption cho S3 Input Bucket bằng SSE-S3.
   - Cấu hình lifecycle rule để tự động xóa uploaded diagrams sau 30 ngày nhằm giảm chi phí lưu trữ.
   - Tạo Amazon DynamoDB table tên `AIArchitectureReviews`.
   - Sử dụng `reviewId` làm partition key.
   - Thiết kế DynamoDB item để lưu các thông tin như review ID, file name, S3 key, upload time, status, report path và metadata.
   - Tạo Lambda execution role với các quyền cần thiết cho S3, DynamoDB và CloudWatch Logs.
   - Tạo Lambda Upload Service.
   - Cấu hình environment variables cho Lambda như input bucket name, table name, maximum file size và allowed origins.
   - Triển khai logic upload để validate file, tạo review ID, lưu diagram vào S3 và lưu metadata vào DynamoDB.
   - Cập nhật review status ban đầu, ví dụ `uploaded` hoặc `pending`.

4. **Tích hợp Amazon API Gateway**
   - Tạo Amazon API Gateway endpoint.
   - Tạo và tích hợp các route sau với Lambda:
     - `POST /upload`
     - `GET /reviews`
     - `GET /reviews/{reviewId}`
     - `GET /reviews/{reviewId}/status`
   - Cấu hình CORS cho localhost và CloudFront frontend domain.
   - Kiểm thử upload file từ frontend.
   - Xác nhận file được lưu trong S3 Input Bucket.
   - Xác nhận metadata được lưu trong DynamoDB.
   - Xác nhận frontend có thể gọi API Gateway thành công.

5. **Tích hợp dữ liệu Review với Frontend**
   - Kết nối các trang frontend với dữ liệu API thật.
   - Sử dụng `GET /reviews` để hiển thị Review History.
   - Sử dụng `GET /reviews/{reviewId}` để hiển thị Review Detail.
   - Sử dụng `GET /reviews/{reviewId}/status` để hiển thị Review Progress.
   - Sửa các lỗi liên quan đến mock data, CORS, React Router và xử lý review ID.
   - Chuẩn bị giao diện để hiển thị các trạng thái xử lý như uploaded, processing, analyzed, completed và failed.

---

#### Các bước triển khai tiếp theo

Các phần sau sẽ được triển khai ở giai đoạn tiếp theo của dự án:

1. **Xây dựng Event-Driven Review Workflow**
   - Cấu hình S3 Object Created Event sau khi diagram được upload.
   - Route event từ Amazon S3 đến Amazon EventBridge.
   - Tạo EventBridge rule để bắt event từ S3 Input Bucket.
   - Cấu hình EventBridge target là AWS Step Functions Review Workflow.
   - Tạo AWS Step Functions workflow để điều phối quá trình review.
   - Cấu hình retry và error handling trong Step Functions.
   - Cập nhật review status trong DynamoDB khi workflow bắt đầu xử lý.

2. **Xây dựng Lambda AI Analyze**
   - Tạo AWS Lambda AI Analyze.
   - Nhận thông tin bucket name, object key và review ID từ Step Functions input.
   - Đọc uploaded diagram từ Amazon S3 Input Bucket.
   - Kiểm tra loại file diagram, ví dụ image hoặc draw.io XML.
   - Nếu file là image, Lambda chuẩn bị dữ liệu để gửi sang Amazon Bedrock.
   - Nếu file là draw.io XML, Lambda có thể parse XML để hỗ trợ trích xuất thông tin sơ đồ.
   - Gửi diagram sang Amazon Bedrock để Bedrock phân tích sơ đồ.
   - Nhận architecture JSON từ Amazon Bedrock.
   - Validate architecture JSON trước khi gửi sang bước cost estimation.
   - Cập nhật review status trong DynamoDB nếu extraction thất bại.

3. **Tích hợp Amazon Bedrock để sinh Architecture JSON**
   - Chọn model Bedrock phù hợp có khả năng xử lý hình ảnh và văn bản.
   - Chuẩn bị prompt cho bước diagram-to-JSON extraction.
   - Gửi uploaded diagram sang Amazon Bedrock.
   - Yêu cầu Bedrock nhận diện các thành phần trong sơ đồ, bao gồm:
     - AWS services.
     - Connections giữa các services.
     - Data flow.
     - Text notes.
     - Security components.
     - Storage components.
     - Monitoring components.
     - Notification components.
   - Yêu cầu Bedrock trả về architecture JSON có cấu trúc rõ ràng.
   - Kiểm tra JSON output để đảm bảo đúng format.
   - Chuẩn hóa dữ liệu architecture JSON để dùng cho các bước tiếp theo.

4. **Xây dựng Lambda Cost Tool**
   - Tạo AWS Lambda Cost Tool.
   - Nhận architecture JSON từ Lambda AI Analyze.
   - Đọc danh sách AWS services được phát hiện trong sơ đồ.
   - Gán usage assumptions mặc định cho từng dịch vụ ở mức demo.
   - Ước tính monthly cost cho các dịch vụ chính như S3, CloudFront, AWS WAF, API Gateway, Lambda, DynamoDB, EventBridge, Step Functions, Bedrock, SNS và CloudWatch.
   - Tạo cost estimation result gồm:
     - Service name.
     - Usage assumption.
     - Pricing unit.
     - Estimated monthly cost.
     - Cost notes.
     - Optimization hints.
   - Trả kết quả cost estimation cho Step Functions hoặc gửi tiếp sang bước final AI review.

5. **Tích hợp Amazon Bedrock cho Final AI Architecture Review**
   - Gửi architecture JSON và cost estimation result sang Amazon Bedrock.
   - Chuẩn bị prompt đánh giá kiến trúc theo AWS Well-Architected Framework.
   - Yêu cầu Bedrock phân tích kiến trúc theo các trụ cột:
     - Security.
     - Reliability.
     - Performance Efficiency.
     - Cost Optimization.
     - Operational Excellence.
     - Sustainability.
   - Yêu cầu Bedrock đánh giá điểm mạnh của kiến trúc.
   - Yêu cầu Bedrock phát hiện rủi ro và điểm cần cải thiện.
   - Yêu cầu Bedrock giải thích chi phí dựa trên cost estimation result.
   - Yêu cầu Bedrock đề xuất cost optimization recommendations.
   - Trả về final AI review result để sử dụng cho bước tạo PDF report.
   - Cập nhật review status trong DynamoDB thành `analyzed` sau khi AI review hoàn tất.

6. **Tạo PDF Report**
   - Tạo AWS Lambda PDF Generator.
   - Nhận final AI review result từ Amazon Bedrock.
   - Tạo báo cáo PDF có cấu trúc rõ ràng.
   - Nội dung PDF report nên bao gồm:
     - Review ID.
     - Upload information.
     - Detected AWS services.
     - Architecture JSON summary.
     - Cost estimation.
     - Cost explanation.
     - Well-Architected review result.
     - Risks.
     - Recommendations.
     - Optimization suggestions.
     - Conclusion.
   - Tạo Amazon S3 Report Bucket để lưu PDF report.
   - Bật server-side encryption bằng SSE-S3 cho Report Bucket.
   - Cấu hình lifecycle rule để quản lý vòng đời report nếu cần.
   - Lưu PDF report vào S3 Report Bucket.
   - Cập nhật report URL hoặc report S3 key vào DynamoDB.
   - Cập nhật review status thành `completed`.

7. **Cấu hình CloudWatch Alarm và SNS Alert Notification**
   - Cấu hình Amazon SNS Topic để nhận cảnh báo từ CloudWatch Alarm.
   - Thêm email subscription cho SNS Topic.
   - Xác nhận email subscription để đảm bảo email có thể nhận cảnh báo.
   - Cấu hình CloudWatch Alarm cho AWS Step Functions để phát hiện workflow failed, timed out hoặc aborted.
   - Cấu hình CloudWatch Alarm cho các AWS Lambda functions để phát hiện errors, throttles và duration cao.
   - Cấu hình CloudWatch Alarm cho API Gateway để phát hiện 5XX errors, 4XX errors và latency cao.
   - Cấu hình CloudWatch Alarm cho DynamoDB để phát hiện system errors và throttled requests.
   - Cấu hình CloudWatch Alarm cho EventBridge để phát hiện failed invocations.
   - Khi CloudWatch Alarm chuyển sang trạng thái ALARM, cảnh báo sẽ được gửi đến SNS Topic.
   - Amazon SNS sau đó gửi email cảnh báo đến các địa chỉ đã đăng ký và confirmed.
   - SNS chỉ phục vụ monitoring alert, không gửi email khi từng review hoàn tất.

8. **Monitoring và Security**
   - Sử dụng Amazon CloudWatch để giám sát Lambda logs.
   - Theo dõi API Gateway requests.
   - Theo dõi Step Functions executions.
   - Kiểm tra lỗi trong từng bước workflow.
   - Cấu hình CloudWatch logs cho các Lambda functions.
   - Kiểm tra lại IAM permissions theo nguyên tắc least privilege access.
   - Bật server-side encryption cho các S3 buckets.
   - Giới hạn quyền truy cập S3 theo đúng Lambda function cần sử dụng.
   - Giới hạn quyền DynamoDB theo table cụ thể.
   - Giới hạn quyền Bedrock invoke model cho Lambda cần gọi AI.
   - Cấu hình AWS WAF cho CloudFront distribution để bảo vệ frontend khỏi các request có nguy cơ độc hại.
   - Theo dõi AWS WAF sampled requests để kiểm tra request hợp lệ có bị rule match hay không.
   - Cấu hình rule ở Count mode trong giai đoạn kiểm thử trước khi chuyển sang Block mode nếu cần.
   - Thêm retry logic và error handling trong Step Functions.
   - Cập nhật DynamoDB review status khi workflow bị lỗi.

---

#### Kết quả mong đợi sau workshop

Sau khi hoàn thành workshop, hệ thống kỳ vọng đạt được các kết quả sau:

- Website React được deploy thành công lên Amazon S3 và phân phối thông qua Amazon CloudFront.
- AWS WAF được gắn với CloudFront distribution để bảo vệ frontend khỏi các request có nguy cơ độc hại.
- Frontend có thể upload architecture diagram thông qua API Gateway.
- Lambda Upload Service có thể validate file, lưu diagram vào S3 Input Bucket và ghi metadata vào DynamoDB.
- Frontend có thể hiển thị review history, review detail và review progress từ API thật.
- S3 Object Created Event có thể kích hoạt EventBridge.
- EventBridge có thể khởi động Step Functions Review Workflow.
- Step Functions có thể điều phối toàn bộ quá trình xử lý review.
- Lambda AI Analyze có thể đọc uploaded diagram và gửi diagram sang Amazon Bedrock.
- Amazon Bedrock có thể sinh architecture JSON từ uploaded diagram.
- Lambda Cost Tool có thể ước tính monthly cost dựa trên architecture JSON.
- Amazon Bedrock có thể đánh giá tổng thể kiến trúc dựa trên architecture JSON và cost estimation result.
- Lambda PDF Generator có thể tạo PDF report từ final AI review result.
- PDF report được lưu trong Amazon S3 Report Bucket.
- DynamoDB được cập nhật review history, review status và report information.
- Amazon SNS gửi email cảnh báo khi Amazon CloudWatch Alarm phát hiện lỗi quan trọng trong hệ thống.
- Amazon CloudWatch ghi logs, theo dõi metrics, tạo alarms và gửi cảnh báo lỗi đến Amazon SNS Topic.
- IAM permissions được kiểm tra theo nguyên tắc least privilege access.
- Hệ thống có thể chạy end-to-end từ upload diagram đến tạo PDF report, lưu report vào S3, cập nhật DynamoDB và cho phép người dùng tải PDF thành công.

---

#### Nội dung Workshop

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Điều kiện chuẩn bị](5.2-Prerequisite/)
3. [Triển khai React Frontend với S3, CloudFront và AWS WAF](5.3-Frontend-hosting/)
4. [Xây dựng Upload Backend với API Gateway, Lambda, S3 và DynamoDB](5.4-Upload-backend/)
5. [Tích hợp Review APIs và Frontend Pages](5.5-Review-api/)
6. [Xây dựng Event-Driven AI Review Workflow](5.6-AI-workflow/)
7. [Monitoring với CloudWatch, SNS Alert và IAM Least Privilege](5.7-Monitoring-security/)