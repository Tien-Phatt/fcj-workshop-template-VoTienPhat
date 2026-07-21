---
title : "Triển khai React Frontend với S3, CloudFront và AWS WAF"
date : 2026-07-03
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Tổng quan về việc lưu trữ frontend

Trong phần này, frontend React của dự án **AI AWS Architecture Reviewer** được triển khai lên AWS bằng **Amazon S3**, **Amazon CloudFront** và được bảo vệ bằng **AWS WAF**.

Ứng dụng React được build cục bộ bằng Vite. Sau khi bản build production được tạo, các tệp bên trong thư mục `dist` sẽ được tải lên một Amazon S3 bucket. Amazon CloudFront được sử dụng để phân phối website đến người dùng với hiệu suất tốt hơn, khả năng cache tốt hơn và hỗ trợ truy cập thông qua CloudFront domain.

Bên cạnh đó, **AWS WAF** được gắn với CloudFront distribution để bổ sung lớp bảo vệ cho frontend. AWS WAF giúp theo dõi, phát hiện và giảm thiểu các request có nguy cơ độc hại trước khi chúng truy cập vào ứng dụng web.

Luồng triển khai frontend như sau:

```text
Mã nguồn React
→ npm run build
→ thư mục dist
→ Amazon S3 Frontend Bucket
→ Amazon CloudFront
→ AWS WAF Protection
→ Người dùng
```

Luồng truy cập frontend sau khi triển khai:

```text
Người dùng
→ CloudFront Distribution được bảo vệ bởi AWS WAF
→ S3 Frontend Bucket
→ React Application
```

Frontend bucket được sử dụng trong dự án này là:

```text
ai-aws-reviewer-frontend-tiersteam
```

Tên miền CloudFront được sử dụng để truy cập website đã triển khai là:

```text
https://d9353ayez9zar.cloudfront.net
```

---

#### Bước 1: Build ứng dụng React

Mở PowerShell và di chuyển đến thư mục dự án:

```text
cd D:\Learning\AWS\ai-aws-architecture-reviewer
```

Chạy lệnh build:

```text
npm run build
```

Sau khi quá trình build hoàn tất, Vite sẽ tạo các tệp production trong thư mục `dist`.

---

#### Bước 2: Tạo S3 frontend bucket

Tạo một Amazon S3 bucket để lưu trữ các tệp build production của ứng dụng React.

Tên bucket được sử dụng trong dự án này là:

```text
ai-aws-reviewer-frontend-tiersteam
```

Bucket này lưu trữ các tệp như:

```text
index.html
Các tài nguyên JavaScript
Các tài nguyên CSS
Các tài nguyên hình ảnh
Các tệp frontend tĩnh khác
```

![S3 React App](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.3-Frontend-hosting/ai-aws-reviewer-frontend-tiersteam.png)

---

#### Bước 3: Tải các tệp build frontend lên S3

Sau khi ứng dụng React được build, tải nội dung của thư mục `dist` lên S3 frontend bucket.

Chạy lệnh sau:

```text
aws s3 sync dist s3://ai-aws-reviewer-frontend-tiersteam --delete
```

Tùy chọn `--delete` sẽ xóa các tệp cũ trong S3 bucket nếu chúng không còn tồn tại trong bản build mới nhất. Điều này giúp ngăn CloudFront hoặc trình duyệt tải các tài nguyên frontend đã lỗi thời.

---

#### Bước 4: Tạo CloudFront distribution

Amazon CloudFront được sử dụng để phân phối React frontend đến người dùng.

CloudFront distribution kết nối với S3 frontend bucket với vai trò là origin. Người dùng truy cập frontend thông qua tên miền CloudFront thay vì truy cập trực tiếp vào S3 bucket.

Tên miền CloudFront distribution:

```text
https://d9353ayez9zar.cloudfront.net
```

CloudFront distribution ID:

```text
E2JHD76RIC6AML
```

![CloudFront](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.3-Frontend-hosting/ai-aws-reviewer-cloudfront.png)

---

#### Bước 5: Cấu hình Origin Access Control

S3 frontend bucket được giữ ở chế độ private. Để cho phép CloudFront đọc các tệp từ S3 bucket private, Origin Access Control được cấu hình.

Cấu hình này giúp cải thiện tính bảo mật vì người dùng không thể truy cập trực tiếp vào S3 bucket. Họ phải truy cập website thông qua CloudFront.

![CloudFront](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.3-Frontend-hosting/ai-aws-reviewer-cloudfront-2.png)

---

#### Bước 6: Cấu hình S3 bucket policy

Sau khi cấu hình Origin Access Control, cập nhật S3 bucket policy để cho phép CloudFront distribution đọc các object từ bucket.

Bucket policy cho phép CloudFront thực hiện hành động `s3:GetObject` trên các tệp frontend.

![S3 React App Policy](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.3-Frontend-hosting/ai-aws-reviewer-frontend-tiersteam-policy.png)

---

#### Bước 7: Cấu hình default root object

Trong CloudFront, cấu hình default root object là:

```text
index.html
```

Điều này cho phép người dùng truy cập website bằng cách mở tên miền gốc của CloudFront mà không cần nhập `index.html`.

Ví dụ:

```text
https://d9353ayez9zar.cloudfront.net
```

---

#### Bước 8: Cấu hình React Router fallback

Vì frontend là một ứng dụng React Single Page Application, việc truy cập trực tiếp vào các route như `/reviews` hoặc `/settings` có thể trả về lỗi nếu CloudFront không tìm thấy một tệp vật lý tương ứng với đường dẫn đó.

Để khắc phục vấn đề này, các custom error response được cấu hình trong CloudFront:

```text
403 → /index.html → 200
404 → /index.html → 200
```

Điều này cho phép React Router xử lý chính xác các route của frontend.

![CloudFront](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.3-Frontend-hosting/ai-aws-reviewer-cloudfront-3.png)

---

#### Bước 9: Cấu hình AWS WAF cho CloudFront

Để tăng cường bảo mật cho frontend, AWS WAF được cấu hình và gắn với CloudFront distribution.

AWS WAF hoạt động như một lớp bảo vệ trước CloudFront, giúp kiểm tra các request gửi đến website. Các request có dấu hiệu độc hại có thể được theo dõi hoặc chặn dựa trên rule group được cấu hình.

Trong dự án này, AWS WAF được sử dụng để bảo vệ frontend khỏi các request có nguy cơ độc hại bằng các AWS managed rule groups.

Các managed rule groups được sử dụng gồm:

```text
AWSManagedRulesAmazonIpReputationList
AWSManagedRulesCommonRuleSet
AWSManagedRulesKnownBadInputsRuleSet
```

Ý nghĩa của các rule groups:

```text
Amazon IP Reputation List
→ Giúp phát hiện các request đến từ những IP có độ uy tín thấp hoặc có lịch sử hoạt động độc hại.

Common Rule Set
→ Giúp bảo vệ ứng dụng khỏi các kiểu tấn công phổ biến như request bất thường, path traversal hoặc các pattern nguy hiểm thường gặp.

Known Bad Inputs Rule Set
→ Giúp phát hiện các input có dấu hiệu độc hại đã được biết đến.
```

Trong giai đoạn kiểm thử, các rule có thể được cấu hình ở **Count mode** để theo dõi số lượng request khớp rule mà chưa chặn ngay lập tức. Sau khi xác nhận các rule không ảnh hưởng đến request hợp lệ, có thể chuyển sang chế độ block để tăng cường bảo vệ.

Luồng bảo vệ frontend sau khi gắn AWS WAF:

```text
Người dùng
→ AWS WAF kiểm tra request
→ Amazon CloudFront
→ Amazon S3 Frontend Bucket
→ React Application
```

![AWS WAF](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.3-Frontend-hosting/AWS-WAF.jpg)

---

#### Bước 10: Tạo CloudFront invalidation

Sau khi tải một bản build frontend mới lên S3, CloudFront vẫn có thể phân phối các tệp đã được lưu trong bộ nhớ đệm. Để CloudFront tải phiên bản frontend mới nhất, hãy tạo một invalidation.

Chạy lệnh sau:

```text
aws cloudfront create-invalidation --distribution-id E2JHD76RIC6AML --paths "/*"
```

Chờ cho đến khi trạng thái invalidation chuyển thành **Completed**.

![CloudFront](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.3-Frontend-hosting/ai-aws-reviewer-cloudfront-4.png)

---

#### Bước 11: Kiểm tra website đã triển khai

Mở tên miền CloudFront trong trình duyệt:

```text
https://d9353ayez9zar.cloudfront.net
```

React frontend sẽ được tải thành công thông qua CloudFront. Đồng thời, các request truy cập frontend sẽ đi qua lớp bảo vệ của AWS WAF được gắn với CloudFront distribution.

![React App](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.3-Frontend-hosting/Website-project.png)

---

#### Kết quả sau khi hoàn thành

Sau khi hoàn thành phần này, frontend của hệ thống **AI AWS Architecture Reviewer** đã được triển khai thành công với các thành phần sau:

```text
Amazon S3
→ Lưu trữ React production build

Amazon CloudFront
→ Phân phối website đến người dùng

AWS WAF
→ Bảo vệ CloudFront distribution khỏi các request có nguy cơ độc hại
```

Website có thể được truy cập thông qua CloudFront domain:

```text
https://d9353ayez9zar.cloudfront.net
```

Luồng frontend hoàn chỉnh:

```text
Mã nguồn React
→ npm run build
→ thư mục dist
→ Amazon S3 Frontend Bucket
→ Amazon CloudFront
→ AWS WAF Protection
→ Người dùng
```