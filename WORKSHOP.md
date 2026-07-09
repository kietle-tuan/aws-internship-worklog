# WORKSHOP

## AWS Stock Analyzer Project Workshop

---

## 1. Overview

This workshop presents the implementation and testing process of the final project **AWS Stock Analyzer**.

The system was built to support stock analysis using an AWS Serverless architecture. Stock market data is collected from Yahoo Finance, processed through AWS services, analyzed with technical indicators, and displayed on a dashboard for trader review.

The system does not send AI recommendations directly to customers immediately. Instead, the analysis result is reviewed by a trader before any recommendation is sent to customers by email.

In this workshop, my role was **QA Tester**. I focused on understanding the project flow, checking the deployed application, observing backend processing, reviewing logs, and recording testing results.

---

## 2. Project Overview

The **AWS Stock Analyzer** system is designed to help analyze stock data more efficiently.

The main purposes of the system are:

- Collect stock market data from Yahoo Finance.
- Store raw stock data for checking and reprocessing.
- Calculate technical indicators such as RSI, MACD, MA, and Volume.
- Use Amazon Bedrock to support AI-based analysis.
- Store analysis results in DynamoDB.
- Display stock analysis results on a dashboard.
- Allow traders to review and approve recommendations before sending them to customers.

The system helps reduce manual analysis time and allows traders to monitor multiple stock symbols more easily.

---

## 3. Target Users

The target users of the system include:

- Individual investors who want stock analysis support.
- Beginners who need help understanding technical signals.
- Traders or financial consultants who need a dashboard to review stock recommendations before sending them to customers.

The system is not a professional trading platform. It is an internship project used to practice AWS Cloud Computing, serverless architecture, and project testing.

---

## 4. System Architecture

The project uses an AWS Serverless architecture.

The main services include:

| Service | Purpose |
|---|---|
| Yahoo Finance API | Provides stock market data |
| Amazon S3 | Stores raw stock data and frontend static files |
| Amazon SQS | Handles asynchronous message processing |
| AWS Lambda | Runs ingestion and processing logic |
| Amazon DynamoDB | Stores final stock analysis results |
| AWS KMS | Encrypts stored data |
| Amazon CloudFront | Distributes the frontend dashboard |
| AWS WAF | Protects the web application |
| Amazon Cognito | Provides user authentication |
| Amazon API Gateway | Connects frontend requests to backend Lambda functions |
| Amazon Bedrock | Supports AI-based stock analysis |
| Amazon SES | Sends email notifications if needed |
| Amazon CloudWatch | Provides logs for checking system behavior |

The system was designed to keep the architecture simple, scalable, and suitable for a student project.

---

## 5. Why This Architecture Was Chosen

The architecture was selected based on four main criteria:

### 5.1 Low Cost

Services such as Lambda, S3, SQS, DynamoDB, API Gateway, CloudFront, and SES are suitable for pay-as-you-go usage.

The system does not require a server running continuously, so the initial cost is lower than using an EC2 server 24/7.

For example:

- Lambda only runs when requests are triggered.
- SQS charges based on message usage.
- DynamoDB charges based on storage and read/write usage.
- S3 charges based on storage usage.

This makes the architecture suitable for an academic project.

---

### 5.2 Serverless Simplicity

The project uses serverless services to reduce system operation work.

Instead of manually configuring servers, installing runtime environments, managing scaling, updating operating systems, and monitoring server resources, the project uses AWS managed and serverless components.

The team could focus more on the main project logic:

- Collecting stock data.
- Calculating technical indicators.
- Calling Amazon Bedrock.
- Displaying results on the dashboard.
- Building the trader review flow.

---

### 5.3 Managed Services

Most components in the system are AWS Managed Services.

This means AWS handles much of the infrastructure operation.

Examples:

- Amazon Bedrock supports AI analysis without deploying a machine learning model manually.
- DynamoDB stores NoSQL data without managing a database server.
- Amazon SQS handles message queue processing.
- Amazon SES supports email sending.
- AWS KMS manages data encryption keys.

This reduces operational risk and makes the project easier to manage.

---

### 5.4 Scalability

The architecture can scale by separating the system into different components.

For example:

- API Gateway and Lambda can handle multiple requests.
- SQS can queue messages when many stock analysis requests are submitted.
- Processing Lambda can process queued messages.
- DynamoDB and S3 can scale with data volume.
- CloudFront can serve the frontend faster to users.
- AWS WAF can protect the application from common web attacks.

This design makes the system easier to expand in the future.

---

## 6. Prerequisites and Deployment Preparation

Before testing the application, the system required several AWS components to be prepared.

### 6.1 AWS Account

An AWS account was used to access the AWS Management Console and create the required cloud services.

The project was deployed in the AWS Singapore region.

---

### 6.2 Backend Deployment

The backend deployment included the following services.

#### Amazon S3 for Raw Data

An S3 bucket was created to store raw stock data from Yahoo Finance.

Bucket example: `production-stock-raw-data-finance`

Amazon S3 was used because raw data needs to be stored for checking, reprocessing, and separating the data collection step from the analysis step.

---

#### Amazon SQS

Amazon SQS was used as a message queue between Ingestion Lambda and Processing Lambda.

Queue examples:

- `stock-processing-queue`
- `stock-processing-dlq`

SQS helps the system process tasks asynchronously. When new stock data is available, a message is sent to SQS, and Processing Lambda handles it later.

This helps avoid overloading the system and separates data ingestion from data processing.

---

#### AWS Lambda

Two main Lambda functions were used:

- `production-stock-ingestion`
- `production-stock-processing`

The Lambda functions used Node.js 22.x.

The **Ingestion Lambda** receives stock analysis requests, calls Yahoo Finance, normalizes stock data, stores raw data in S3, and sends a message to SQS.

The **Processing Lambda** reads messages from SQS, retrieves stock data from S3, calculates technical indicators such as RSI, MACD, MA20, MA50, and Volume, then sends processed data to Amazon Bedrock for AI analysis.

---

#### IAM Role and Policy

IAM permissions were required so Lambda could access AWS services such as:

- S3
- SQS
- DynamoDB
- SES
- Amazon Bedrock

The IAM role allowed Lambda functions to interact with the required resources.

---

### 6.3 Database Deployment

#### AWS KMS

AWS KMS was used to encrypt stored data.

KMS helps protect analysis data at rest and increases system security.

---

#### Amazon DynamoDB

DynamoDB was used to store final stock analysis results.

Example table: `Stock_reports_1`

The table used a partition key and sort key structure.

DynamoDB stores information such as:

- Stock symbol
- Timeframe
- Technical indicators
- AI recommendation
- Confidence score
- Analysis reason
- Trader approval status

The dashboard queries DynamoDB to display analysis results to users.

---

### 6.4 Frontend Deployment

#### Amazon S3 for Frontend Hosting

Another S3 bucket was used to host the frontend static files.

Example: `stock-frontend-finance`

HTML, CSS, JavaScript, and other static files were stored in S3.

This approach reduces operational cost because no web server needs to run continuously.

---

#### Amazon CloudFront

Amazon CloudFront was used as a CDN to distribute the frontend dashboard.

CloudFront improves access speed by serving static content through edge locations near users.

It also supports HTTPS and can integrate with AWS WAF.

The deployed application link:

https://d3k9qj467czrvg.cloudfront.net/

---

#### AWS WAF

AWS WAF was used in front of CloudFront to help protect the web application.

WAF can filter and block common web attacks such as:

- SQL Injection
- Cross-Site Scripting
- Bot requests
- Abnormal request patterns

This helps improve the security of the frontend application.

---

### 6.5 User Authentication

Amazon Cognito was used for user authentication.

Only valid users, such as traders or administrators, can log in to the dashboard, view stock analysis reports, and approve or reject recommendations.

---

### 6.6 API Gateway

Amazon API Gateway was used as the communication layer between frontend and backend.

When users interact with the dashboard, the frontend sends requests to API Gateway. API Gateway then forwards the requests to the related Lambda functions.

---

### 6.7 Amazon Bedrock

Amazon Bedrock was used to support AI-based stock analysis.

In this project, Bedrock does not directly collect stock data or news. It receives calculated technical indicators such as RSI, MACD, MA, and stock price data, then generates analysis and recommendations such as:

- Strong Buy
- Strong Sell
- Watch

Bedrock also helps provide confidence scores and analysis reasons.

---

## 7. Testing Process

### 7.1 Login Testing

The deployed application was accessed through the CloudFront URL.

The login page used Amazon Cognito authentication.

Since an internal user account was already available, login could be completed successfully.

Checked items:

- Application opened normally.
- Cognito login button displayed.
- Internal user could access the dashboard.
- Dashboard loaded after login.

---

### 7.2 Dashboard Checking

After login, the dashboard displayed available stock data.

At first, the dashboard showed only the stock symbol **FPT**.

The dashboard was checked to confirm that stock data could be displayed with related analysis information.

---

### 7.3 Stock Analysis Request

To test another stock symbol, I selected the analysis page and chose the stock symbol **VNM**.

Testing steps:

1. Clicked the **Analysis** section.
2. Selected stock symbol **VNM**.
3. Selected the timeframe.
4. Clicked the **Analyze** button.
5. Observed the system response.

The request was successfully submitted.

---

### 7.4 Backend Processing Flow

After submitting the stock analysis request, the backend processing flow was checked.

The expected flow was:

1. Ingestion Lambda calls Yahoo Finance API.
2. Raw stock data is stored in S3.
3. A message is sent to SQS.
4. Processing Lambda is triggered.
5. Processing Lambda reads stock data and calculates technical indicators.
6. Final result is stored in DynamoDB.
7. Dashboard displays updated analysis results.

The SQS queue showed zero remaining messages after processing, which means the message had already been handled by Processing Lambda.

---

### 7.5 DynamoDB Checking

After Processing Lambda completed the task, the analysis result was stored in DynamoDB.

The DynamoDB table showed stock records such as:

- FPT
- VNM
- VIC

This confirmed that the backend processing flow was able to store analysis results successfully.

---

### 7.6 Dashboard Result Checking

The dashboard displayed stock analysis results successfully.

The results included technical indicators such as:

- MA20
- MACD
- RSI
- Confidence score
- AI status

The dashboard showed multiple stock symbols because several stock analysis requests had been submitted.

---

### 7.7 CloudWatch Log Checking

CloudWatch logs were checked for the Processing Lambda function.

The logs showed that the processing flow had run according to the expected logic.

This confirmed that the main backend flow was working from ingestion to processing and storage.

---

## 8. Issue Found During Testing

During testing, an issue occurred in Amazon Bedrock.

The error message was related to token quota:

`Too many tokens per day, please trying again.`

Because of this issue, Bedrock could not complete the AI analysis normally.

The system used fallback mock data with a confidence score of **68%**. This is why the stock results showed confidence scores lower than the expected threshold of 75%.

The Bedrock issue affected the AI analysis and email recommendation flow. Because Bedrock could not generate the final AI report, the system could not fully complete the intended recommendation process.

---

## 9. Suggested Fix

The suggested solution is to request a quota increase for the Amazon Bedrock model being used.

The model mentioned in the project was `Claude 3.5 v2`.

Increasing the token quota would allow Bedrock to process more analysis requests and generate reports for the dashboard.

After the quota issue is resolved, Bedrock should be able to analyze the technical indicators and return a more complete AI-generated recommendation.

---

## 10. Trader Review Flow

The system was designed so that traders review the final recommendation before sending it to customers.

If there is a strong buy or strong sell signal, the trader will check and approve the report before the recommendation is sent by email.

This design helps reduce risk because investment-related recommendations should not be sent automatically without human review.

---

## 11. My Role as QA Tester

In this workshop, my role was **QA Tester**.

My main tasks included:

- Reviewing the system overview.
- Understanding the AWS serverless architecture.
- Checking the deployed CloudFront application.
- Logging in through Cognito.
- Testing stock analysis requests.
- Observing S3, SQS, Lambda, DynamoDB, and CloudWatch behavior.
- Checking dashboard output.
- Recording the Bedrock quota issue.
- Reporting testing observations to the team.

I did not independently deploy the whole AWS infrastructure. My contribution focused on testing, checking system behavior, and recording issues.

---

## 12. Future Improvements

The project can be improved in several ways:

1. Add more stock data sources besides Yahoo Finance.
2. Improve AI analysis by adding more technical indicators such as Bollinger Bands, Stochastic, ADX, and ATR.
3. Add real-time alerts when stock prices or indicators reach important thresholds.
4. Improve user role management for Admin, Trader, and Customer.
5. Add more security controls such as CloudWatch Alarm, CloudTrail, WAF rules, IP restriction, and KMS encryption.
6. Optimize cost by reducing unnecessary Bedrock calls, caching stock data, and adjusting Lambda memory and timeout.
7. Improve the dashboard with charts, filters, approval status, confidence score, and analysis history.
8. Add CI/CD using GitHub Actions or AWS CodePipeline.
9. Configure DLQ and retry policy for SQS.
10. Add a mechanism to evaluate recommendation accuracy over time.

---

## 13. Conclusion

This workshop helped me understand the AWS Stock Analyzer project more clearly.

The system used AWS serverless services to collect stock data, process technical indicators, generate AI-supported recommendations, and display results on a dashboard.

From the QA Tester perspective, I tested the deployed application, checked the backend processing flow, reviewed logs, and recorded the Bedrock quota issue.

Although the project still had limitations, especially the Bedrock token quota issue, the workshop gave me practical experience in checking and testing a cloud-based application built on AWS.
