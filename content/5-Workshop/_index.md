---
title: "Workshop"
date: 2026-07-03
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# AI AWS Architecture Reviewer

#### Overview

**AI AWS Architecture Reviewer** is a serverless web platform that allows users to upload AWS architecture diagrams and receive AI-powered architecture review results based on the **AWS Well-Architected Framework**.

In this workshop, the system is implemented in multiple phases, starting from frontend development, deploying the React website to Amazon S3, distributing the website using Amazon CloudFront, and protecting the frontend with AWS WAF. After that, the system continues with the upload backend using Amazon API Gateway and AWS Lambda, storing architecture diagrams in Amazon S3, storing review metadata and status in Amazon DynamoDB, and then expanding to an automated processing workflow using Amazon EventBridge and AWS Step Functions.

In the AI processing stage, the system uses AWS Lambda AI Analyze to read the uploaded diagram from the Amazon S3 Input Bucket. Lambda AI Analyze sends the diagram to Amazon Bedrock so that Bedrock can identify AWS services, connections, text notes, and generate structured architecture JSON. Then, the architecture JSON is sent to AWS Lambda Cost Tool to estimate monthly costs. The cost estimation result, together with the architecture JSON, is sent back to Amazon Bedrock to evaluate the overall architecture based on the AWS Well-Architected Framework, explain costs, and suggest optimizations.

After the analysis is completed, AWS Lambda PDF Generator creates a PDF report from the final review result, stores the report in the Amazon S3 Report Bucket, and updates the review history in Amazon DynamoDB. Amazon CloudWatch is used to record logs, monitor metrics, and create alarms for important components such as Lambda, API Gateway, Step Functions, EventBridge, and DynamoDB. When an important error affects the system, CloudWatch Alarm sends an alert to an Amazon SNS Topic. Amazon SNS then distributes email alerts to subscribed and confirmed email addresses. AWS IAM is used to control access between services based on the principle of least privilege access. AWS WAF is associated with the CloudFront distribution to protect the frontend from potentially malicious requests.

The project uses the following main AWS services: **Amazon S3, Amazon CloudFront, AWS WAF, Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon EventBridge, AWS Step Functions, Amazon Bedrock, Amazon SNS, Amazon CloudWatch, and AWS IAM**.

The main objective of this workshop is to document the process of deploying the website and backend services, explain how to configure each AWS service, and complete the AI-powered AWS architecture review workflow, including frontend hosting, frontend protection with AWS WAF, diagram analysis, architecture JSON generation, cost estimation, final architecture review, PDF report generation, CloudWatch monitoring, SNS alert notification, and security.

---

#### Overall System Architecture

The architecture of **AI AWS Architecture Reviewer** is designed based on a **serverless event-driven architecture**. Users access the web application through Amazon CloudFront protected by AWS WAF. The React frontend is stored in an Amazon S3 frontend bucket and distributed through CloudFront to improve access performance. AWS WAF is associated with the CloudFront distribution to inspect requests, monitor suspicious requests, and reduce potentially malicious requests before they reach the frontend.

When users upload an AWS architecture diagram, the request is sent from the frontend to Amazon API Gateway. API Gateway forwards the request to AWS Lambda Upload Service. Lambda Upload Service validates the uploaded file, generates a review ID, stores the diagram in the Amazon S3 Input Bucket, and writes the initial metadata to the Amazon DynamoDB Review Database.

After the diagram file is stored in the S3 Input Bucket, Amazon S3 generates an Object Created Event. This event is sent to Amazon EventBridge. EventBridge uses the configured rule to start the AWS Step Functions Review Workflow. Step Functions is responsible for orchestrating the entire review process, including extraction, cost estimation, AI review, PDF generation, result storage, and error handling.

In the Processing Layer, Step Functions invokes AWS Lambda AI Analyze. Lambda AI Analyze reads the uploaded diagram from the S3 Input Bucket, then sends the diagram to Amazon Bedrock. In this step, Amazon Bedrock performs the initial diagram analysis to identify AWS services, connections, text notes, and architecture components. The returned result is structured architecture JSON.

After the architecture JSON is generated, Lambda AI Analyze sends this data to AWS Lambda Cost Tool. Lambda Cost Tool analyzes the list of AWS services detected in the architecture JSON and estimates monthly costs based on demo-level usage assumptions. The cost estimation result includes the service list, usage assumptions, estimated costs, and components that may have a significant impact on the budget.

Next, the architecture JSON and cost estimation result are sent back to Amazon Bedrock to perform the final AI review. In this step, Amazon Bedrock evaluates the entire architecture based on the AWS Well-Architected Framework, including the pillars of Security, Reliability, Performance Efficiency, Cost Optimization, Operational Excellence, and Sustainability. Bedrock also explains costs, identifies risks, and suggests architecture optimization recommendations.

After the final AI review is completed, AWS Lambda PDF Generator creates a PDF report from the analysis result. The report is stored in the Amazon S3 Report Bucket. Lambda PDF Generator also updates review history, review status, and report information in Amazon DynamoDB. Amazon SNS is not used to send email when each review is completed. Instead, Amazon SNS is integrated with Amazon CloudWatch Alarm to send operational email alerts when the system encounters errors, such as Step Functions failed, Lambda error, API Gateway 5XX, DynamoDB throttling, or EventBridge failed invocation.

![AI AWS Architecture Reviewer Workshop](/fcj-workshop-template-VoTienPhat/images/5-Workshop/ai-aws-architecture-reviewer.png)

---

#### Main Workflow

The system workflow includes the following steps:

1. User accesses the web application through Amazon CloudFront.
2. AWS WAF is associated with the CloudFront distribution to inspect requests and protect the frontend.
3. Amazon CloudFront distributes the React frontend from the Amazon S3 frontend bucket.
4. User submits an architecture diagram through the frontend.
5. Amazon API Gateway receives the upload request and sends the request to AWS Lambda Upload Service.
6. AWS Lambda Upload Service validates the upload request and stores the uploaded diagram in the Amazon S3 Input Bucket.
7. Amazon S3 publishes an Object Created Event after the diagram is uploaded successfully.
8. Amazon EventBridge receives the event and starts the AWS Step Functions Review Workflow.
9. AWS Step Functions orchestrates the workflow and invokes AWS Lambda AI Analyze to process the diagram.
10. AWS Lambda AI Analyze sends the uploaded diagram to Amazon Bedrock so Bedrock can identify AWS services, connections, text notes, and generate architecture JSON.
11. AWS Lambda AI Analyze sends the architecture JSON to AWS Lambda Cost Tool to estimate monthly costs.
12. AWS Lambda Cost Tool sends the cost estimation result together with the architecture JSON to Amazon Bedrock to evaluate the entire architecture, explain costs, and suggest optimizations.
13. AWS Lambda PDF Generator creates a PDF report from the final AI review result.
14. AWS Lambda PDF Generator updates review history and review status in the Amazon DynamoDB Review Database.
15. AWS Lambda PDF Generator stores the generated PDF report in the Amazon S3 Report Bucket.
16. The frontend displays the review result and allows users to download the PDF report.
17. Amazon CloudWatch records logs, monitors metrics, and creates alarms for important components such as Lambda, API Gateway, Step Functions, EventBridge, and DynamoDB.
18. When CloudWatch Alarm detects an important system error, the alarm sends an alert to an Amazon SNS Topic.
19. Amazon SNS sends error alert emails to subscribed and confirmed email addresses.

Frontend protection flow:

```text
User
→ AWS WAF
→ Amazon CloudFront
→ Amazon S3 Frontend Bucket
→ React Application
```

Main review flow:

```text
User
→ AWS WAF
→ Amazon CloudFront
→ S3 React Frontend
→ API Gateway
→ Lambda Upload Service
→ S3 Input Bucket
→ EventBridge
→ Step Functions
→ Lambda AI Analyze
→ Amazon Bedrock generates architecture JSON
→ Lambda Cost Tool estimates monthly cost
→ Amazon Bedrock performs final architecture review and cost optimization
→ Lambda PDF Generator
→ S3 Report Bucket
→ DynamoDB
→ Frontend displays results and downloads PDF
```

Monitoring and error alert flow:

```text
AWS Service Error
→ Amazon CloudWatch Logs / Metrics
→ CloudWatch Alarm
→ Amazon SNS Topic
→ Email Alert
```

---

#### AWS Services Used in the Workshop

The main AWS services used in the workshop include:

- **AWS WAF**: Protects the CloudFront distribution from potentially malicious requests. AWS WAF is used with managed rule groups such as Amazon IP Reputation List, Common Rule Set, and Known Bad Inputs Rule Set to monitor, detect, and reduce suspicious requests before they reach the frontend.
- **Amazon CloudFront**: Distributes the React web application to users with better performance and lower latency.
- **Amazon S3 Frontend Bucket**: Stores the production build of the React frontend, including `index.html` and static assets.
- **Amazon API Gateway**: Provides API endpoints for diagram upload, review history, review detail, and review status checking.
- **AWS Lambda Upload Service**: Handles upload requests, validates files, generates review IDs, stores diagrams in the S3 Input Bucket, and writes metadata to DynamoDB.
- **Amazon S3 Input Bucket**: Stores the original AWS architecture diagrams uploaded by users.
- **Amazon DynamoDB Review Database**: Stores metadata, review status, review history, uploaded file information, and PDF report paths.
- **Amazon EventBridge**: Receives S3 Object Created Events and triggers the Step Functions Review Workflow.
- **AWS Step Functions**: Orchestrates the entire review workflow, including diagram extraction, cost estimation, final AI review, PDF generation, and error handling.
- **AWS Lambda AI Analyze**: Reads uploaded diagrams from the S3 Input Bucket and sends them to Amazon Bedrock to generate architecture JSON.
- **Amazon Bedrock**: Used in two stages. The first stage analyzes the diagram and generates architecture JSON. The second stage evaluates the entire architecture based on architecture JSON and the cost estimation result.
- **AWS Lambda Cost Tool**: Receives architecture JSON, identifies AWS services appearing in the diagram, and estimates monthly costs.
- **AWS Lambda PDF Generator**: Creates PDF reports from the final AI review result, including architecture review, cost estimation, cost explanation, and optimization recommendations.
- **Amazon S3 Report Bucket**: Stores the PDF reports generated after the review is completed.
- **Amazon SNS**: Receives alerts from Amazon CloudWatch Alarm and sends system error notification emails to subscribed and confirmed email addresses. In this project, SNS is not used to send email when each review is completed.
- **Amazon CloudWatch**: Records logs, monitors metrics, creates CloudWatch Alarms, and supports troubleshooting for Lambda, API Gateway, Step Functions, EventBridge, and DynamoDB. When an alarm detects an important error, CloudWatch sends the alert to an Amazon SNS Topic.
- **AWS IAM**: Controls access between services based on the principle of least privilege access.

---

#### Current Implementation Status

The following parts have been implemented in the project:

1. **Frontend Development**
   - Created the React frontend using Vite.
   - Built the main application interface.
   - Developed the main pages, including Dashboard, Upload Diagram, Review Progress, Review History, Report Detail, and Settings.
   - Prepared the frontend structure for integration with the backend on AWS.
   - Handled routing using React Router.
   - Prepared the interface to display review status, review history, and review detail.

2. **Frontend Deployment with Amazon S3, Amazon CloudFront, and AWS WAF**
   - Created an S3 bucket to store the production build of React.
   - Built the frontend using the `npm run build` command.
   - Uploaded the contents of the `dist` folder to Amazon S3.
   - Created an Amazon CloudFront distribution to distribute the React website.
   - Configured Origin Access Control so CloudFront can securely access the private S3 bucket.
   - Added an S3 bucket policy to allow CloudFront to read frontend files.
   - Configured the default root object as `index.html`.
   - Configured custom error responses for React Router:
     - 403 → `/index.html` → 200
     - 404 → `/index.html` → 200
   - Configured AWS WAF for the CloudFront distribution to protect the frontend from potentially malicious requests.
   - Used AWS managed rule groups such as Amazon IP Reputation List, Common Rule Set, and Known Bad Inputs Rule Set.
   - Created CloudFront invalidation after each frontend deployment to prevent CloudFront from serving an old build.

3. **Upload Backend Development**
   - Created the Amazon S3 Input Bucket to store architecture diagrams uploaded by users.
   - Enabled server-side encryption for the S3 Input Bucket using SSE-S3.
   - Configured a lifecycle rule to automatically delete uploaded diagrams after 30 days to reduce storage costs.
   - Created an Amazon DynamoDB table named `AIArchitectureReviews`.
   - Used `reviewId` as the partition key.
   - Designed the DynamoDB item structure to store information such as review ID, file name, S3 key, upload time, status, report path, and metadata.
   - Created a Lambda execution role with the required permissions for S3, DynamoDB, and CloudWatch Logs.
   - Created the Lambda Upload Service.
   - Configured Lambda environment variables such as input bucket name, table name, maximum file size, and allowed origins.
   - Implemented upload logic to validate files, generate review IDs, store diagrams in S3, and store metadata in DynamoDB.
   - Updated the initial review status, such as `uploaded` or `pending`.

4. **Amazon API Gateway Integration**
   - Created an Amazon API Gateway endpoint.
   - Created and integrated the following routes with Lambda:
     - `POST /upload`
     - `GET /reviews`
     - `GET /reviews/{reviewId}`
     - `GET /reviews/{reviewId}/status`
   - Configured CORS for localhost and the CloudFront frontend domain.
   - Tested file upload from the frontend.
   - Confirmed that files were stored in the S3 Input Bucket.
   - Confirmed that metadata was stored in DynamoDB.
   - Confirmed that the frontend could call API Gateway successfully.

5. **Review Data Integration with Frontend**
   - Connected frontend pages to real API data.
   - Used `GET /reviews` to display Review History.
   - Used `GET /reviews/{reviewId}` to display Review Detail.
   - Used `GET /reviews/{reviewId}/status` to display Review Progress.
   - Fixed issues related to mock data, CORS, React Router, and review ID handling.
   - Prepared the interface to display processing statuses such as uploaded, processing, analyzed, completed, and failed.

---

#### Next Implementation Steps

The following parts will be implemented in the next stage of the project:

1. **Build the Event-Driven Review Workflow**
   - Configure S3 Object Created Event after a diagram is uploaded.
   - Route events from Amazon S3 to Amazon EventBridge.
   - Create an EventBridge rule to capture events from the S3 Input Bucket.
   - Configure the EventBridge target as the AWS Step Functions Review Workflow.
   - Create an AWS Step Functions workflow to orchestrate the review process.
   - Configure retry and error handling in Step Functions.
   - Update review status in DynamoDB when the workflow starts processing.

2. **Build Lambda AI Analyze**
   - Create AWS Lambda AI Analyze.
   - Receive bucket name, object key, and review ID from Step Functions input.
   - Read the uploaded diagram from the Amazon S3 Input Bucket.
   - Check the diagram file type, such as image or draw.io XML.
   - If the file is an image, Lambda prepares the data to be sent to Amazon Bedrock.
   - If the file is draw.io XML, Lambda can parse the XML to support diagram information extraction.
   - Send the diagram to Amazon Bedrock so Bedrock can analyze it.
   - Receive architecture JSON from Amazon Bedrock.
   - Validate architecture JSON before sending it to the cost estimation step.
   - Update review status in DynamoDB if extraction fails.

3. **Integrate Amazon Bedrock to Generate Architecture JSON**
   - Choose a suitable Bedrock model that can process images and text.
   - Prepare the prompt for the diagram-to-JSON extraction step.
   - Send the uploaded diagram to Amazon Bedrock.
   - Ask Bedrock to identify components in the diagram, including:
     - AWS services.
     - Connections between services.
     - Data flow.
     - Text notes.
     - Security components.
     - Storage components.
     - Monitoring components.
     - Notification components.
   - Ask Bedrock to return clear structured architecture JSON.
   - Check the JSON output to ensure the correct format.
   - Standardize the architecture JSON data for the next steps.

4. **Build Lambda Cost Tool**
   - Create AWS Lambda Cost Tool.
   - Receive architecture JSON from Lambda AI Analyze.
   - Read the list of AWS services detected in the diagram.
   - Assign default usage assumptions for each service at demo level.
   - Estimate monthly costs for main services such as S3, CloudFront, AWS WAF, API Gateway, Lambda, DynamoDB, EventBridge, Step Functions, Bedrock, SNS, and CloudWatch.
   - Create a cost estimation result including:
     - Service name.
     - Usage assumption.
     - Pricing unit.
     - Estimated monthly cost.
     - Cost notes.
     - Optimization hints.
   - Return the cost estimation result to Step Functions or send it to the final AI review step.

5. **Integrate Amazon Bedrock for Final AI Architecture Review**
   - Send architecture JSON and the cost estimation result to Amazon Bedrock.
   - Prepare a prompt to review the architecture based on the AWS Well-Architected Framework.
   - Ask Bedrock to analyze the architecture based on the following pillars:
     - Security.
     - Reliability.
     - Performance Efficiency.
     - Cost Optimization.
     - Operational Excellence.
     - Sustainability.
   - Ask Bedrock to evaluate the strengths of the architecture.
   - Ask Bedrock to identify risks and areas for improvement.
   - Ask Bedrock to explain costs based on the cost estimation result.
   - Ask Bedrock to suggest cost optimization recommendations.
   - Return the final AI review result for the PDF report generation step.
   - Update the review status in DynamoDB to `analyzed` after the AI review is completed.

6. **Generate PDF Report**
   - Create AWS Lambda PDF Generator.
   - Receive the final AI review result from Amazon Bedrock.
   - Create a clearly structured PDF report.
   - The PDF report should include:
     - Review ID.
     - Upload information.
     - Detected AWS services.
     - Architecture JSON summary.
     - Cost estimation.
     - Cost explanation.
     - Well-Architected review result.
     - Risks.
     - Recommendations.
     - Optimization suggestions.
     - Conclusion.
   - Create an Amazon S3 Report Bucket to store PDF reports.
   - Enable server-side encryption using SSE-S3 for the Report Bucket.
   - Configure a lifecycle rule to manage the report lifecycle if needed.
   - Store the PDF report in the S3 Report Bucket.
   - Update the report URL or report S3 key in DynamoDB.
   - Update the review status to `completed`.

7. **Configure CloudWatch Alarm and SNS Alert Notification**
   - Configure an Amazon SNS Topic to receive alerts from CloudWatch Alarm.
   - Add email subscriptions to the SNS Topic.
   - Confirm email subscriptions to ensure alerts can be received by email.
   - Configure CloudWatch Alarm for AWS Step Functions to detect workflow failed, timed out, or aborted.
   - Configure CloudWatch Alarm for AWS Lambda functions to detect errors, throttles, and high duration.
   - Configure CloudWatch Alarm for API Gateway to detect 5XX errors, 4XX errors, and high latency.
   - Configure CloudWatch Alarm for DynamoDB to detect system errors and throttled requests.
   - Configure CloudWatch Alarm for EventBridge to detect failed invocations.
   - When CloudWatch Alarm changes to the ALARM state, the alert is sent to the SNS Topic.
   - Amazon SNS then sends email alerts to subscribed and confirmed addresses.
   - SNS only serves monitoring alerts and does not send email when each review is completed.

8. **Monitoring and Security**
   - Use Amazon CloudWatch to monitor Lambda logs.
   - Monitor API Gateway requests.
   - Monitor Step Functions executions.
   - Check errors in each workflow step.
   - Configure CloudWatch logs for Lambda functions.
   - Review IAM permissions based on the principle of least privilege access.
   - Enable server-side encryption for S3 buckets.
   - Limit S3 access permissions according to the Lambda function that needs access.
   - Limit DynamoDB permissions to the specific table.
   - Limit Bedrock invoke model permissions to the Lambda function that calls AI.
   - Configure AWS WAF for the CloudFront distribution to protect the frontend from potentially malicious requests.
   - Monitor AWS WAF sampled requests to check whether valid requests match any rules.
   - Configure rules in Count mode during the testing phase before switching to Block mode if needed.
   - Add retry logic and error handling in Step Functions.
   - Update DynamoDB review status when the workflow fails.

---

#### Expected Results After the Workshop

After completing the workshop, the system is expected to achieve the following results:

- The React website is successfully deployed to Amazon S3 and distributed through Amazon CloudFront.
- AWS WAF is associated with the CloudFront distribution to protect the frontend from potentially malicious requests.
- The frontend can upload architecture diagrams through API Gateway.
- Lambda Upload Service can validate files, store diagrams in the S3 Input Bucket, and write metadata to DynamoDB.
- The frontend can display review history, review detail, and review progress from real APIs.
- S3 Object Created Event can trigger EventBridge.
- EventBridge can start the Step Functions Review Workflow.
- Step Functions can orchestrate the entire review processing workflow.
- Lambda AI Analyze can read uploaded diagrams and send them to Amazon Bedrock.
- Amazon Bedrock can generate architecture JSON from uploaded diagrams.
- Lambda Cost Tool can estimate monthly costs based on architecture JSON.
- Amazon Bedrock can evaluate the overall architecture based on architecture JSON and the cost estimation result.
- Lambda PDF Generator can create PDF reports from the final AI review result.
- PDF reports are stored in the Amazon S3 Report Bucket.
- DynamoDB is updated with review history, review status, and report information.
- Amazon SNS sends email alerts when Amazon CloudWatch Alarm detects important system errors.
- Amazon CloudWatch records logs, monitors metrics, creates alarms, and sends error alerts to the Amazon SNS Topic.
- IAM permissions are reviewed based on the principle of least privilege access.
- The system can run end-to-end from diagram upload to PDF report generation, store the report in S3, update DynamoDB, and allow users to download the PDF successfully.

---

#### Workshop Content

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequisite/)
3. [Deploy React Frontend with S3, CloudFront and AWS WAF](5.3-Frontend-hosting/)
4. [Build Upload Backend with API Gateway, Lambda, S3 and DynamoDB](5.4-Upload-backend/)
5. [Integrate Review APIs and Frontend Pages](5.5-Review-api/)
6. [Build Event-Driven AI Review Workflow](5.6-AI-workflow/)
7. [Monitoring with CloudWatch, SNS Alert and IAM Least Privilege](5.7-Monitoring-security/)