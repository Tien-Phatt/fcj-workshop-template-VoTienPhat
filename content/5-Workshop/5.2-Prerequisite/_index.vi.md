---
title : "Điều kiện chuẩn bị"
date : 2026-07-03 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

#### AWS Region sử dụng

Trong workshop này, AWS Region chính được sử dụng cho các backend services là:

```text
Asia Pacific (Singapore) - ap-southeast-1
```

Đối với Amazon CloudFront và AWS WAF gắn với CloudFront distribution, phạm vi sử dụng là:

```text
CloudFront (Global)
```

---

#### Công cụ cần chuẩn bị

Trước khi bắt đầu workshop này, hãy đảm bảo các công cụ sau đã được cài đặt và cấu hình trên máy local:

+ Cần có một AWS account để tạo và quản lý các dịch vụ được sử dụng trong dự án này.

+ Node.js và npm là cần thiết để chạy, build và deploy ứng dụng React frontend.

+ Ứng dụng frontend được xây dựng bằng React và Vite.

+ Cần có trình duyệt web để truy cập website đã deploy bằng CloudFront và kiểm thử ứng dụng.

---

#### Thư mục dự án trên máy local

Source code của dự án được lưu trong thư mục local sau:

```text
D:\Learning\AWS\ai-aws-architecture-reviewer
```

Thư mục này chứa source code React frontend, các file cấu hình và kết quả build.

Lệnh build chính của frontend là:

```text
npm run build
```

Sau khi quá trình build hoàn tất, các file production sẽ được tạo trong thư mục `dist`.

---

#### Quyền IAM

IAM user hoặc role được sử dụng trong workshop này cần có quyền để tạo, cấu hình, deploy, kiểm thử và dọn dẹp các dịch vụ AWS được sử dụng bởi dự án **AI AWS Architecture Reviewer**.

Các dịch vụ AWS cần sử dụng bao gồm:

+ Amazon S3,
+ Amazon CloudFront,
+ AWS WAF,
+ Amazon API Gateway,
+ AWS Lambda,
+ Amazon DynamoDB,
+ Amazon EventBridge,
+ AWS Step Functions,
+ Amazon Bedrock,
+ Amazon SNS,
+ Amazon CloudWatch,
+ IAM.

AWS WAF được sử dụng để bảo vệ CloudFront distribution của frontend. Vì vậy, tài khoản triển khai cần có quyền xem, tạo hoặc quản lý Web ACL, protection pack và managed rule groups nếu thực hiện cấu hình WAF trong workshop.

---

#### Tài nguyên Frontend Hosting và Protection

Ứng dụng frontend được deploy bằng Amazon S3, Amazon CloudFront và được bảo vệ bằng AWS WAF.

S3 frontend bucket được sử dụng để lưu các file React production build:

```text
ai-aws-reviewer-frontend-tiersteam
```

CloudFront distribution được sử dụng để phân phối website đến người dùng.

CloudFront domain đã deploy là:

```text
https://d9353ayez9zar.cloudfront.net
```

Lệnh deploy frontend là:

```text
aws s3 sync dist s3://ai-aws-reviewer-frontend-tiersteam --delete
```

Sau khi upload bản build mới, cần tạo CloudFront invalidation:

```text
/*
```

Điều này đảm bảo CloudFront sẽ phục vụ phiên bản mới nhất của ứng dụng React.

AWS WAF được gắn với CloudFront distribution để bổ sung lớp bảo vệ cho frontend. WAF protection pack hoặc Web ACL được dùng để áp dụng các managed rule groups nhằm theo dõi, phát hiện và giảm thiểu các request có nguy cơ độc hại trước khi chúng truy cập vào ứng dụng.

Các managed rule groups được sử dụng gồm:

```text
AWSManagedRulesAmazonIpReputationList
AWSManagedRulesCommonRuleSet
AWSManagedRulesKnownBadInputsRuleSet
```

Trong giai đoạn kiểm thử, các rule có thể được chạy ở **Count mode** để theo dõi request trước khi chuyển sang chế độ chặn thực tế.

---

#### Tài nguyên Backend API

Backend API được public thông qua Amazon API Gateway.

API Gateway endpoint là:

```text
https://031hqksomd.execute-api.ap-southeast-1.amazonaws.com
```

Các API routes cần có gồm:

```text
POST /upload
GET /reviews
GET /reviews/{reviewId}
GET /reviews/{reviewId}/status
```

Các routes này được tích hợp với Lambda Upload Service.

---

#### Tài nguyên lưu trữ

Hệ thống sử dụng Amazon S3 và Amazon DynamoDB để lưu trữ dữ liệu.

S3 Input Bucket lưu các sơ đồ kiến trúc được upload:

```text
ai-aws-reviewer-input-bucket-tiersteam
```

Các diagram được upload sẽ được lưu theo cấu trúc key sau:

```text
uploads/{reviewId}/{fileName}
```

DynamoDB table lưu review metadata và review history:

```text
AIArchitectureReviews
```

Partition key là:

```text
reviewId
```

---

#### Lambda Upload Service

Upload backend được triển khai bằng AWS Lambda.

Tên Lambda function là:

```text
ai-aws-reviewer-upload-service
```

Function này xử lý các nhiệm vụ sau:

+ Validate upload request
+ Validate file type và file size
+ Tạo review ID
+ Upload architecture diagrams vào S3 Input Bucket
+ Ghi review metadata vào DynamoDB
+ Trả review information về frontend
+ Truy xuất review history và review status

Các Lambda environment variables cần có là:

```text
INPUT_BUCKET = ai-aws-reviewer-input-bucket-tiersteam
TABLE_NAME = AIArchitectureReviews
MAX_FILE_SIZE_MB = 5
ALLOWED_ORIGINS = http://localhost:5173,https://d9353ayez9zar.cloudfront.net
```

---

#### File kiểm thử

Chuẩn bị một hoặc nhiều file sơ đồ kiến trúc để kiểm thử upload.

Thư mục ví dụ:

```text
D:\Learning\AWS\test-files
```

Các file ví dụ:

```text
architecture-1.png
architecture-2.jpg
```

Để kiểm thử upload bằng PowerShell, chạy lệnh:

```text
curl.exe -X POST "https://031hqksomd.execute-api.ap-southeast-1.amazonaws.com/upload" -F "file=@D:\Learning\AWS\test-files\architecture-1.png"
```

Nếu upload thành công, API sẽ trả về response có chứa `reviewId`.

Response ví dụ:

```text
{
  "reviewId": "REV-5E57628D",
  "status": "uploaded",
  "fileName": "architecture-1.png",
  "fileType": "image/png",
  "fileSize": 17755,
  "message": "Upload successful"
}
```