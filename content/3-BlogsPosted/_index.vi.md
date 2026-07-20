---
title: "Các bài blog đã đăng"
date: 2026-07-11
weight: 3
chapter: false
pre: " <b> 3. </b> "
---



Phần này tổng hợp các bài blog mình đã thực hiện và đăng tải trong quá trình tìm hiểu các dịch vụ AWS. Nội dung tập trung vào kiến trúc Cloud, Serverless, Generative AI và AI Agent, đồng thời chia sẻ các kiến thức, kinh nghiệm cũng như cách ứng dụng các dịch vụ AWS để giải quyết những bài toán thực tế.

### [Blog 1 - Xây dựng kiến trúc Microservices với Amazon SQS, Amazon SNS và AWS Lambda](3.1-Blog1/)

Bài viết giới thiệu cách xây dựng kiến trúc Microservices theo mô hình giao tiếp bất đồng bộ (Asynchronous Communication) bằng cách kết hợp Amazon SQS, Amazon SNS và AWS Lambda. Nội dung tập trung phân tích bài toán thực tế, kiến trúc tổng thể, luồng hoạt động của hệ thống, vai trò của từng dịch vụ AWS cũng như những lợi ích của mô hình Event-Driven và Serverless trong việc giảm sự phụ thuộc giữa các dịch vụ, tăng khả năng mở rộng và nâng cao khả năng chịu lỗi.

### [Blog 2 - Xây dựng AI AWS Architecture Reviewer – Tự động đánh giá kiến trúc AWS với Amazon Bedrock và Serverless](3.2-Blog2/)

Bài viết chia sẻ quá trình thiết kế hệ thống AI AWS Architecture Reviewer – một ứng dụng Generative AI trên AWS có khả năng tự động phân tích sơ đồ kiến trúc, đánh giá theo AWS Well-Architected Framework, ước tính chi phí triển khai và tạo báo cáo PDF. Nội dung trình bày kiến trúc Serverless kết hợp Event-Driven, luồng xử lý của hệ thống, vai trò của các dịch vụ như Amazon Bedrock, AWS Lambda, Amazon EventBridge, AWS Step Functions, Amazon S3 và Amazon DynamoDB, đồng thời giải thích lý do lựa chọn kiến trúc này cho bài toán thực tế.

### [Blog 3 - Amazon Bedrock AgentCore Web Search: Khi AI Agent không còn bị giới hạn bởi Knowledge Cutoff](3.3-Blog3/)

Bài viết giới thiệu tính năng Web Search trên Amazon Bedrock AgentCore, giúp AI Agent truy xuất thông tin mới từ Internet để tạo ra các câu trả lời có căn cứ và được cập nhật theo thời gian thực. Nội dung phân tích những hạn chế của AI Agent khi chỉ dựa trên dữ liệu huấn luyện, kiến trúc hoạt động của Web Search, vai trò của AgentCore Gateway và Model Context Protocol (MCP), đồng thời trình bày các ứng dụng thực tế cũng như những lợi ích của việc tích hợp khả năng tìm kiếm web vào các hệ thống AI Agent trong môi trường doanh nghiệp.