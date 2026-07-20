---
title: "Event 1"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---




# Bài thu hoạch Buổi **"Non-Determinism of "Deterministic" LLM Settings"**

## Mục Đích Của Sự Kiện

* Giải thích cách **Large Language Models (LLMs)** sinh văn bản và lựa chọn **token** tiếp theo.
* Làm rõ vì sao **Temperature = 0** không đảm bảo kết quả hoàn toàn **deterministic** như nhiều người vẫn nghĩ.
* Phân tích các nguyên nhân kỹ thuật và thương mại dẫn đến hiện tượng **non-determinism** trong các mô hình AI hiện đại.
* Chia sẻ các chiến lược giúp tăng tính ổn định và độ tin cậy khi triển khai **LLM** trong môi trường thực tế.
* Giúp **Developer** và **AI Engineer** xây dựng các ứng dụng **Production-ready** với **Large Language Models**.

---

## Danh Sách Diễn Giả

* **Đức Đào** – Solution Architect, Cloud Kinetics

---

## Nội Dung Nổi Bật

### Cách LLM Lựa Chọn Token Tiếp Theo

* Giải thích quy trình sinh văn bản của **Large Language Models**.
* Mỗi bước mô hình sẽ:

  * Tính toán **logits** cho toàn bộ từ vựng.
  * Chuyển đổi thành xác suất bằng **Softmax**.
  * Chọn **token** tiếp theo dựa trên xác suất cao nhất.
* Quá trình này được lặp lại cho đến khi mô hình hoàn thành câu trả lời.

---

### Vai Trò của Temperature, Top-P và Top-K

* Giải thích ảnh hưởng của **Temperature** đến khả năng sinh văn bản của mô hình.
* Các mức **Temperature** phổ biến:

  * **Temperature > 1:** Nội dung đa dạng nhưng nhiều tính ngẫu nhiên.
  * **Temperature = 1:** Cân bằng giữa tính sáng tạo và sự ổn định.
  * **Temperature gần 0:** Kết quả nhất quán hơn.
  * **Temperature = 0:** Về lý thuyết luôn chọn **token** có xác suất cao nhất.
* So sánh vai trò của **Top-P** và **Top-K** trong việc kiểm soát quá trình lấy mẫu (**sampling**).

---

### Giả Định và Thực Tế về Temperature = 0

* Nhiều người tin rằng **Temperature = 0** sẽ luôn tạo ra cùng một kết quả.
* Đây là nền tảng của nhiều ứng dụng như:

  * **Structured Output**
  * **Regression Testing**
  * **Prompt Benchmarking**
  * **Production Reliability**
* Tuy nhiên, các nghiên cứu thực tế cho thấy giả định này không hoàn toàn chính xác.

---

### Nghiên Cứu về Non-Determinism

* Thử nghiệm trên nhiều mô hình:

  * **GPT-3.5 Turbo**
  * **GPT-4o**
  * **Llama 3**
  * **Mixtral**
* Tiến hành nhiều lần với:

  * Cùng **Prompt**
  * Cùng **Seed**
  * **Temperature = 0**
* Kết quả cho thấy:

  * Không có mô hình nào luôn tạo ra kết quả giống nhau.
  * Độ chính xác có thể thay đổi giữa các lần chạy.
  * Nội dung sinh ra đôi khi khác biệt mặc dù cấu hình hoàn toàn giống nhau.

---

### Nguyên Nhân Gây Ra Non-Determinism

#### Nguyên nhân kỹ thuật

* Sai số của phép toán **Floating-point** trên **GPU**.
* **GPU** xử lý song song nên thứ tự thực thi có thể thay đổi.
* Sai số rất nhỏ trong **logits** có thể khiến mô hình lựa chọn **token** khác.

#### Nguyên nhân từ nhà cung cấp dịch vụ

* Các nền tảng AI thường sử dụng **Inference Batching**.
* Nhiều yêu cầu (**request**) được xử lý đồng thời nhằm tối ưu hiệu năng của **GPU**.
* Kết quả của một yêu cầu có thể chịu ảnh hưởng từ các yêu cầu khác trong cùng một **batch**.

---

### Ảnh Hưởng Trong Thực Tế

* Đối với các hệ thống yêu cầu độ chính xác cao như:

  * **Healthcare**
  * **Legal**
  * **Finance**
* Việc giả định rằng **Temperature = 0** luôn ổn định có thể gây ra rủi ro trong quá trình vận hành hệ thống.

---

### Chiến Lược Giảm Thiểu

* Chạy mô hình nhiều lần và sử dụng **Majority Voting**.
* Áp dụng:

  * **JSON Mode**
  * **Function Calling**
  * **Structured Output**
* Tự triển khai mô hình (**Self-hosted LLM**) để kiểm soát hạ tầng.
* Thiết kế hệ thống theo hướng chấp nhận sự biến thiên của **LLM**.
* Xây dựng cơ chế xử lý các kết quả không chắc chắn.

---

### Best Practices

* Không nên luôn đặt **Temperature = 0**.
* **Temperature = 0.1** thường mang lại kết quả ổn định hơn trong nhiều trường hợp.
* Tăng **Repeat Penalty** để giảm hiện tượng lặp nội dung.
* Luôn tham khảo tài liệu của từng mô hình vì mỗi nhà cung cấp có cách triển khai khác nhau.

---

## Những Gì Học Được

### AI & Large Language Models

* Hiểu cách **LLM** sinh từng **token**.
* Hiểu cơ chế hoạt động của:

  * **Softmax**
  * **Temperature**
  * **Top-P**
  * **Top-K**
* Biết vì sao kết quả của **LLM** vẫn có thể thay đổi dù sử dụng cùng một **Prompt**.

---

### Machine Learning Infrastructure

* Hiểu rằng phép toán **Floating-point** trên **GPU** không hoàn toàn xác định.
* **Inference Optimization** có thể ảnh hưởng trực tiếp đến kết quả của mô hình.
* **Non-determinism** là đặc tính của hệ thống chứ không phải lỗi của mô hình.

---

### Production AI

* Không nên giả định **LLM** luôn trả về cùng một kết quả.
* Thiết kế hệ thống cần tính đến sự biến thiên của AI.
* **Testing** và **Validation** đóng vai trò quan trọng trong quá trình triển khai thực tế.

---

### Prompt Engineering

* **Temperature = 0** không phải lúc nào cũng là lựa chọn tối ưu.
* **Structured Output** giúp tăng tính ổn định của kết quả.
* Kết hợp **Prompt Engineering** với các tham số của mô hình để đạt hiệu quả cao hơn.

---

## Ứng Dụng Vào Công Việc

* Thiết kế chatbot có khả năng xử lý các kết quả khác nhau của **LLM**.
* Sử dụng **Structured Output** khi xây dựng các **AI API**.
* Áp dụng **Majority Voting** cho các bài toán yêu cầu độ chính xác cao.
* Điều chỉnh **Temperature**, **Top-P** và **Repeat Penalty** phù hợp với từng bài toán.
* Thiết kế quy trình kiểm thử AI dựa trên nhiều lần chạy thay vì chỉ đánh giá một kết quả duy nhất.

---

## Trải Nghiệm Trong Sự Kiện

Tham gia buổi chia sẻ **"Non-Determinism of "Deterministic" LLM Settings"** giúp tôi hiểu rõ hơn về cách hoạt động thực sự của **Large Language Models**. Trước đây, tôi cho rằng chỉ cần đặt **Temperature = 0** thì mô hình sẽ luôn trả về cùng một kết quả. Tuy nhiên, bài trình bày đã chỉ ra rằng điều này không hoàn toàn đúng trong môi trường triển khai thực tế và còn phụ thuộc vào nhiều yếu tố về hạ tầng cũng như cơ chế suy luận của mô hình.

### Học Hỏi Từ Chuyên Gia

* Hiểu sâu hơn về cơ chế sinh **token** của **LLM**.
* Tiếp cận các nghiên cứu mới về **Non-determinism** trong AI.
* Hiểu rõ hơn những hạn chế khi triển khai **Large Language Models** trên các nền tảng Cloud.

---

### Trải Nghiệm Kỹ Thuật

* Quan sát cách **Temperature**, **Top-P** và **Top-K** ảnh hưởng đến kết quả sinh văn bản.
* Hiểu nguyên nhân gây ra sự khác biệt giữa các lần chạy của cùng một **Prompt**.
* Tìm hiểu ảnh hưởng của **GPU**, **Floating-point Arithmetic** và **Inference Batching**.

---

### Kiến Thức Thực Tế

* Biết cách thiết kế các hệ thống AI có độ tin cậy cao hơn.
* Hiểu vai trò của **Structured Output**, **Majority Voting** và **Guardrails** trong các ứng dụng Production.
* Nhận thức rằng việc triển khai AI không chỉ phụ thuộc vào **Prompt** mà còn phụ thuộc vào hạ tầng và cơ chế suy luận của mô hình.

---

### Bài Học Rút Ra

* **Temperature = 0** không đảm bảo kết quả hoàn toàn **deterministic**.
* **Non-determinism** là đặc điểm tự nhiên của nhiều hệ thống **LLM** hiện đại.
* Khi phát triển ứng dụng AI, cần thiết kế hệ thống để chấp nhận sự biến thiên thay vì cố gắng loại bỏ hoàn toàn.
* Luôn kiểm thử nhiều lần và đánh giá trên nhiều tình huống khác nhau trước khi đưa hệ thống vào môi trường **Production**.

---

### Tổng Kết


> Nhìn chung, buổi chia sẻ đã giúp tôi thay đổi góc nhìn về tính ổn định của **Large Language Models** và hiểu rõ hơn các yếu tố ảnh hưởng đến quá trình suy luận của AI. Những kiến thức này sẽ rất hữu ích khi xây dựng các ứng dụng **Generative AI** có độ tin cậy cao trong môi trường thực tế.
