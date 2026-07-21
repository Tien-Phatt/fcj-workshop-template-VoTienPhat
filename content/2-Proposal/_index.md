---
title: "Proposal"
date: 2026-07-03
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

This section summarizes the workshop topic, including the problem statement, solution architecture, AWS services used, technical implementation plan, timeline, risks, and expected outcomes.

# AI AWS Architecture Reviewer

## AI-Powered Serverless Platform for Reviewing AWS Architecture Diagrams

### 1. Executive Summary

AI AWS Architecture Reviewer is a serverless web platform designed to help users automatically review AWS architecture diagrams using AI and the AWS Well-Architected Framework. The platform allows users to upload architecture diagrams through a React web application, securely store uploaded files in Amazon S3, record metadata for each review in Amazon DynamoDB, and trigger an automated review workflow.

The solution uses Amazon S3 to store the production build of the React frontend, Amazon CloudFront to distribute the website to users, and AWS WAF to protect the CloudFront distribution from potentially malicious requests. Amazon API Gateway and AWS Lambda are used to handle uploads, Amazon S3 Input Bucket is used to store uploaded architecture diagrams, and Amazon EventBridge and AWS Step Functions are used to start and manage the review workflow.

In the processing workflow, AWS Lambda AI Analyzer reads the uploaded diagram from the Amazon S3 Input Bucket and prepares the input data to be sent to Amazon Bedrock. Amazon Bedrock identifies AWS services, connections, text notes, and architecture components from the diagram, then returns structured architecture JSON. This architecture JSON is used as the central data source for the next analysis steps.

After the architecture JSON is generated, the system sends this data to AWS Lambda Cost Tool to estimate monthly costs based on the AWS services detected in the diagram. Lambda Cost Tool generates a cost estimation result, including the list of services, pricing units, usage assumptions, and estimated monthly costs.

Finally, the architecture JSON and cost estimation result are sent back to Amazon Bedrock to perform an overall architecture review based on the AWS Well-Architected Framework. Amazon Bedrock analyzes aspects such as Security, Reliability, Performance Efficiency, Cost Optimization, Operational Excellence, and Sustainability, while also explaining cost considerations and suggesting optimization recommendations.

After the analysis is completed, AWS Lambda PDF Generator creates a PDF report that includes the architecture review, cost estimation, cost explanation, and optimization recommendations. The report is stored in the Amazon S3 Report Bucket, and the review history is updated in Amazon DynamoDB. Amazon CloudWatch is used to record logs, monitor metrics, and create alarms for important components. Amazon SNS is integrated with CloudWatch Alarm to send email alerts when the system encounters errors. AWS IAM is used to apply permissions based on the principle of least privilege, while AWS WAF protects the frontend through the CloudFront distribution.

The objective of this project is to provide a practical AWS serverless solution that is scalable and cost-optimized, helping students, learners, and cloud project teams review AWS architectures more consistently, reduce manual review effort, and improve their understanding of best practices in cloud architecture design.

---

### 2. Problem Statement

### Current Problem

Reviewing an AWS architecture manually requires knowledge of many AWS services, cloud design patterns, security best practices, reliability strategies, performance optimization, cost optimization, and system operations. For students, cloud beginners, or project teams, determining whether an AWS architecture follows the AWS Well-Architected Framework is often challenging.

Common challenges include:

- AWS architecture diagrams are often reviewed manually and lack consistency.
- Students may not know which important services are missing or which design risks exist in their architecture.
- Manual review requires cloud architecture experience and takes a lot of time.
- There is no centralized system to upload, review, store, and track architecture review history.
- Creating a clear and structured review report manually is time-consuming.
- Teams need an automated solution that can analyze architecture diagrams and provide improvement recommendations.

### Solution

AI AWS Architecture Reviewer provides an automated serverless workflow to review AWS architecture diagrams. Users access the web application through Amazon CloudFront protected by AWS WAF. The React frontend is stored in an Amazon S3 frontend bucket and distributed to users through CloudFront. AWS WAF is associated with the CloudFront distribution to monitor, detect, and reduce potentially malicious requests before they reach the application.

API Gateway forwards upload requests to AWS Lambda Upload Service. The Lambda function validates the uploaded file, stores the diagram in the Amazon S3 Input Bucket, and writes initial metadata to Amazon DynamoDB. After an object is created in the input bucket, Amazon S3 generates an object-created event. This event is routed through Amazon EventBridge to start the AWS Step Functions review workflow.

AWS Step Functions orchestrates the entire review process. First, Step Functions invokes AWS Lambda AI Analyzer to read the uploaded diagram from the S3 Input Bucket. Lambda AI Analyzer prepares the input data and sends the diagram to Amazon Bedrock. Amazon Bedrock analyzes the diagram to identify AWS services, connections, text notes, and important architecture components, then returns structured architecture JSON.

After receiving the architecture JSON, Lambda AI Analyzer sends this data to AWS Lambda Cost Tool. Lambda Cost Tool uses the list of AWS services in the architecture JSON to estimate monthly costs. The result of this step is a cost estimation result, including detected services, usage assumptions, estimated costs, and components that may have a significant impact on the budget.

Next, the architecture JSON and cost estimation result are sent back to Amazon Bedrock to perform the overall architecture review. In this step, Amazon Bedrock analyzes the architecture based on the AWS Well-Architected Framework, explains design risks, evaluates costs, suggests cost optimization, and provides improvement recommendations.

After Bedrock completes the overall review, AWS Lambda PDF Generator creates a PDF report from the AI review result. The report is stored in the Amazon S3 Report Bucket, and the review history is updated in Amazon DynamoDB. Users can view the review result and download the PDF report directly from the frontend.

Amazon CloudWatch is used to record logs, monitor metrics, and create alarms for Lambda, API Gateway, Step Functions, DynamoDB, and EventBridge. When the system encounters an important error, CloudWatch Alarm sends an alert to an Amazon SNS Topic, and SNS then sends an email alert to the subscribed and confirmed email addresses.

Overall system flow:

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
→ Lambda AI Analyzer
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

### Benefits and Return on Investment

The solution provides a practical tool for learning and reviewing AWS architecture design. The system helps reduce manual review effort, improve consistency in architecture evaluation, and help users better understand AWS Well-Architected principles.

Key benefits include:

- Automatically reviews AWS architectures based on Well-Architected best practices.
- Provides faster feedback for students and project teams.
- Centrally stores uploaded diagrams and review metadata.
- Tracks review history using DynamoDB.
- Generates PDF reports for documentation and presentations.
- Allows users to view review results and download PDF reports from the frontend.
- Adds a frontend protection layer using AWS WAF associated with the CloudFront distribution.
- Sends operational error alerts through CloudWatch Alarm and Amazon SNS.
- Uses serverless architecture to reduce operational effort.
- Provides cost-optimized design because most services follow a pay-per-use model.
- Can be easily extended through EventBridge, Step Functions, Lambda, S3, and DynamoDB.

This project also creates long-term value as a reusable educational platform for learning cloud architecture, reviewing AWS projects, and developing AI-powered cloud assessment tools in the future.

---

### 3. Solution Architecture

The platform uses an AWS serverless event-driven architecture. The frontend layer is hosted using Amazon S3, distributed through Amazon CloudFront, and protected by AWS WAF. The API layer is built with Amazon API Gateway and AWS Lambda. Uploaded files are stored in the Amazon S3 Input Bucket, while review metadata is stored in Amazon DynamoDB. The review workflow is triggered by Amazon EventBridge and orchestrated by AWS Step Functions.

In the Processing Layer, AWS Lambda AI Analyzer reads the uploaded diagram from the S3 Input Bucket and sends the diagram to Amazon Bedrock for diagram analysis. Amazon Bedrock returns architecture JSON, including detected AWS services, connections, text notes, and architecture components. The architecture JSON is then sent to AWS Lambda Cost Tool to estimate monthly costs. The cost estimation result, together with the architecture JSON, is sent back to Amazon Bedrock to evaluate the overall architecture based on the AWS Well-Architected Framework and generate the final analysis content. AWS Lambda PDF Generator uses this result to create the PDF report.

The architecture includes the following main steps:

1. User accesses the web application through Amazon CloudFront.
2. AWS WAF is associated with the CloudFront distribution to inspect requests and protect the frontend.
3. CloudFront distributes the React frontend from the Amazon S3 frontend bucket.
4. User submits an architecture diagram through the frontend.
5. API Gateway receives the upload request and sends it to AWS Lambda Upload Service.
6. Lambda Upload Service validates the upload request and stores the uploaded diagram in the Amazon S3 Input Bucket.
7. Amazon S3 generates an object-created event after the diagram is uploaded.
8. Amazon EventBridge receives the object-created event and starts the AWS Step Functions review workflow.
9. AWS Step Functions orchestrates the review workflow and invokes AWS Lambda AI Analyzer.
10. AWS Lambda AI Analyzer sends the uploaded diagram to Amazon Bedrock so Bedrock can identify AWS services, connections, text notes, and generate architecture JSON.
11. AWS Lambda AI Analyzer sends the architecture JSON to AWS Lambda Cost Tool to estimate monthly costs.
12. AWS Lambda Cost Tool sends the cost estimation result together with the architecture JSON to Amazon Bedrock to evaluate the entire architecture, explain costs, and suggest optimizations.
13. AWS Lambda PDF Generator creates a PDF report from the final AI review result.
14. AWS Lambda PDF Generator stores the generated PDF report in the Amazon S3 Report Bucket.
15. AWS Lambda PDF Generator updates review history, report path, and review status in Amazon DynamoDB.
16. The frontend displays the review result and allows users to download the PDF report.
17. Amazon CloudWatch records logs, monitors metrics, and creates alarms for Lambda, Step Functions, API Gateway, DynamoDB, and EventBridge.
18. Amazon SNS receives alerts from CloudWatch Alarm and sends email alerts when the system encounters important errors.
19. AWS IAM applies least privilege access between services.

![AI AWS Architecture Reviewer Architecture](/fcj-workshop-template-VoTienPhat/images/2-Proposal/ai-aws-architecture-reviewer-proposal.png)

### AWS Services Used

- **AWS WAF**: Protects the CloudFront distribution from potentially malicious requests using managed rule groups such as Amazon IP Reputation List, Common Rule Set, and Known Bad Inputs Rule Set.
- **Amazon CloudFront**: Distributes the React web application and improves frontend performance.
- **Amazon S3 Frontend Bucket**: Stores the React production build, including `index.html` and static assets.
- **Amazon API Gateway**: Provides API endpoints for upload, review history, review detail, and review status.
- **AWS Lambda Upload Service**: Validates upload requests, stores uploaded diagrams, writes metadata, and handles review data APIs.
- **Amazon S3 Input Bucket**: Stores original architecture diagrams uploaded by users.
- **Amazon DynamoDB**: Stores review metadata, upload information, review status, and review history.
- **Amazon EventBridge**: Captures S3 object-created events and triggers the review workflow.
- **AWS Step Functions**: Orchestrates the review workflow with retry and error handling.
- **AWS Lambda AI Analyzer**: Reads uploaded architecture diagrams from the Amazon S3 Input Bucket, prepares input data, and sends diagrams to Amazon Bedrock to identify AWS services, connections, text notes, and generate architecture JSON.
- **Amazon Bedrock**: Used in two main stages. In the first stage, Bedrock analyzes diagrams to generate structured architecture JSON. In the second stage, Bedrock receives architecture JSON and cost estimation results to evaluate the overall architecture based on the AWS Well-Architected Framework, explain costs, and suggest optimizations.
- **AWS Lambda Cost Tool**: Receives architecture JSON from Lambda AI Analyzer, analyzes detected AWS services in the diagram, and estimates monthly costs. The cost estimation result is sent back to Amazon Bedrock for the overall review and cost optimization step.
- **AWS Lambda PDF Generator**: Creates PDF reports from the final AI review result, including architecture review, cost estimation, cost explanation, and optimization recommendations.
- **Amazon S3 Report Bucket**: Stores generated PDF reports.
- **Amazon CloudWatch**: Provides logging, monitoring, metric filters, alarms, and troubleshooting for Lambda, API Gateway, Step Functions, DynamoDB, and EventBridge.
- **Amazon SNS**: Receives alerts from CloudWatch Alarm and sends email alerts when the system encounters important errors.
- **AWS IAM**: Controls access permissions for services using least privilege access.

### Component Design

- **Frontend and Protection Layer**: The React application is hosted in Amazon S3, distributed through Amazon CloudFront, and protected by AWS WAF. The frontend includes Dashboard, Upload Diagram, Review Progress, Review History, Report Detail, and Settings pages.
- **API Layer**: Amazon API Gateway provides endpoints such as `POST /upload`, `GET /reviews`, `GET /reviews/{reviewId}`, and `GET /reviews/{reviewId}/status`.
- **Upload Processing Layer**: AWS Lambda Upload Service validates uploaded files, creates a unique review ID, stores files in S3, and writes metadata to DynamoDB.
- **Storage Layer**: Amazon S3 Input Bucket stores uploaded diagrams, Amazon S3 Report Bucket stores generated PDF reports, and DynamoDB stores metadata and review history.
- **Workflow Layer**: EventBridge receives object-created events from S3 and starts Step Functions to orchestrate the review process.
- **AI Processing Layer**: AWS Lambda AI Analyzer reads uploaded diagrams from the S3 Input Bucket and sends them to Amazon Bedrock to extract architecture information. Amazon Bedrock identifies AWS services, connections, text notes, and returns architecture JSON. The architecture JSON is then used as input data for cost estimation and the overall architecture review.
- **Cost Analysis Component**: AWS Lambda Cost Tool receives architecture JSON, identifies AWS services appearing in the diagram, and estimates monthly costs based on demo-level usage assumptions. The cost estimation result helps the report provide not only architecture design evaluation but also an overview of expected operational costs.
- **Final AI Review Component**: Amazon Bedrock receives architecture JSON and cost estimation results to evaluate the entire architecture based on the AWS Well-Architected Framework. The analysis result includes strengths, risks, issues for improvement, cost explanation, and optimization suggestions.
- **Reporting Layer**: AWS Lambda PDF Generator receives the final AI review result from Amazon Bedrock, creates a PDF report, and stores the report in the Amazon S3 Report Bucket.
- **Monitoring and Alerting Layer**: Amazon CloudWatch records logs, monitors metrics, creates metric filters, and creates CloudWatch Alarms. Amazon SNS receives alerts from CloudWatch Alarm and sends email alerts to subscribed addresses.
- **Security Layer**: AWS IAM applies least privilege access between services. AWS WAF protects the frontend by inspecting requests before CloudFront and reducing potentially malicious requests.

---

### 4. Technical Implementation

**Implementation Phases**

The project is implemented in multiple phases, starting from frontend development and the basic upload function, then expanding to the event-driven AI workflow, PDF reporting, monitoring, alerting, and security.

- **Frontend development and hosting**: Build the React frontend using Vite, create the main pages, build production files, upload them to Amazon S3, distribute the frontend through Amazon CloudFront, and associate AWS WAF with the CloudFront distribution to strengthen frontend protection.
- **Upload backend implementation**: Create the S3 Input Bucket, DynamoDB review table, Lambda execution role, and Lambda Upload Service to handle file uploads and store metadata.
- **API Gateway and Review Data API**: Create API Gateway routes for diagram upload and retrieving review data. Configure CORS to allow requests from the deployed CloudFront frontend.
- **Event-Driven Review Workflow**: Configure S3 object-created events, route events through EventBridge, and start the Step Functions workflow to process uploaded architecture diagrams.
- **AI Analysis and Reporting**: Use Lambda AI Analyzer to read uploaded diagrams and send them to Amazon Bedrock to generate architecture JSON. Then, the architecture JSON is sent to Lambda Cost Tool to estimate monthly costs. The architecture JSON and cost estimation result are sent back to Amazon Bedrock to evaluate the overall architecture based on the AWS Well-Architected Framework. Finally, Lambda PDF Generator creates the PDF report and stores it in the S3 Report Bucket.
- **Monitoring, Alerting, and Security**: Monitor logs, metrics, and workflow execution using CloudWatch. CloudWatch Alarm sends alerts to the Amazon SNS Topic to send email alerts when the system encounters errors. AWS IAM applies least privilege permissions, while AWS WAF protects the frontend through the CloudFront distribution.

### Technical Requirements

- React frontend using Vite.
- Amazon S3 frontend bucket for the React production build.
- Amazon CloudFront distribution with Origin Access Control.
- AWS WAF Web ACL or protection pack associated with the CloudFront distribution.
- API Gateway for backend APIs.
- AWS Lambda for upload handling and review API logic.
- Amazon S3 Input Bucket for uploaded diagrams.
- Amazon DynamoDB for metadata and review history.
- Amazon EventBridge for event-driven workflow triggering.
- AWS Step Functions for review orchestration.
- AWS Lambda AI Analyzer for diagram parsing.
- Amazon Bedrock for diagram-to-JSON extraction and final AI architecture review.
- AWS Lambda Cost Tool for monthly cost estimation based on architecture JSON.
- AWS Lambda PDF Generator for report generation.
- Amazon S3 Report Bucket for PDF storage.
- Amazon CloudWatch for logging, monitoring, metric filters, and alarms.
- Amazon SNS for email alerts when CloudWatch Alarm detects important errors.
- IAM for least privilege access control.

---

### 5. Roadmap and Implementation Milestones

**Project Timeline**

The project is planned and implemented over 4 internship weeks, from Week 9 to Week 12. The roadmap focuses on architecture design, frontend development, backend configuration on AWS, system deployment, AI workflow integration, testing, monitoring, security, and documentation completion.

- **Week 9: Project Planning and Architecture Design**
  - Define the project topic: AI AWS Architecture Reviewer.
  - Analyze system requirements and the main user workflow.
  - Study the AWS Well-Architected Framework.
  - Select the AWS services required for the solution.
  - Design the overall AWS serverless architecture diagram.
  - Plan the main application features, including Dashboard, Upload Diagram, Review Progress, Review History, Report Detail, and Settings.

- **Week 10: Frontend Development**
  - Create the React project using Vite.
  - Build the main frontend layout with Header, Sidebar, and Main Content.
  - Develop the Dashboard page.
  - Develop the Upload Diagram page.
  - Develop the Review Progress page.
  - Develop the Review History page.
  - Develop the Report Detail and Settings pages.
  - Prepare the frontend structure for integration with the AWS backend APIs.

- **Week 11: AWS Backend Development and Frontend Deployment**
  - Create the S3 Frontend Bucket to store the production build of React.
  - Build and upload React production files to Amazon S3.
  - Create the Amazon CloudFront Distribution.
  - Configure Origin Access Control, S3 bucket policy, default root object, SPA fallback, and CloudFront invalidation.
  - Configure AWS WAF for the CloudFront distribution to protect the frontend from potentially malicious requests.
  - Create the S3 Input Bucket to store architecture diagrams uploaded by users.
  - Create the DynamoDB Review Table to store metadata for each review.
  - Create the Lambda Execution Role with the required permissions for S3, DynamoDB, and CloudWatch.
  - Create the Lambda Upload Service.
  - Create API Gateway routes, including `POST /upload`, `GET /reviews`, `GET /reviews/{reviewId}`, and `GET /reviews/{reviewId}/status`.
  - Configure CORS for localhost and the CloudFront frontend domain.
  - Test file upload from the frontend to S3 and metadata writing to DynamoDB.

- **Week 12: AI Workflow Integration, Reporting, Testing, and Completion**
  - Create the AWS Step Functions Review Workflow.
  - Create Lambda AI Analyzer to read uploaded diagrams from the S3 Input Bucket.
  - Integrate Amazon Bedrock in the first stage to analyze diagrams and generate architecture JSON.
  - Create Lambda Cost Tool to estimate monthly costs based on architecture JSON.
  - Integrate Amazon Bedrock in the final review step to evaluate the entire architecture based on the AWS Well-Architected Framework, explain costs, and suggest optimizations.
  - Create Lambda PDF Generator to generate review reports from the final AI review result.
  - Store generated PDF reports in the S3 Report Bucket.
  - Display real review data on the frontend and complete the PDF download feature.
  - Monitor logs, metrics, and execution status using Amazon CloudWatch.
  - Configure CloudWatch Alarms and Amazon SNS to send email alerts when the system encounters errors.
  - Review IAM permissions based on the principle of least privilege access.
  - Perform end-to-end testing for upload, diagram extraction, cost estimation, AI review, report generation, PDF download, and monitoring alerts.

---

### 6. Budget Estimation

The project uses AWS serverless services, so the cost mainly depends on usage. During the MVP and medium demo stage, the expected traffic is not high, but the system still includes all major components such as frontend hosting, frontend protection, API backend, file storage, metadata database, event-driven workflow, AI analysis, PDF report generation, monitoring, and alerting.

### Calculation Assumptions

The budget estimation is based on a medium demo scale as follows:

- Number of users: around 5–10 users.
- Number of diagram uploads: around 200–300 diagrams/month.
- Average diagram size: around 3–5 MB.
- Number of generated PDF reports: around 200–300 reports/month.
- Number of review workflow executions: around 200–300 executions/month.
- Number of API requests: around 5,000–15,000 requests/month.
- Each workflow includes the following steps: upload diagram, store metadata, trigger workflow, send diagram to Bedrock to generate architecture JSON, estimate monthly cost using Lambda Cost Tool, send architecture JSON and cost estimation result to Bedrock for overall review, generate PDF report, store report, and update review history.
- Amazon Bedrock is used for architecture analysis at demo scale, not for large production traffic.
- AWS WAF is used to protect the CloudFront distribution at a basic level with managed rule groups.

### Estimated Infrastructure Cost

- **Amazon S3**: around **0.10–0.50 USD/month**  
  Used to store React frontend build files, uploaded diagrams, and generated PDF reports. With only a few GB of storage during the demo stage, the S3 cost is very low.

- **Amazon CloudFront**: around **0.00–1.00 USD/month**  
  Used to distribute the frontend web application. With medium demo traffic, CloudFront cost is usually low because data transfer is not high.

- **AWS WAF**: cost depends on the number of Web ACLs, rule groups, and inspected requests.  
  In this project, AWS WAF is used at demo scale to protect the CloudFront distribution with managed rule groups. The actual cost depends on the Web ACL configuration and the number of frontend requests.

- **Amazon API Gateway**: around **0.05–0.20 USD/month**  
  Used to handle API requests such as `POST /upload`, `GET /reviews`, `GET /reviews/{reviewId}`, and `GET /reviews/{reviewId}/status`.

- **AWS Lambda**: around **0.00–0.70 USD/month**  
  Used to handle uploads, validate files, write metadata, read uploaded diagrams, process architecture JSON, estimate monthly cost through Lambda Cost Tool, and generate PDF reports. With demo-level request volume, Lambda cost remains very low.

- **AWS Lambda Cost Tool**: around **0.00–0.20 USD/month**  
  Used to receive architecture JSON, identify AWS services in the diagram, and estimate monthly costs. With a demo scale of 200–300 diagrams/month, the cost of running Lambda Cost Tool is very low. The actual cost depends on Lambda runtime duration, the number of services to estimate, and the complexity of the cost estimation logic.

- **Amazon DynamoDB**: around **0.05–0.30 USD/month**  
  Used to store review metadata, review history, and review status. Each review record is small, so DynamoDB cost is low during the demo stage.

- **Amazon EventBridge**: around **0.01–0.05 USD/month**  
  Used to route S3 object-created events and trigger the workflow after users upload diagrams.

- **AWS Step Functions**: around **0.00–0.30 USD/month**  
  Used to orchestrate the review workflow. With around 200–300 workflow executions/month, Step Functions cost remains very low if the number of state transitions is not too high.

- **Amazon SNS**: around **0.00–0.10 USD/month**  
  Used to send email alerts when CloudWatch Alarm detects important errors in the system.

- **Amazon CloudWatch**: around **0.50–2.00 USD/month**  
  Used to store logs, monitor metrics, create metric filters, create alarms, and support troubleshooting for Lambda, API Gateway, Step Functions, DynamoDB, and EventBridge. CloudWatch cost depends on the amount of logs generated during testing and debugging.

- **Amazon Bedrock**: around **5.00–35.00 USD/month**  
  Used in two stages: analyzing diagrams to generate architecture JSON and evaluating the overall architecture based on architecture JSON and cost estimation results. This is the most variable cost component because it depends on the selected model, input/output tokens, diagram size, architecture JSON length, cost estimation detail, and number of AI calls. Using a smaller model, limiting prompt length, and reducing input data can lower the cost.

### Total Estimated Cost

With a medium demo scale, the estimated infrastructure cost is around:

**10–45 USD/month**

Equivalent to around:

**120–540 USD/year**

Among these services, basic serverless services such as S3, CloudFront, API Gateway, Lambda, DynamoDB, EventBridge, Step Functions, SNS, and Lambda Cost Tool account for relatively low costs. AWS WAF may add extra cost depending on the number of rules and inspected requests. The largest cost driver remains Amazon Bedrock because the system uses Bedrock for both diagram-to-architecture JSON generation and the final AI architecture review.

### Notes

To control costs, the system should limit upload file size, limit the number of AI calls, choose a suitable Bedrock model, reduce diagram data before sending it to Bedrock, standardize architecture JSON only to the necessary level, send only important cost estimation results instead of all raw data, reduce unnecessary logs, configure AWS WAF appropriately for demo needs, and use AWS Budgets to monitor monthly costs.

---

### 7. Risk Assessment

#### Risk Matrix

- Invalid or unsupported diagram file: Medium impact, medium probability.
- AI review result is inaccurate or incomplete: High impact, medium probability.
- Workflow fails during extraction or report generation: Medium impact, medium probability.
- CORS errors between CloudFront and API Gateway: Medium impact, medium probability.
- CloudFront cache still serves old frontend build: Low to medium impact, medium probability.
- AWS WAF rules block valid requests if configured too strictly: Medium impact, low to medium probability.
- IAM permissions are too broad or too restricted: High impact, medium probability.
- Amazon Bedrock cost increases due to high token usage: Medium impact, low probability.
- Bedrock incorrectly identifies AWS services or connections from the diagram: High impact, medium probability.
- Generated architecture JSON is incomplete or not in the correct structure: High impact, medium probability.
- Cost estimation is inaccurate due to missing usage assumptions: Medium impact, medium probability.
- Amazon Bedrock cost increases due to AI calls in both the JSON generation step and the final review step: Medium impact, medium probability.
- CloudWatch Metric Filter captures normal log keywords incorrectly and creates false alarms: Low to medium impact, medium probability.

#### Mitigation Strategies

- Validate file type and file size before storing uploads.
- Use structured prompts for Amazon Bedrock.
- Use Step Functions retry and error handling.
- Store workflow status in DynamoDB.
- Configure CORS correctly for localhost and the CloudFront domain.
- Create CloudFront invalidation after each frontend deployment.
- Configure AWS WAF managed rule groups in Count mode before switching to Block mode.
- Monitor AWS WAF sampled requests to check whether valid requests match rules.
- Apply least privilege IAM policies.
- Monitor Lambda, API Gateway, Step Functions, Bedrock, and WAF usage through CloudWatch.
- Use AWS Budgets to monitor expected monthly costs.
- Standardize prompts for the diagram-to-JSON extraction step.
- Validate architecture JSON before sending it to Lambda Cost Tool.
- Use clear default usage assumptions for cost estimation.
- Limit diagram size and the amount of data sent to Bedrock.
- Separate the architecture JSON generation prompt and the final architecture review prompt to better control output.
- Tune CloudWatch Metric Filters to detect only real errors such as `ERROR`, `Exception`, `Traceback`, `AccessDenied`, `ValidationException`, and `Timeout` instead of overly broad keywords.

#### Contingency Plan

- If AI analysis fails, mark the review status as failed in DynamoDB.
- If PDF generation fails, keep the AI review result and retry the PDF generation process.
- If CloudWatch or SNS alerts do not work, check alarm actions, SNS subscription, and the confirmed status of the email.
- If AWS WAF blocks valid requests, switch the rule to Count mode, check sampled requests, and adjust the rule action.
- If CloudFront still serves old files, create an invalidation and redeploy the frontend.
- If Bedrock usage becomes expensive, limit input size or reduce the number of review requests.

---

### 8. Expected Outcomes

#### Technical Improvements

The project is expected to produce a working AWS serverless platform that allows users to upload architecture diagrams and receive AI-powered architecture review results.

Expected technical outcomes include:

- React web application running on S3 and distributed through CloudFront.
- Secure frontend distribution using CloudFront OAC and private S3 bucket access.
- AWS WAF associated with the CloudFront distribution to strengthen frontend protection.
- API Gateway routes for upload and review data retrieval.
- Lambda Upload Service for file validation, S3 storage, and DynamoDB metadata.
- S3 Input Bucket for uploaded architecture diagrams.
- DynamoDB Review Database for review history and status tracking.
- EventBridge and Step Functions workflow for automated review processing.
- Lambda AI Analyzer reading uploaded diagrams and sending them to Amazon Bedrock.
- Amazon Bedrock generating architecture JSON from uploaded diagrams.
- Lambda Cost Tool estimating monthly costs based on architecture JSON.
- Amazon Bedrock performing the final AI architecture review based on architecture JSON and cost estimation results.
- PDF report generation including architecture review, cost estimation, cost explanation, and optimization recommendations.
- PDF reports stored in the S3 Report Bucket.
- Frontend displaying review results and supporting PDF report download.
- CloudWatch monitoring, metric filters, and alarms for important components.
- Amazon SNS sending email alerts when CloudWatch Alarm detects system errors.
- IAM least privilege security for Lambda functions and AWS services.

#### Long-Term Value

The platform can be reused as a learning tool for AWS architecture design and Well-Architected review practice. The system can also be extended into a more advanced cloud architecture assessment platform with features such as user authentication, multi-user review history, architecture scoring, advanced report templates, and support for more diagram formats.

The project provides practical experience with important AWS services, including AWS WAF, CloudFront, S3, API Gateway, Lambda, DynamoDB, EventBridge, Step Functions, Amazon Bedrock, SNS, CloudWatch, and IAM.