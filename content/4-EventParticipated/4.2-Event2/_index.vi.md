---
title: "Event 2"
date: 2026-06-06
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---


# Bài Thu Hoạch Buổi "**Community Day / Tech Sharing**"

## Mục Đích Của Sự Kiện

* Chia sẻ các xu hướng và công nghệ mới trong lĩnh vực **Cloud Computing, AI, DevOps, Cybersecurity và Game Development**.
* Giới thiệu các giải pháp hiện đại trên nền tảng **Amazon Web Services (AWS)**.
* Chia sẻ kinh nghiệm thực tế từ các kỹ sư, **System Administrator** và **Developer** đang làm việc trong doanh nghiệp.
* Giúp sinh viên và người mới định hướng lộ trình nghề nghiệp trong lĩnh vực **Cloud, AI và Infrastructure**.
* Kết nối cộng đồng công nghệ thông qua các bài chia sẻ thực tế và demo trực tiếp.

---

## Danh Sách Diễn Giả

* **Bao Huynh** – Junior Cloud Native Developer, Endava Vietnam
* **Lê Hoàng Gia Đại** – AWS & Cyber Security Enthusiast
* **Nguyễn Quốc Bảo** – Godot Multiplayer Developer
* **Trương Huy Phước** – Teamwork & Collaboration Speaker
* **Trần Trung Vinh** – Senior System Administrator, Central Retail Group
* **Việt Phát** – AI Student, Swinburne University (GraphRAG on AWS)

---

## Nội Dung Nổi Bật

### Docker và Containerization

* Giới thiệu sự khác nhau giữa **Virtual Machine** và **Containerization**.
* Phân tích ưu điểm của Docker:

  * **Lightweight**
  * **Portable**
  * **Build once, Run anywhere**
* Tìm hiểu:

  * **Docker Image**
  * **Docker Container**
  * **Dockerfile**
  * **Image Layer**
* Các Docker commands thường dùng trong quá trình phát triển.
* Ứng dụng Docker trong:

  * **CI/CD**
  * **Microservices**
  * **Cloud Native Applications**
  * **Development Environment**

---

### AWS WAF kết hợp Machine Learning NIDS

* Giới thiệu **AWS WAF** và khả năng bảo vệ ứng dụng Web khỏi:

  * **SQL Injection**
  * **Cross Site Scripting (XSS)**
  * **Brute Force**
  * **Bot Attack**
* Phân tích hạn chế của hệ thống WAF truyền thống khi chỉ dựa vào **Rule-Based Detection**.
* Xây dựng hệ thống **Network Intrusion Detection System (NIDS)** bằng **Machine Learning**.
* Sử dụng bộ dữ liệu **CSE-CIC-IDS2018** để huấn luyện mô hình **LightGBM**.
* Thiết kế kiến trúc Cloud trên AWS với:

  * **Amazon EC2**
  * **AWS WAF**
  * **Amazon S3**
  * **Lambda**
  * **CloudWatch**
  * **Security Hub**
  * **GuardDuty**
* Xây dựng Dashboard giám sát và cảnh báo tấn công theo thời gian thực.

---

### Multiplayer Game với AWS WebSocket

* Giới thiệu các mô hình kết nối Multiplayer:

  * **UDP**
  * **HTTP Polling**
  * **WebSocket**
* Xây dựng hệ thống Multiplayer sử dụng:

  * **Godot Engine**
  * **API Gateway WebSocket**
  * **AWS Lambda**
  * **Amazon DynamoDB**
* Thiết kế hệ thống Matchmaking giữa nhiều người chơi.
* Đồng bộ dữ liệu trò chơi theo thời gian thực thông qua WebSocket.
* Phân tích những khó khăn:

  * **Stateless Lambda**
  * **GoneException**
  * **DynamoDB Scan Cost**
* Đề xuất mở rộng bằng **AWS GameLift** cho các game thời gian thực quy mô lớn.

---

### Kỹ Năng Làm Việc Nhóm Hiệu Quả

* Chia sẻ các nguyên tắc giúp tăng hiệu quả làm việc nhóm.
* Bốn nguyên tắc quan trọng:

  * **Clear & Shared Goals**
  * **Right Person, Right Place**
  * **Open Communication**
  * **Personal Accountability**
* Giới thiệu các công cụ hỗ trợ cộng tác:

  * **Trello**
  * **ClickUp**
  * **Slack**
  * **Google Workspace**
  * **Discord**

---

### Hành Trình Từ IT Helpdesk Đến Senior Sysadmin

* Chia sẻ kinh nghiệm phát triển nghề nghiệp từ **Helpdesk** lên **System Administrator**.
* Những kỹ năng quan trọng:

  * **Troubleshooting**
  * **Linux**
  * **Networking**
  * **Monitoring**
  * **Automation**
* Chuyển đổi từ **On-premise** sang **Cloud**.
* Giới thiệu:

  * **Docker**
  * **Terraform**
  * **CI/CD**
  * **DevOps Culture**
* Chia sẻ kinh nghiệm phỏng vấn và xây dựng **Portfolio** thực tế.

---

### GraphRAG với Amazon Bedrock và Neptune

* Giới thiệu **Retrieval-Augmented Generation (RAG)**.
* Phân tích hạn chế của **RAG** truyền thống đối với các bài toán **Multi-hop Reasoning**.
* Giới thiệu **GraphRAG**:

  * **Relationship Awareness**
  * **Multi-hop Reasoning**
* Hai hướng triển khai:

  * **Amazon Bedrock Knowledge Bases + Neptune Analytics**
  * **LlamaIndex + Amazon Neptune**
* Ứng dụng **Graph Database** để nâng cao chất lượng câu trả lời của **Large Language Models**.

---

## Những Gì Học Được

### Cloud & DevOps

* Hiểu rõ sự khác nhau giữa **Virtual Machine** và **Containerization**.
* Biết cách đóng gói ứng dụng bằng **Docker** để đảm bảo tính nhất quán khi triển khai.
* Hiểu vai trò của **Docker** trong **CI/CD** và **Cloud Native**.

---

### Cyber Security

* **AWS WAF** chỉ phù hợp với các cuộc tấn công đã biết.
* **Machine Learning** giúp phát hiện các cuộc tấn công bất thường chưa từng xuất hiện.
* Biết quy trình:

  * Thu thập dữ liệu
  * Tiền xử lý
  * Huấn luyện
  * Triển khai mô hình **ML** trên **AWS**.

---

### Cloud Architecture

* Hiểu cách kết hợp nhiều dịch vụ AWS để xây dựng hệ thống có khả năng mở rộng.
* Nắm được vai trò của:

  * **API Gateway**
  * **Lambda**
  * **DynamoDB**
  * **EC2**
  * **CloudWatch**
* Biết cách xây dựng hệ thống **Event-driven** trên nền tảng **AWS**.

---

### AI & Generative AI

* Hiểu sự khác biệt giữa **RAG** và **GraphRAG**.
* Biết cách sử dụng **Graph Database** để tăng khả năng suy luận nhiều bước.
* Hiểu cách **Amazon Bedrock** tích hợp với **Neptune** trong các ứng dụng **GenAI**.

---

### Software Engineering

* Lựa chọn giao thức phù hợp:

  * **UDP**
  * **WebSocket**
  * **HTTP Polling**
* Hiểu cách xây dựng **Multiplayer Game** theo mô hình **Serverless**.

---

### Soft Skills & Career

* Tầm quan trọng của:

  * **Teamwork**
  * **Communication**
  * **Accountability**
* Hiểu lộ trình phát triển từ:

  * **IT Helpdesk**
  * **System Administrator**
  * **Cloud Engineer**
  * **DevOps Engineer**

---

## Ứng Dụng Vào Công Việc

* Áp dụng **Docker** để đóng gói ứng dụng và triển khai nhất quán giữa các môi trường.
* Xây dựng các hệ thống **Serverless** trên AWS bằng **Lambda** và **API Gateway**.
* Nghiên cứu **GraphRAG** nhằm cải thiện chất lượng chatbot hoặc **AI Assistant**.
* Áp dụng **Machine Learning** để hỗ trợ phát hiện các hành vi bất thường trong hệ thống.
* Tận dụng **WebSocket** để phát triển các ứng dụng thời gian thực.
* Áp dụng các nguyên tắc làm việc nhóm nhằm tăng hiệu quả phối hợp trong các dự án.
* Định hướng lộ trình học tập theo hướng **Cloud, DevOps** hoặc **AI** dựa trên kinh nghiệm thực tế từ các diễn giả.

---

## Trải Nghiệm Trong Sự Kiện

Tham gia buổi **Community Day** là một trải nghiệm rất giá trị khi tôi được tiếp cận nhiều chủ đề công nghệ hiện đại như **Cloud Computing, Artificial Intelligence, Cybersecurity, Game Development, DevOps** và **System Administration**. Nội dung chương trình không chỉ tập trung vào lý thuyết mà còn chia sẻ nhiều kinh nghiệm thực tế từ các kỹ sư đang làm việc trong doanh nghiệp.

### Học hỏi từ các chuyên gia

* Được lắng nghe những chia sẻ thực tế từ các kỹ sư **Cloud, AI** và **System Administrator**.
* Hiểu rõ hơn quá trình triển khai các hệ thống thực tế trên **AWS**.
* Tiếp cận nhiều công nghệ hiện đại như **Docker, GraphRAG, Machine Learning** và **Serverless**.

---

### Trải nghiệm kỹ thuật thực tế

* Quan sát các demo triển khai **Docker** và **Containerization**.
* Hiểu cách xây dựng hệ thống **Multiplayer** sử dụng **AWS WebSocket**.
* Tìm hiểu quy trình xây dựng hệ thống **NIDS** bằng **Machine Learning** trên **AWS**.
* Khám phá cách xây dựng **GraphRAG** với **Amazon Bedrock** và **Neptune**.

---

### Kiến thức nghề nghiệp

* Học được lộ trình phát triển từ **IT Helpdesk** lên **Senior System Administrator**.
* Hiểu tầm quan trọng của việc xây dựng **Portfolio** và tích lũy kinh nghiệm thực tế.
* Có thêm định hướng học tập theo **Cloud, DevOps** và **AI**.

---

### Kỹ năng mềm

* Hiểu rõ vai trò của giao tiếp, trách nhiệm cá nhân và mục tiêu chung trong làm việc nhóm.
* Biết thêm nhiều công cụ hỗ trợ cộng tác như **Trello, Slack** và **Google Workspace**.

---

### Bài học rút ra

* **Docker** và **Containerization** đang trở thành nền tảng quan trọng trong phát triển phần mềm hiện đại.
* AWS cung cấp đầy đủ dịch vụ để xây dựng các hệ thống **Cloud Native** có khả năng mở rộng cao.
* **Machine Learning** có thể bổ sung hiệu quả cho các giải pháp bảo mật truyền thống.
* **GraphRAG** mở ra hướng tiếp cận mới trong việc xây dựng các ứng dụng **Generative AI** có khả năng suy luận tốt hơn.
* Kiến thức chuyên môn cần đi cùng với kỹ năng giao tiếp, làm việc nhóm và tinh thần học hỏi liên tục để phát triển sự nghiệp trong lĩnh vực CNTT.

---

### Một số hình ảnh khi tham gia sự kiện

*Thêm các hình ảnh của bạn tại đây.*

> Nhìn chung, sự kiện đã mang đến nhiều kiến thức thực tiễn về **Cloud Computing, AI, Cybersecurity, DevOps** và **Software Engineering**. Những chia sẻ từ các diễn giả không chỉ giúp tôi mở rộng kiến thức chuyên môn mà còn mang lại nhiều góc nhìn thực tế về định hướng nghề nghiệp, phương pháp học tập cũng như cách triển khai các công nghệ hiện đại vào các dự án trong tương lai.
