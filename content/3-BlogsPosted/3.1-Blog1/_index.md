---
title: "[Cloud Architecture] Building a Microservices Architecture with Amazon SQS, Amazon SNS, and AWS Lambda"
date: 2026-07-11
weight: 1
chapter: false
pre: "<b>3.1.</b>"
---

During the development of modern systems, Microservices have gradually become one of the architectures chosen by many organizations thanks to their scalability, independent deployment, and ease of maintenance. However, as the number of services increases, allowing services to communicate directly with one another creates several issues, such as tight coupling, difficulty in scaling, and challenges in handling failures.

AWS provides many services that help solve this problem. Among them, Amazon SQS (Simple Queue Service), Amazon SNS (Simple Notification Service), and AWS Lambda are three services that are commonly used together to build asynchronous communication systems.

In this article, we will explore how these three services can be combined to build a flexible, scalable, and more fault-tolerant Microservices architecture.

---

## 1. Problem Statement

Suppose we are building an e-commerce system.

After a customer clicks the **Place Order** button, the system needs to perform several different tasks:

- Save the order.
- Process the payment.
- Update the inventory.
- Send a confirmation email.
- Send a notification to the application.
- Update the analytics system.

If all of these tasks are executed within a single request, users will have to wait much longer before receiving a response. Furthermore, if just one service encounters an error, the entire process may be affected.

This is a problem that many Microservices systems face when their components depend directly on one another.

---

## 2. Solution with Amazon SQS and Amazon SNS

Instead of allowing services to communicate directly with each other, we can adopt an asynchronous communication architecture.

In this model:

- The Order Service only needs to record the request and send a message to an Amazon SQS queue.
- The downstream services will retrieve messages from the queue one by one for processing.
- Once processing is completed, Amazon SNS publishes notifications to the systems that need to receive the information.

This approach allows each component to operate independently and significantly reduces the dependencies between services.

---

## 3. Overall Architecture

![Kiến trúc Microservices](/fcj-workshop-template-VoTienPhat/images/3-BlogsPosted/Blog1.jpg)

The system is built using the following main components:

- Amazon API Gateway receives requests from users.
- AWS Lambda handles the initial business logic and sends messages to Amazon SQS.
- Amazon SQS acts as a Message Queue, storing requests that need to be processed.
- AWS Lambda Consumers read messages from Amazon SQS to perform individual tasks.
- Amazon SNS publishes notifications to multiple services.
- Amazon DynamoDB stores order data.
- Amazon CloudWatch monitors the entire system.

This architecture enables the system to operate based on the Event-Driven model and makes it easier to scale as the number of users increases.
---

## 4. Workflow

The processing flow is carried out as follows:

1. The user sends an order request through Amazon API Gateway.
2. AWS Lambda receives the request, validates the data, and stores the initial order information.
3. Lambda sends a message to Amazon SQS.
4. Amazon SQS stores the message until a Lambda Consumer is ready to process it.
5. The Lambda Consumer performs the following tasks:
   - Process the payment.
   - Update the inventory.
   - Store the data in Amazon DynamoDB.
6. After completing the processing, Lambda publishes an event to Amazon SNS.
7. Amazon SNS simultaneously sends notifications to multiple systems, including:
   - Confirmation email.
   - Mobile application.
   - Analytics system.
   - Monitoring dashboard.

The entire process is performed asynchronously, helping reduce the response time for users while improving the scalability of the system.

---

## 5. The Role of Each AWS Service

### Amazon SQS

Amazon SQS is a fully managed Message Queue service provided by AWS.

It helps store processing requests in a queue, ensuring that messages are not lost even if downstream services temporarily experience failures.

In addition, Amazon SQS supports **Dead Letter Queue (DLQ)**, which stores messages that fail to be processed so they can be reviewed and handled later.

---

### Amazon SNS

Amazon SNS operates based on the **Publish/Subscribe** model.

With just a single Publish action, the system can simultaneously send notifications to multiple Subscribers, such as Email, SMS, AWS Lambda, or HTTP Endpoints.

This makes it much easier to expand the system without modifying the application's business logic.

---

### AWS Lambda

AWS Lambda processes each business function independently without requiring server management.

Each Lambda function focuses on a single responsibility, making the source code easier to maintain and reuse.

---

### Amazon DynamoDB

Amazon DynamoDB stores order data with high performance and automatic scalability, making it suitable for applications that need to process a large number of requests.

---

### Amazon CloudWatch

Amazon CloudWatch collects logs, metrics, and alarms, allowing the operations team to easily monitor the health and status of the entire system.

---

## 6. Benefits of the Architecture

Combining Amazon SQS, Amazon SNS, and AWS Lambda provides several advantages:

- Reduces dependencies between Microservices.
- Improves the scalability of the system.
- Asynchronous processing helps reduce response time.
- Supports Retry and Dead Letter Queue (DLQ) when failures occur.
- Makes it easy to add new services without changing the existing architecture.

This is also one of the architectures recommended by AWS when building Cloud-Native and Serverless applications.
---

## Personal Perspective

What I find most interesting about this architecture is the way AWS clearly separates data transmission from business logic processing.

Amazon SQS is responsible for storing and distributing messages reliably, while Amazon SNS helps deliver notifications to multiple systems simultaneously. AWS Lambda focuses only on processing the specific business logic assigned to it.

This approach not only makes the system more stable but also allows each component to be scaled independently as traffic increases.

---

## Conclusion

Microservices are not only about splitting an application into multiple services, but also about enabling those services to communicate with one another efficiently.

The combination of Amazon SQS, Amazon SNS, and AWS Lambda makes it possible to build an asynchronous architecture with high scalability, reduced dependencies between components, and improved fault tolerance.

For e-commerce, financial services, IoT applications, or real-time data processing systems, this architecture is a strong option to consider when deploying workloads on AWS.

I hope this article provides you with another perspective on how AWS builds modern Microservices architectures. If you have used Amazon SQS or Amazon SNS in real-world projects, I would be happy to exchange ideas and learn from your experiences.

---

## References

- **Amazon SQS Documentation**  
  https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html

- **Amazon SNS Documentation**  
  https://docs.aws.amazon.com/sns/

- **AWS Lambda Documentation**  
  https://docs.aws.amazon.com/lambda/

- **AWS Well-Architected Framework**  
  https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html

- **AWS Serverless Lens**  
  https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/welcome.html