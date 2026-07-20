---
title: "[Amazon Bedrock AgentCore] Web Search: Khi AI Agent không còn bị giới hạn bởi Knowledge Cutoff"
date: 2026-07-11
weight: 3
chapter: false
pre: "<b>3.3.</b>"
---



Hôm nay mình muốn chia sẻ một tính năng khá đáng chú ý vừa được AWS công bố: **Web Search trên Amazon Bedrock AgentCore**.

Trong thời gian gần đây, AI Agent đang trở thành một hướng phát triển rất mạnh. Không chỉ dừng lại ở việc hỏi đáp, AI Agent còn có thể lập kế hoạch, gọi công cụ, truy cập dữ liệu doanh nghiệp và thực hiện nhiều tác vụ thay người dùng.

Tuy nhiên, một vấn đề lớn của nhiều AI Agent là chúng thường bị giới hạn bởi dữ liệu huấn luyện hoặc dữ liệu nội bộ được cung cấp sẵn. Khi người dùng hỏi về thông tin mới, chính sách mới, sản phẩm mới hoặc sự kiện vừa xảy ra, Agent có thể trả lời thiếu chính xác nếu không có nguồn dữ liệu cập nhật.

Để giải quyết vấn đề này, AWS giới thiệu **Web Search on Amazon Bedrock AgentCore** — một công cụ được quản lý hoàn toàn, giúp AI Agent truy xuất kiến thức mới từ web, có nguồn dẫn và vẫn nằm trong môi trường AWS được kiểm soát. Tính năng này được AWS công bố **General Availability (GA)** vào ngày **17/06/2026**.

Trong bài viết này, chúng ta sẽ cùng tìm hiểu:

- Web Search trên AgentCore giải quyết vấn đề gì?
- Kiến trúc hoạt động cơ bản gồm những thành phần nào?
- Vì sao tính năng này đáng chú ý đối với các hệ thống AI Agent?

---

## 1. Vấn đề: AI Agent giỏi nhưng vẫn cần kiến thức mới

Một AI Agent có thể rất mạnh trong việc suy luận, phân tích và tự động hóa tác vụ. Tuy nhiên, nếu Agent không được kết nối với nguồn dữ liệu cập nhật, nó sẽ gặp một giới hạn quen thuộc: **Knowledge Cutoff**.

Điều này đặc biệt quan trọng trong các bài toán như:

- Tìm hiểu tin tức hoặc xu hướng mới.
- Tra cứu thông tin sản phẩm.
- Theo dõi thay đổi chính sách.
- Tổng hợp dữ liệu thị trường.
- Hỗ trợ nghiên cứu.
- Trả lời các câu hỏi cần nguồn dẫn mới nhất.

Trước đây, để giải quyết bài toán này, đội ngũ kỹ thuật thường phải tự tích hợp Search API bên ngoài, tự quản lý API Key, tự xử lý kết quả tìm kiếm, tự lọc nguồn và tự kiểm soát dữ liệu đi ra khỏi hệ thống.

Cách làm này hoàn toàn khả thi, nhưng đối với các doanh nghiệp hoặc hệ thống yêu cầu bảo mật cao, nó tạo thêm nhiều thách thức về quản trị, chi phí, kiểm soát dữ liệu và độ tin cậy.

---

## 2. Web Search trên Amazon Bedrock AgentCore là gì?

Web Search on Amazon Bedrock AgentCore là một công cụ cho phép AI Agent truy xuất thông tin mới từ Internet để tạo ra câu trả lời có căn cứ hơn.

Thay vì chỉ dựa vào kiến thức sẵn có của mô hình, Agent có thể gửi một truy vấn bằng ngôn ngữ tự nhiên đến Web Search. Sau đó, công cụ sẽ trả về các thông tin liên quan như đoạn trích, tiêu đề, nguồn và ngày xuất bản để mô hình sử dụng trong quá trình tạo câu trả lời.

AWS cho biết Web Search hoạt động thông qua **Amazon Bedrock AgentCore Gateway** cùng với **Model Context Protocol (MCP)**.

Có thể hình dung quy trình như sau:

```text
User
   │
   ▼
AI Agent
   │
   ▼
AgentCore Gateway
   │
   ▼
Web Search
   │
   ▼
Relevant Web Sources
   │
   ▼
Grounded Answer
```

Điểm quan trọng ở đây không chỉ là việc AI có thể tìm kiếm thông tin, mà là AI thực hiện việc tìm kiếm trong một kiến trúc được AWS quản lý, giúp giảm đáng kể công sức xây dựng một hệ thống tìm kiếm từ đầu.

---

## 3. Kiến trúc hoạt động cơ bản

![Amazon Bedrock AgentCore Web Search Architecture](/fcj-workshop-template-VoTienPhat/images/3-BlogsPosted/Blog3.jpg)

Một kiến trúc đơn giản của hệ thống có thể mô tả như sau:

```text
User
   │
   ▼
AI Agent
   │
   ▼
Amazon Bedrock AgentCore
   │
   ▼
AgentCore Gateway
   │
   ▼
Web Search Tool
   │
   ▼
Current Web Knowledge
   │
   ▼
Grounded Answer with Sources
```

Trong kiến trúc này:

- **AI Agent** là thành phần tiếp nhận yêu cầu, lập kế hoạch và quyết định thời điểm cần tìm kiếm thông tin mới.
- **Amazon Bedrock AgentCore** là nền tảng giúp triển khai và vận hành AI Agent ở quy mô lớn. AWS mô tả AgentCore là tập hợp các dịch vụ hỗ trợ runtime, memory, identity, gateway và observability cho AI Agent.
- **AgentCore Gateway** đóng vai trò kết nối Agent với các công cụ hoặc API bên ngoài.
- **Web Search Tool** là công cụ tìm kiếm web được AWS quản lý, giúp Agent lấy thông tin mới mà không cần tự xây dựng toàn bộ hạ tầng tìm kiếm.
- **Grounded Answer** là câu trả lời được tạo dựa trên các nguồn thông tin vừa được truy xuất thay vì chỉ dựa trên kiến thức có sẵn của mô hình.

---

## 4. Điểm đột phá của tính năng này

Điểm nổi bật nhất là Web Search giúp AI Agent vượt qua giới hạn của việc chỉ trả lời dựa trên dữ liệu đã biết.

Với các AI Agent truyền thống, khi gặp câu hỏi liên quan đến thông tin mới, hệ thống thường chỉ có hai lựa chọn:

- Từ chối trả lời.
- Trả lời với mức độ không chắc chắn.

Với Web Search, Agent có thể chủ động tìm kiếm các nguồn mới, đọc dữ liệu liên quan và tạo ra câu trả lời có căn cứ hơn.

Một điểm quan trọng khác là AWS nhấn mạnh rằng người dùng có thể xây dựng AI Agent có khả năng truy xuất web mà không cần tự quản lý hạ tầng tìm kiếm hoặc tích hợp Search API từ các nhà cung cấp bên ngoài AWS.

Điều này đặc biệt phù hợp với các tổ chức quan tâm đến:

- Kiểm soát dữ liệu.
- Quản trị nguồn thông tin.
- Triển khai AI Agent trong môi trường doanh nghiệp.
- Giảm sự phụ thuộc vào các dịch vụ bên ngoài.
- Tăng độ tin cậy của câu trả lời nhờ có nguồn dẫn.

Nói cách khác, Web Search không chỉ giúp AI Agent "biết nhiều hơn", mà còn giúp Agent luôn cập nhật và tạo ra các câu trả lời đáng tin cậy hơn.

---

## 5. Ứng dụng thực tế

Web Search trên AgentCore có thể được ứng dụng trong nhiều hệ thống AI Agent khác nhau.

Ví dụ:

- Trong chăm sóc khách hàng, Agent có thể tra cứu thông tin sản phẩm, chính sách hoặc thông báo dịch vụ mới nhất trước khi phản hồi.
- Trong nghiên cứu thị trường, Agent có thể tổng hợp xu hướng, tin tức ngành và các biến động mới từ nhiều nguồn khác nhau.
- Trong phân tích bảo mật, Agent có thể theo dõi các lỗ hổng bảo mật, bản vá hoặc cảnh báo mới.
- Trong môi trường doanh nghiệp, Agent có thể kết hợp dữ liệu nội bộ với thông tin mới trên Internet để tạo ra các câu trả lời đầy đủ hơn.

Điểm chung của các bài toán này là dữ liệu luôn thay đổi theo thời gian. Vì vậy, một AI Agent chỉ dựa vào dữ liệu tĩnh sẽ khó đáp ứng tốt. Web Search giúp bổ sung lớp kiến thức động cho Agent.

---

## 6. Góc nhìn cá nhân

Điều mình thấy thú vị ở Web Search trên AgentCore không chỉ là việc AI có thể tìm kiếm thông tin trên web. Thực tế, khả năng tìm kiếm không phải là điều mới.

Điểm mới nằm ở việc AWS đưa khả năng đó vào một nền tảng AI Agent được quản lý, có thể kết hợp với Gateway, Memory, Identity, Observability và các thành phần khác của AgentCore.

Trước đây, khi xây dựng một AI Agent có khả năng tìm kiếm, đội ngũ phát triển thường phải tự tích hợp rất nhiều thành phần như Search API, Tool Calling, Logging, Permission, Rate Limit, Citation, Filtering và Policy.

Với AgentCore, AWS đang cố gắng biến những thành phần hạ tầng phức tạp đó thành các dịch vụ được quản lý, giúp các nhà phát triển tập trung hơn vào việc xây dựng logic của ứng dụng.

Đây cũng là một hướng đi rất quen thuộc của AWS: khi một nhóm bài toán xuất hiện phổ biến trong nhiều doanh nghiệp, AWS sẽ đóng gói phần hạ tầng phức tạp thành một Managed Service để giảm gánh nặng vận hành.

---

## Kết luận

Web Search trên Amazon Bedrock AgentCore là một tính năng rất đáng chú ý trong làn sóng phát triển AI Agent hiện nay.

Tính năng này giải quyết một vấn đề thực tế: AI Agent không thể chỉ dựa vào kiến thức tĩnh nếu muốn hỗ trợ các tác vụ yêu cầu thông tin mới, có nguồn dẫn và luôn được cập nhật.

Việc kết hợp Amazon Bedrock AgentCore, AgentCore Gateway và Web Search Tool giúp:

- AI Agent truy xuất được thông tin mới từ Internet.
- Câu trả lời có căn cứ và có nguồn dẫn rõ ràng hơn.
- Giảm công sức tích hợp Search API từ các nhà cung cấp bên ngoài.
- Phù hợp hơn với môi trường doanh nghiệp.
- Mở rộng khả năng ứng dụng của AI Agent trong nhiều bài toán thực tế.

Đối với mình, đây là một bước tiến thú vị vì nó cho thấy AI Agent đang dần chuyển từ một chatbot có khả năng trả lời sang một hệ thống có thể tìm hiểu, cập nhật và hành động dựa trên thông tin mới.

Rất mong nhận được những góp ý và chia sẻ từ anh/chị và các bạn, đặc biệt là những người đang quan tâm đến AI Agent, RAG hoặc các hệ thống cần dữ liệu được cập nhật theo thời gian thực.

---

## Nguồn tham khảo

- AWS News Blog – Announcing Web Search on Amazon Bedrock AgentCore
- AWS News Blog – Introducing Amazon Bedrock AgentCore