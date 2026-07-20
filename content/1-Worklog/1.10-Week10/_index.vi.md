---
title: "Worklog Tuần 10"
date: 2026-07-03
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---



### Mục tiêu tuần 10:

* Hiểu cơ chế ghi nhật ký và giám sát hoạt động trên AWS.
* Tìm hiểu dịch vụ AWS CloudTrail và Amazon Athena trong việc theo dõi, phân tích sự kiện.
* Nắm được quy trình kiểm tra và truy vấn nhật ký phục vụ kiểm toán, giám sát và bảo mật.
* Thực hành xây dựng hệ thống lưu trữ và phân tích nhật ký trên AWS.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu AWS CloudTrail <br>&emsp;+ Khái niệm và vai trò <br>&emsp;+ Event History <br>&emsp;+ Trail <br> - Thực hành tạo CloudTrail | 22/06/2026 | 22/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Tìm hiểu cơ chế lưu trữ nhật ký trên Amazon S3 <br> - Cấu hình CloudTrail ghi log vào S3 Bucket <br> - Kiểm tra và theo dõi các sự kiện phát sinh trong tài khoản AWS | 23/06/2026 | 23/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Tìm hiểu Amazon Athena <br>&emsp;+ Tạo Database <br>&emsp;+ Tạo Table <br>&emsp;+ Truy vấn dữ liệu nhật ký bằng SQL | 24/06/2026 | 24/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Thực hành:** <br>&emsp;+ Truy vấn CloudTrail Logs bằng Amazon Athena <br>&emsp;+ Phân tích các API Call <br>&emsp;+ Kiểm tra lịch sử hoạt động của tài nguyên AWS | 25/06/2026 | 25/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành:** <br>&emsp;+ Chia sẻ dữ liệu đã mã hóa trên Amazon S3 <br>&emsp;+ Kiểm tra quyền truy cập dữ liệu <br>&emsp;+ Dọn dẹp tài nguyên sau khi hoàn thành Lab | 26/06/2026 | 26/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 10:

* Hiểu vai trò của AWS CloudTrail trong việc ghi nhận và theo dõi toàn bộ hoạt động diễn ra trên tài khoản AWS.

* Nắm được quy trình:
  * Tạo CloudTrail.
  * Cấu hình lưu trữ nhật ký trên Amazon S3.
  * Theo dõi lịch sử hoạt động của tài nguyên AWS.

* Hiểu cách sử dụng Amazon Athena để truy vấn dữ liệu nhật ký bằng ngôn ngữ SQL mà không cần triển khai hệ thống cơ sở dữ liệu riêng.

* Thực hành thành công:
  * Tạo CloudTrail.
  * Cấu hình Logging vào Amazon S3.
  * Tạo Database và Table trên Amazon Athena.
  * Truy vấn và phân tích CloudTrail Logs.

* Hiểu quy trình kiểm toán và giám sát hoạt động của hệ thống nhằm phục vụ công tác vận hành và bảo mật.

* Biết cách kết hợp CloudTrail, Amazon S3 và Amazon Athena để xây dựng giải pháp phân tích nhật ký tập trung trên AWS.

* Hoàn thành các bài học và bài thực hành về Logging, Monitoring và Audit trong chương trình First Cloud Journey Bootcamp.