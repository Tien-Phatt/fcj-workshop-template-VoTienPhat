---
title: "Worklog Tuần 12"
date: 2026-07-03
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---



### Mục tiêu tuần 12:

* Hoàn thiện các nội dung nâng cao về AWS Identity and Access Management (IAM).
* Hiểu cách áp dụng Condition trong IAM Policy để tăng cường bảo mật.
* Tìm hiểu cơ chế xác thực thông qua Access Key và IAM Role.
* Thực hành triển khai môi trường AWS đáp ứng các yêu cầu bảo mật và quản lý truy cập.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu giới hạn quyền truy cập bằng IAM Condition <br>&emsp;+ Giới hạn Switch Role theo địa chỉ IP <br>&emsp;+ Giới hạn Switch Role theo thời gian <br> - Dọn dẹp tài nguyên sau Lab | 06/07/2026 | 06/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Thực hành:** <br>&emsp;+ Tạo Amazon EC2 Instance <br>&emsp;+ Tạo Amazon S3 Bucket <br> - Chuẩn bị môi trường cho các bài Lab về IAM và Access Key | 07/07/2026 | 07/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Tạo IAM User <br> - Tạo Access Key <br> - Cấu hình AWS CLI sử dụng Access Key <br> - Kiểm tra quyền truy cập đến các dịch vụ AWS | 08/07/2026 | 08/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Tìm hiểu IAM Role dành cho dịch vụ AWS <br> - Tạo IAM Role cho EC2 <br> - Gán Role cho EC2 Instance <br> - Kiểm tra quyền truy cập mà không sử dụng Access Key | 09/07/2026 | 09/07/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành tổng hợp:** <br>&emsp;+ Kiểm tra truy cập Amazon S3 từ EC2 thông qua IAM Role <br>&emsp;+ So sánh Access Key và IAM Role <br>&emsp;+ Tổng kết và dọn dẹp tài nguyên sau Lab | 10/07/2026 | 10/07/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 12:

* Hiểu cách sử dụng **IAM Condition** để tăng cường bảo mật cho quyền truy cập.

* Thực hành thành công việc giới hạn quyền **Switch Role** dựa trên:
  * Địa chỉ IP.
  * Thời gian truy cập.

* Nắm được quy trình tạo và sử dụng **IAM User** cùng **Access Key** để xác thực khi làm việc với AWS CLI.

* Hiểu sự khác nhau giữa:
  * IAM User + Access Key.
  * IAM Role.

* Thực hành triển khai thành công:
  * Amazon EC2 Instance.
  * Amazon S3 Bucket.
  * IAM User.
  * IAM Role.

* Biết cách gán IAM Role cho EC2 Instance để truy cập các dịch vụ AWS mà không cần lưu trữ Access Key trên máy chủ.

* Hiểu các khuyến nghị bảo mật của AWS trong việc quản lý thông tin xác thực và cấp quyền cho ứng dụng.

* Hoàn thành các bài học và bài thực hành về **IAM nâng cao**, **Access Key** và **IAM Role** trong chương trình First Cloud Journey Bootcamp.