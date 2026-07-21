---
title : "Prerequisite"
date : 2026-07-03 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

#### AWS Region Used

In this workshop, the main AWS Region used for backend services is:

```text
Asia Pacific (Singapore) - ap-southeast-1
```

For Amazon CloudFront and AWS WAF associated with the CloudFront distribution, the scope is:

```text
CloudFront (Global)
```

---

#### Required Tools

Before starting this workshop, make sure the following tools are installed and configured on the local machine:

+ An AWS account is required to create and manage the services used in this project.

+ Node.js and npm are required to run, build, and deploy the React frontend application.

+ The frontend application is built using React and Vite.

+ A web browser is required to access the website deployed through CloudFront and test the application.

---

#### Local Project Directory

The project source code is stored in the following local directory:

```text
D:\Learning\AWS\ai-aws-architecture-reviewer
```

This directory contains the React frontend source code, configuration files, and build output.

The main frontend build command is:

```text
npm run build
```

After the build process is completed, the production files are generated in the `dist` directory.

---

#### IAM Permissions

The IAM user or role used in this workshop needs permissions to create, configure, deploy, test, and clean up the AWS services used by the **AI AWS Architecture Reviewer** project.

The AWS services required include:

+ Amazon S3,
+ Amazon CloudFront,
+ AWS WAF,
+ Amazon API Gateway,
+ AWS Lambda,
+ Amazon DynamoDB,
+ Amazon EventBridge,
+ AWS Step Functions,
+ Amazon Bedrock,
+ Amazon SNS,
+ Amazon CloudWatch,
+ IAM.

AWS WAF is used to protect the frontend CloudFront distribution. Therefore, the deployment account needs permissions to view, create, or manage Web ACLs, protection packs, and managed rule groups if WAF configuration is performed in this workshop.

---

#### Frontend Hosting and Protection Resources

The frontend application is deployed using Amazon S3, Amazon CloudFront, and protected by AWS WAF.

The S3 frontend bucket is used to store the React production build files:

```text
ai-aws-reviewer-frontend-tiersteam
```

The CloudFront distribution is used to distribute the website to users.

The deployed CloudFront domain is:

```text
https://d9353ayez9zar.cloudfront.net
```

The frontend deployment command is:

```text
aws s3 sync dist s3://ai-aws-reviewer-frontend-tiersteam --delete
```

After uploading a new build, a CloudFront invalidation needs to be created:

```text
/*
```

This ensures that CloudFront serves the latest version of the React application.

AWS WAF is associated with the CloudFront distribution to add a protection layer for the frontend. The WAF protection pack or Web ACL is used to apply managed rule groups to monitor, detect, and reduce potentially malicious requests before they reach the application.

The managed rule groups used include:

```text
AWSManagedRulesAmazonIpReputationList
AWSManagedRulesCommonRuleSet
AWSManagedRulesKnownBadInputsRuleSet
```

During the testing phase, the rules can be run in **Count mode** to monitor requests before switching to actual blocking mode.

---

#### Backend API Resources

The backend API is exposed publicly through Amazon API Gateway.

The API Gateway endpoint is:

```text
https://031hqksomd.execute-api.ap-southeast-1.amazonaws.com
```

The required API routes are:

```text
POST /upload
GET /reviews
GET /reviews/{reviewId}
GET /reviews/{reviewId}/status
```

These routes are integrated with the Lambda Upload Service.

---

#### Storage Resources

The system uses Amazon S3 and Amazon DynamoDB to store data.

The S3 Input Bucket stores uploaded architecture diagrams:

```text
ai-aws-reviewer-input-bucket-tiersteam
```

Uploaded diagrams are stored using the following key structure:

```text
uploads/{reviewId}/{fileName}
```

The DynamoDB table stores review metadata and review history:

```text
AIArchitectureReviews
```

The partition key is:

```text
reviewId
```

---

#### Lambda Upload Service

The upload backend is implemented using AWS Lambda.

The Lambda function name is:

```text
ai-aws-reviewer-upload-service
```

This function handles the following tasks:

+ Validate upload requests
+ Validate file type and file size
+ Generate a review ID
+ Upload architecture diagrams to the S3 Input Bucket
+ Write review metadata to DynamoDB
+ Return review information to the frontend
+ Retrieve review history and review status

The required Lambda environment variables are:

```text
INPUT_BUCKET = ai-aws-reviewer-input-bucket-tiersteam
TABLE_NAME = AIArchitectureReviews
MAX_FILE_SIZE_MB = 5
ALLOWED_ORIGINS = http://localhost:5173,https://d9353ayez9zar.cloudfront.net
```

---

#### Test Files

Prepare one or more architecture diagram files to test the upload feature.

Example directory:

```text
D:\Learning\AWS\test-files
```

Example files:

```text
architecture-1.png
architecture-2.jpg
```

To test file upload using PowerShell, run the following command:

```text
curl.exe -X POST "https://031hqksomd.execute-api.ap-southeast-1.amazonaws.com/upload" -F "file=@D:\Learning\AWS\test-files\architecture-1.png"
```

If the upload is successful, the API returns a response containing a `reviewId`.

Example response:

```text
{
  "reviewId": "REV-5E57628D",
  "status": "uploaded",
  "fileName": "architecture-1.png",
  "fileType": "image/png",
  "fileSize": 17755,
  "message": "Upload successful"
}
```