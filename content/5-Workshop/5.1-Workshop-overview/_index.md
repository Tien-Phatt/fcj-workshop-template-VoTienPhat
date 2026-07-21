---
title : "Workshop Overview"
date : 2026-07-03 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### AI AWS Architecture Reviewer

+ **AI AWS Architecture Reviewer** is a serverless web application that allows users to upload AWS architecture diagrams and receive AI-powered architecture review results based on the **AWS Well-Architected Framework**.
+ The system allows users to submit architecture diagrams through a React frontend, store uploaded files in Amazon S3, store metadata and review status in Amazon DynamoDB, and process the review workflow using serverless services on AWS.
+ The system is designed based on a **serverless event-driven architecture**, where AWS services work together through events, workflows, and Lambda functions to automate the architecture review process.
+ The key point of the AI workflow is that the system not only analyzes architecture diagrams, but also generates **architecture JSON**, estimates **monthly cost**, evaluates the architecture based on the pillars of the AWS Well-Architected Framework, and creates PDF reports for users.
+ The main objective of this workshop is to document the process of deploying the website and backend services on AWS, including frontend hosting, upload processing, review data integration, event-driven workflow, AI analysis, cost estimation, report generation, monitoring, alerting, and security.

---

#### Workshop Overview

In this workshop, the **AI AWS Architecture Reviewer** system is built and deployed step by step, from frontend, backend upload, data storage, automated processing workflow, AI review, cost estimation, PDF report generation, frontend result display, to system monitoring and error alerting.

+ **Frontend and Protection Layer** uses Amazon S3, Amazon CloudFront, and AWS WAF. Amazon S3 is used to store the production build of the React application, Amazon CloudFront is used to distribute the website to users, and AWS WAF is associated with the CloudFront distribution to add a protection layer for the frontend. AWS WAF uses managed rule groups such as Amazon IP Reputation List, Common Rule Set, and Known Bad Inputs Rule Set to monitor, detect, and reduce potentially malicious requests before they reach the application.
+ **API and Upload Layer** uses Amazon API Gateway and AWS Lambda Upload Service to receive upload requests, validate architecture diagram files, generate review IDs, store uploaded diagrams in the Amazon S3 Input Bucket, and store metadata in Amazon DynamoDB.
+ **Review Data Layer** uses Amazon DynamoDB to store review information such as review ID, file name, file type, file size, upload date, review status, S3 input bucket, S3 object key, report path, and review result information.
+ **Event-Driven Workflow Layer** uses Amazon EventBridge and AWS Step Functions to start the review workflow after a diagram is uploaded to the S3 Input Bucket. When S3 generates an Object Created Event, EventBridge captures the event and triggers the Step Functions Review Workflow.
+ **AI Processing Layer** uses AWS Lambda AI Analyzer and Amazon Bedrock. Lambda AI Analyzer reads the uploaded diagram from the S3 Input Bucket, then sends the diagram to Amazon Bedrock so that Bedrock can identify AWS services, connections, text notes, and generate structured architecture JSON.
+ **Cost Analysis Layer** uses AWS Lambda Cost Tool to receive architecture JSON, analyze the AWS services detected in the diagram, and estimate monthly cost based on demo-level usage assumptions.
+ **Final AI Review Layer** continues to use Amazon Bedrock to receive the architecture JSON and cost estimation result, then evaluates the overall architecture based on the AWS Well-Architected Framework, explains cost considerations, detects risks, and suggests optimization recommendations.
+ **Reporting Layer** uses AWS Lambda PDF Generator to create the PDF review report, Amazon S3 Report Bucket to store the generated report, and Amazon DynamoDB to update the completed status and report paths. Users can view the review result and download the PDF report directly from the frontend.
+ **Monitoring, Alerting and Security Layer** uses Amazon CloudWatch, Amazon SNS, AWS IAM, and AWS WAF. Amazon CloudWatch is used to record logs, monitor metrics, and create alarms for Lambda, API Gateway, Step Functions, DynamoDB, and EventBridge. Amazon SNS is integrated with CloudWatch Alarm to send email alerts when the system encounters important errors. AWS IAM is used to apply least privilege access between AWS services, while AWS WAF protects the frontend through the CloudFront distribution.

The architecture below shows the overall workflow of the **AI AWS Architecture Reviewer** system, from users accessing the website through CloudFront protected by AWS WAF, uploading diagrams, processing AI analysis, estimating costs, generating reports, storing review history, to monitoring and sending system error alerts using CloudWatch and SNS.

![AI AWS Architecture Reviewer Overview](/fcj-workshop-template-VoTienPhat/images/5-Workshop/5.1-Workshop-overview/ai-aws-architecture-reviewer-overview.png)