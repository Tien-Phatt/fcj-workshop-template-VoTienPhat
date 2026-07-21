---
title : "Tổng quan Workshop"
date : 2026-07-03 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### AI AWS Architecture Reviewer

+ **AI AWS Architecture Reviewer** là một ứng dụng web serverless giúp người dùng upload sơ đồ kiến trúc AWS và nhận kết quả đánh giá kiến trúc có hỗ trợ bởi AI dựa trên **AWS Well-Architected Framework**.
+ Hệ thống cho phép người dùng gửi sơ đồ kiến trúc thông qua frontend React, lưu trữ file upload trong Amazon S3, lưu metadata và trạng thái của mỗi lần review trong Amazon DynamoDB, đồng thời xử lý workflow review bằng các dịch vụ serverless trên AWS.
+ Hệ thống được thiết kế theo mô hình **serverless event-driven architecture**, trong đó các dịch vụ AWS phối hợp với nhau thông qua event, workflow và Lambda functions để tự động hóa quá trình review kiến trúc.
+ Điểm chính của workflow AI là hệ thống không chỉ phân tích sơ đồ kiến trúc, mà còn sinh **architecture JSON**, ước tính **monthly cost**, đánh giá kiến trúc theo các trụ cột của AWS Well-Architected Framework và tạo báo cáo PDF cho người dùng.
+ Mục tiêu chính của workshop này là ghi lại quá trình triển khai website và các backend services trên AWS, bao gồm frontend hosting, upload processing, review data integration, event-driven workflow, AI analysis, cost estimation, report generation, monitoring, alerting và security.

---

#### Tổng quan Workshop

Trong workshop này, hệ thống **AI AWS Architecture Reviewer** sẽ được xây dựng và triển khai từng bước, từ frontend, backend upload, lưu trữ dữ liệu, workflow xử lý tự động, AI review, cost estimation, tạo báo cáo PDF, hiển thị kết quả trên frontend cho đến monitoring và cảnh báo lỗi hệ thống.

+ **Frontend and Protection Layer** sử dụng Amazon S3, Amazon CloudFront và AWS WAF. Amazon S3 được dùng để lưu trữ bản build production của ứng dụng React, Amazon CloudFront được dùng để phân phối website đến người dùng, còn AWS WAF được gắn với CloudFront distribution để bổ sung lớp bảo vệ cho frontend. AWS WAF sử dụng các managed rule groups như Amazon IP Reputation List, Common Rule Set và Known Bad Inputs Rule Set để theo dõi, phát hiện và giảm thiểu các request có nguy cơ độc hại trước khi chúng truy cập vào ứng dụng.
+ **API and Upload Layer** sử dụng Amazon API Gateway và AWS Lambda Upload Service để nhận upload request, validate file sơ đồ kiến trúc, tạo review ID, lưu uploaded diagrams vào Amazon S3 Input Bucket và lưu metadata vào Amazon DynamoDB.
+ **Review Data Layer** sử dụng Amazon DynamoDB để lưu thông tin review như review ID, file name, file type, file size, upload date, review status, S3 input bucket, S3 object key, report path và thông tin kết quả review.
+ **Event-Driven Workflow Layer** sử dụng Amazon EventBridge và AWS Step Functions để khởi động review workflow sau khi diagram được upload vào S3 Input Bucket. Khi S3 phát sinh Object Created Event, EventBridge sẽ bắt event này và kích hoạt Step Functions Review Workflow.
+ **AI Processing Layer** sử dụng AWS Lambda AI Analyzer và Amazon Bedrock. Lambda AI Analyzer đọc uploaded diagram từ S3 Input Bucket, sau đó gửi diagram sang Amazon Bedrock để Bedrock nhận diện AWS services, connections, text notes và sinh ra architecture JSON có cấu trúc.
+ **Cost Analysis Layer** sử dụng AWS Lambda Cost Tool để nhận architecture JSON, phân tích các AWS services được phát hiện trong sơ đồ và ước tính monthly cost dựa trên các giả định sử dụng ở mức demo.
+ **Final AI Review Layer** tiếp tục sử dụng Amazon Bedrock để nhận architecture JSON và cost estimation result, sau đó đánh giá tổng thể kiến trúc dựa trên AWS Well-Architected Framework, giải thích chi phí, phát hiện rủi ro và đề xuất các hướng tối ưu.
+ **Reporting Layer** sử dụng AWS Lambda PDF Generator để tạo PDF review report, Amazon S3 Report Bucket để lưu generated report và Amazon DynamoDB để cập nhật trạng thái hoàn tất cũng như đường dẫn báo cáo. Người dùng có thể xem kết quả review và tải PDF report trực tiếp từ frontend.
+ **Monitoring, Alerting and Security Layer** sử dụng Amazon CloudWatch, Amazon SNS, AWS IAM và AWS WAF. Amazon CloudWatch được dùng để ghi logs, theo dõi metrics và tạo alarms cho Lambda, API Gateway, Step Functions, DynamoDB và EventBridge. Amazon SNS được tích hợp với CloudWatch Alarm để gửi email cảnh báo khi hệ thống phát sinh lỗi quan trọng. AWS IAM được sử dụng để áp dụng least privilege access giữa các AWS services, trong khi AWS WAF bảo vệ frontend thông qua CloudFront distribution.

Kiến trúc bên dưới thể hiện workflow tổng thể của hệ thống **AI AWS Architecture Reviewer**, từ bước người dùng truy cập website thông qua CloudFront được bảo vệ bởi AWS WAF, upload diagram, xử lý AI analysis, ước tính chi phí, tạo report, lưu review history cho đến monitoring và gửi cảnh báo lỗi hệ thống bằng CloudWatch và SNS.

![Tổng quan AI AWS Architecture Reviewer](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.1-Workshop-overview/ai-aws-architecture-reviewer-overview.png)