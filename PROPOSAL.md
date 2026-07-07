---

title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
--------------------

# Stock Analysis and Alert System

## An AWS Serverless & AI Agent Solution for Traders

### 1. Executive Summary

This project proposes building a **Stock Alerts System** - an automated stock analysis, reasoning, and alerting platform based on AWS Serverless architecture and AI Agent capabilities through Amazon Bedrock. The system supports traders by monitoring market data, collecting financial information automatically, calculating technical indicators, generating AI-assisted analysis, and producing recommendations that can be reviewed before being sent to end users.

The key design principle is to separate two types of tasks:

* **Quantitative calculation** is handled by Python code running on AWS Lambda to keep indicators such as RSI, MACD, and moving averages consistent.
* **Natural language reasoning and recommendation explanation** are handled by an AI Agent on Amazon Bedrock using normalized data as context.

This approach reduces the risk of AI hallucination or incorrect math, while preserving a **Human-in-the-loop** workflow where traders approve recommendations before sending them to clients.

### My Role in the Project

In this project, my role is **QA Tester**.
My responsibility is to support the team by testing the main functions of the AWS Stock Analyzer project, including frontend interface behavior, backend API responses, input validation, and frontend-backend integration.

The testing work is performed manually and focuses on checking whether the main features work as expected.
I also record testing observations and provide feedback to support the final project review.

---

### 2. Problem Statement

#### Current Problem

Traders often need to process a large amount of market information: price, volume, technical indicators, sector movement, and macroeconomic news. Manual analysis is time-consuming, may miss important opportunities, and can be affected by emotion.

Using a general AI model directly for financial analysis also introduces risk. A language model may calculate indicators incorrectly, over-explain weak signals, or create unsupported information. In finance, these mistakes can lead to risky investment decisions.

#### Proposed Solution

The system solves this problem through a separated pipeline:

* Ingestion Lambda retrieves market data from Yahoo Finance or a finance API.
* Processing Lambda calculates technical indicators using Python logic.
* Raw data is stored in Amazon S3, while analysis results and recommendations are stored in DynamoDB.
* Amazon Bedrock receives the processed data as context and generates reasoning-based analysis.
* Traders sign in to the dashboard through Amazon Cognito, review the AI reasoning, edit if needed, and approve the final content before sending it to clients.

#### Benefits and ROI

The solution significantly reduces the time traders spend collecting and summarizing data, standardizes the analysis process, and allows multiple tickers to be monitored in parallel. With a serverless architecture, the system follows a pay-as-you-go cost model, making it suitable for learning, prototyping, and gradual scaling.

---

### 3. Solution Architecture

The system is designed as a serverless architecture divided into independent layers: frontend, ingestion, processing, AI analysis, database, and monitoring.

![Stock Alerts System Architecture](./images/2-Proposal/stock-alerts-architecture.png)

#### AWS Services Used

* **AWS WAF**: filters malicious requests before they enter the system.
* **Amazon CloudFront**: distributes the frontend with low latency.
* **Amazon S3**: stores the static frontend and raw JSON market data.
* **Amazon API Gateway**: provides REST APIs for the dashboard and manual sync.
* **Amazon Cognito**: authenticates users and issues JWT tokens for traders.
* **AWS Lambda**: performs ingestion, data processing, AI calls, and persistence.
* **Amazon EventBridge**: triggers ingestion based on market schedules.
* **Amazon SQS**: decouples ingestion and processing, supports retry, and prevents overload.
* **Amazon SQS DLQ**: stores failed messages for later investigation.
* **Amazon DynamoDB**: stores recommendations, time-series data, and analysis results.
* **AWS KMS**: encrypts sensitive data stored in DynamoDB.
* **AWS Systems Manager Parameter Store**: stores API keys and secure configuration.
* **Amazon Bedrock**: provides the AI Agent using Claude for reasoning and recommendations.
* **Amazon CloudWatch**: collects logs, metrics, and triggers operational alarms.
* **Amazon SNS**: sends operational alerts or important notifications to traders/admins.

#### Main Workflow

1. Admin or trader sends a request to the dashboard.
2. AWS WAF checks the request and forwards clean traffic through CloudFront.
3. The static frontend is loaded from S3.
4. The dashboard calls API Gateway and sends a JWT token issued by Cognito.
5. API Gateway verifies the token and triggers Lambda when manual sync is needed.
6. EventBridge triggers ingestion on a market schedule.
7. Ingestion Lambda calls the finance API, retrieves secrets from Parameter Store, and stores raw JSON in S3.
8. S3 Event Notification sends a message to SQS.
9. Processing Lambda reads from SQS, calculates technical indicators, and calls Bedrock for analysis.
10. Recommendations are stored in DynamoDB with data encrypted by KMS.
11. CloudWatch records metrics/logs and triggers SNS if an error or threshold breach occurs.
12. The trader reviews the recommendation on the dashboard before sending it to clients.

---

### 4. Technical Implementation

#### Frontend & Dashboard

The frontend is built with JavaScript and deployed as a static website on Amazon S3, distributed through CloudFront. The dashboard allows traders to sign in, view ticker lists, monitor indicators, read AI reasoning, and approve content before delivery.

#### Ingestion & Math Engine

EventBridge triggers Lambda based on market schedules. Lambda retrieves data from Yahoo Finance or another finance API and stores raw JSON in S3. When a new object appears, S3 sends an event to SQS so Processing Lambda can process data in a controlled way.

Processing Lambda uses Python to calculate indicators such as RSI, MACD, and moving averages. Performing these calculations in code ensures that quantitative results do not depend on AI reasoning.

#### AI Analytics Engine

Amazon Bedrock receives normalized technical data as context. The prompt is designed to force AI output into a structured JSON format containing:

* Action recommendation.
* Confidence score.
* Reasoning trace.
* Risk factors to consider.

Signals with low confidence scores can be filtered out or placed into manual review.

#### Database & Security

DynamoDB stores analysis results using a time-series query pattern. A suggested key design is:

```text
PK: TICKER#<Stock_Symbol>
SK: TIMESTAMP#<YYYYMMDD-HHMMSS>
```

Data is encrypted with AWS KMS. API keys and sensitive configuration are stored in AWS Systems Manager Parameter Store instead of being hard-coded in source code.

#### Observability & Alerting

CloudWatch collects logs and metrics from Lambda, SQS, and API Gateway. If ingestion fails, messages exceed retry limits, or AI cost starts rising unexpectedly, the system can send alerts through SNS so traders/admins can respond.

---

### 5. Testing Scope

As a QA Tester, I focus on manually testing the main functions of the AWS Stock Analyzer project.
The testing scope includes frontend testing, backend API testing, input validation, and frontend-backend integration testing.

#### Frontend Testing

The frontend testing focuses on checking whether the user interface works correctly and is easy to understand.

Main testing items include:

* Page layout and display.
* Input fields.
* Buttons and user actions.
* Result display section.
* Basic error message display.

#### Backend API Testing

The backend testing focuses on checking whether the backend can receive requests and return expected responses.

Main testing items include:

* Valid stock symbol input.
* Empty input.
* Invalid stock symbol input.
* API response format.
* Basic error handling behavior.

#### Integration Testing

Integration testing focuses on checking whether the frontend and backend work together correctly.

Main testing items include:

* Frontend sends requests to backend successfully.
* Backend returns responses to frontend.
* Frontend displays returned results correctly.
* Error cases are shown clearly to users.

#### Final Testing Review

At the final stage, I review the main testing results, summarize testing observations, and prepare notes to support the final report.

---

### 6. Timeline & Milestones

* **May**: Study AWS and identify the services used in the project.
* **June**: Design the architecture, draw the system diagram, divide responsibilities, and define data flows.
* **July**: Implement the project, test ingestion, processing, dashboard, alerts, and fix issues.

Project worklog timeline:

| Week            | Main Activity                                                        |
| --------------- | -------------------------------------------------------------------- |
| Week 1 - Week 2 | Learned AWS basic concepts and practiced simple guided tasks         |
| Week 3 - Week 5 | Mainly watched YouTube tutorials and read AWS learning materials     |
| Week 6          | Reviewed AWS fundamentals and prepared project direction             |
| Week 7          | Started AWS Stock Analyzer project and took QA Tester role           |
| Week 8          | Prepared basic test cases and testing direction                      |
| Week 9          | Tested backend API behavior                                          |
| Week 10         | Tested frontend interface                                            |
| Week 11         | Tested frontend-backend integration                                  |
| Week 12         | Completed planned testing tasks and summarized final testing results |

---

### 7. Budget Estimation

The infrastructure cost is optimized to use AWS Free Tier during the learning and testing phase.

| Service                 | Estimated Usage                      | Expected Cost             |
| ----------------------- | ------------------------------------ | ------------------------- |
| Amazon EventBridge      | Around 8,800 events/month            | $0.00 within Free Tier    |
| AWS Lambda              | Around 17,600 invocations/month      | $0.00 within Free Tier    |
| Amazon SQS              | Around 17,600 messages/month         | $0.00 within Free Tier    |
| Amazon S3               | Around 200 MB JSON/month             | $0.00 within Free Tier    |
| Amazon DynamoDB         | Around 2,200 records/month           | $0.00 within Free Tier    |
| Amazon API Gateway      | Around 5,000 requests/month          | $0.00 within Free Tier    |
| Amazon Cognito          | 1 trader/admin account               | $0.00 within Free Tier    |
| AWS SSM Parameter Store | Standard parameters                  | $0.00                     |
| Amazon CloudWatch       | Logs and metrics under 5 GB          | $0.00 within Free Tier    |
| Amazon Bedrock          | Around 2,200 analysis requests/month | Variable token-based cost |

For the dev/test phase using Claude 3.5 Sonnet, assuming each request uses about 1,500 input tokens, 2,200 requests equals around 3.3 million input tokens. The AI cost is estimated at about **$19.80/month** when the system is actively tested. The remaining serverless infrastructure is designed to stay close to **$0/month** within Free Tier limits.

---

### 8. Risk Assessment

#### Risk 1: AI cost overrun caused by token bursts

When the market is highly volatile, more tickers may require analysis, causing more Bedrock calls than expected.

**Mitigation:** set daily Bedrock invocation quotas, prioritize ticker lists, use Claude 3.5 Sonnet for dev/test, and configure AWS Budgets alerts.

#### Risk 2: Processing congestion when many files arrive in S3

If ingestion creates many files at the same time, Lambda may be triggered too aggressively.

**Mitigation:** use SQS as an intermediate buffer, limit Lambda concurrency, retry in a controlled way, and send failed messages to DLQ.

#### Risk 3: Finance API connection failure or request throttling

Data sources such as yfinance/Yahoo Finance may fail temporarily or throttle requests.

**Mitigation:** retry twice, cache the latest available data in S3, send failed messages to DLQ, and trigger CloudWatch Alarm.

#### Risk 4: Inaccurate AI recommendation

AI may over-interpret signals or produce reasoning that does not fully match the data.

**Mitigation:** separate quantitative calculations from AI, enforce structured JSON output, use confidence scoring, and require trader approval before sending recommendations to clients.

#### Risk 5: Frontend or backend bugs during testing

The application may have UI issues, API response errors, or incorrect handling of invalid input.

**Mitigation:** perform manual testing, record testing observations, and provide feedback to the team for improvement.

---

### 9. Expected Outcomes

#### Technical Improvements

The project is expected to build a fault-tolerant asynchronous workflow including scheduled ingestion, SQS-based processing, Bedrock analysis, DynamoDB persistence, and CloudWatch/SNS alerting.

#### Practical Value

Traders gain an AI assistant that helps summarize market data, explain signals, and generate reviewable recommendations. The solution saves analysis time, improves consistency in decision-making, and preserves safety through the Human-in-the-loop approval process.

#### Personal Learning Outcomes

Through this project, I can understand how AWS services are combined in a cloud-based application.
As a QA Tester, I also learn how to prepare basic test cases, test frontend and backend behavior, check integration flow, and record testing results.

---

### 10. Conclusion

The Stock Analysis and Alert System is a practical AWS cloud computing project that applies serverless services and AI-assisted analysis to support stock monitoring and recommendation review.
The system architecture includes frontend hosting, API communication, data ingestion, asynchronous processing, AI analysis, database storage, security, monitoring, and alerting.

My role in this project is QA Tester.
I focus on manual testing tasks such as frontend testing, backend API testing, input validation testing, and integration testing.
Although my testing work is at a basic manual level, it helps support the final review of the AWS Stock Analyzer project and improves my understanding of software testing in an AWS cloud project.
