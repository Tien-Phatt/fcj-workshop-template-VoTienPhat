---
title: "[Generative AI on AWS] Building AI AWS Architecture Reviewer – Automatically Reviewing AWS Architectures with Amazon Bedrock and Serverless"
date: 2026-07-11
weight: 2
chapter: false
pre: "<b>3.2.</b>"
---


When learning or working with AWS, we often use tools such as **Draw.io** to design system architectures before deploying them in real-world environments. However, after completing the architecture diagram, a common question arises:

> **Does this architecture comply with the AWS Well-Architected Framework?**

In most cases, the evaluation depends on the experience of a Solution Architect or a manual review process. This approach is quite time-consuming, especially for architecture diagrams that contain many AWS services and complex relationships.

To address this challenge, our team developed **AI AWS Architecture Reviewer**—a Generative AI application on AWS that can analyze architecture diagrams, identify AWS services, evaluate the architecture based on the AWS Well-Architected Framework, estimate deployment costs, and automatically generate a PDF report.

In this article, I will share how the system was designed, the AWS services used, and why we chose a Serverless architecture combined with an Event-Driven approach.

---

## 1. Problem Statement

During the process of learning and developing applications on AWS, designing an architecture diagram is almost an essential step. However, evaluating the quality of the architecture is far from simple.

Some common challenges include:

- The architecture may not follow AWS Best Practices.
- It is difficult to identify performance or security bottlenecks.
- The review process depends heavily on the experience of the architect.
- Deployment cost estimation is usually performed manually.

Our idea was to build an AI-powered system that can automate this process, allowing users to receive feedback more quickly and providing them with a stronger basis for improving their architecture before deployment.

---

## 2. Overall Architecture

![AI AWS Architecture Reviewer Architecture](/images/3-BlogsPosted/Blog2.jpg)

The system is built using a **Serverless Architecture** combined with an **Event-Driven Architecture**, leveraging fully managed AWS services.

The architecture consists of the following main layers:

- **Presentation Layer:** Amazon CloudFront and the React frontend allow users to upload AWS architecture diagrams.
- **Processing Layer:** Amazon API Gateway, AWS Lambda, Amazon EventBridge, and AWS Step Functions handle the entire processing workflow.
- **AI Layer:** Amazon Bedrock analyzes the architecture and generates evaluation content.
- **Storage Layer:** Amazon S3 stores architecture diagrams and generated reports, while Amazon DynamoDB stores the evaluation history.
- **Monitoring & Security Layer:** Amazon CloudWatch monitors the system, and AWS Identity and Access Management (IAM) controls access permissions.

Dividing the system into multiple layers makes the architecture clearer, easier to scale, and more convenient to maintain.

---

## 3. System Workflow

The processing workflow consists of the following steps:

1. The user accesses the application and uploads an AWS architecture diagram.
2. Amazon CloudFront delivers the React frontend, while Amazon API Gateway receives the user's request.
3. The Lambda Upload Service stores the diagram in Amazon S3 and generates a new event.
4. Amazon EventBridge receives the event and triggers AWS Step Functions to start the processing workflow.
5. The Lambda functions within Step Functions perform the following tasks in sequence:
   - Extract information from the architecture diagram.
   - Build the architecture model.
   - Send the extracted data to Amazon Bedrock for analysis.
6. Amazon Bedrock evaluates the architecture based on the AWS Well-Architected Framework and generates recommendations for improvement.
7. The system then estimates the deployment cost, generates a PDF report, and stores the results in Amazon S3.
8. Finally, Amazon DynamoDB stores the evaluation history, while Amazon SNS sends a notification after the process is completed.

The entire workflow is fully automated, significantly reducing the amount of manual work required from users.
---

## 4. Key AWS Services

### Amazon Bedrock

Amazon Bedrock serves as the core AI component of the system. It analyzes the architecture diagram, evaluates it based on the AWS Well-Architected Framework, and provides recommendations to improve security, performance, scalability, and cost optimization.

---

### AWS Step Functions

AWS Step Functions orchestrates the entire processing workflow. Instead of allowing Lambda functions to invoke each other directly, the entire process is defined as a State Machine, making it easier to monitor, retry failed tasks, and handle errors.

---

### Amazon EventBridge

Amazon EventBridge is responsible for receiving and routing events after users upload architecture diagrams to the system. This approach reduces the direct dependencies between components and aligns with the Event-Driven architecture recommended by AWS.

---

### AWS Lambda

Each Lambda function is responsible for a single task, such as uploading files, extracting data, performing AI analysis, or generating reports. This design makes the system easier to scale and maintain.

---

### Amazon S3

Amazon S3 stores architecture diagrams, generated PDF reports, and other output files of the system with virtually unlimited scalability.

---

### Amazon DynamoDB

Amazon DynamoDB stores the evaluation history, allowing users to review previous analysis results without having to execute the entire workflow again.

---

### Amazon CloudWatch

Amazon CloudWatch collects logs and metrics from the entire system, enabling the development team to monitor the workflow status and troubleshoot issues whenever necessary.

---

## 5. Why Did Our Team Choose a Serverless Architecture?

One of the most important decisions made by our team was choosing a **Serverless Architecture** instead of deploying the system on traditional servers.

With a Serverless architecture, the team does not need to manage infrastructure. The system can automatically scale based on the number of incoming requests, and costs are charged only for the actual execution time.

In addition, combining Amazon EventBridge with AWS Step Functions makes the workflow much more flexible. If another processing step needs to be added in the future, the team only needs to update the State Machine without modifying the entire architecture.

---

## 6. Value of the Solution

The system provides several notable benefits:

- Automates the AWS architecture review process.
- Helps identify areas that need improvement based on the AWS Well-Architected Framework.
- Generates PDF reports and stores the evaluation history.
- Estimates deployment costs during the analysis process.
- The Serverless architecture makes the system easier to scale while optimizing operational costs.

Although this project was developed as an academic project, the architecture can be further developed into a practical tool to support development teams and Cloud engineers in real-world scenarios.
---

## Personal Perspective

What I find most interesting about this project is not only the use of Amazon Bedrock to analyze architecture diagrams, but also the way multiple AWS services are combined using an Event-Driven architecture.

Instead of allowing Lambda functions to invoke each other directly, our team uses Amazon EventBridge to handle events and AWS Step Functions to orchestrate the workflow. This approach makes the system architecture clearer, reduces dependencies between components, and makes it easier to extend in the future.

In my opinion, this project is also a good example of how Generative AI can be combined with a Serverless architecture to solve a real-world problem on the AWS platform.

---

## Conclusion

AI AWS Architecture Reviewer is an example that demonstrates how Generative AI can do more than just generate content—it can also participate in the process of analyzing and evaluating system architectures.

By combining Amazon Bedrock with Serverless services such as AWS Lambda, Amazon EventBridge, and AWS Step Functions, the system provides an automated, flexible, and scalable processing workflow.

I hope this article gives you another perspective on how Generative AI can be applied in the field of Cloud Architecture. If you have similar ideas or experiences, I would be happy to exchange knowledge and learn from your experiences.

---

## References

- AWS Well-Architected Framework  
  https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html

- Amazon Bedrock Documentation  
  https://docs.aws.amazon.com/bedrock/

- AWS Step Functions Developer Guide  
  https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html

- Amazon EventBridge User Guide  
  https://docs.aws.amazon.com/eventbridge/latest/userguide/what-is-amazon-eventbridge.html

- AWS Lambda Documentation  
  https://docs.aws.amazon.com/lambda/

- Amazon S3 Documentation  
  https://docs.aws.amazon.com/s3/

- Amazon DynamoDB Documentation  
  https://docs.aws.amazon.com/dynamodb/

- Amazon CloudWatch Documentation  
  https://docs.aws.amazon.com/cloudwatch/
