# POSTED BLOGS

## Summary of Reviewed Learning Materials

This section summarizes several learning materials and tutorial topics that I reviewed during the AWS Cloud Computing internship.
These materials supported my understanding of AWS basic services, serverless architecture, and testing activities for the AWS Stock Analyzer project.

The purpose of this section is not to claim advanced implementation experience, but to record the learning resources and concepts that helped me prepare for my role as a QA Tester.

---

## Blog 1 - Introduction to AWS Cloud Computing

### Source

* YouTube AWS learning playlist
* AWS Study Group materials

### Summary

This topic introduced the basic concepts of cloud computing and Amazon Web Services.
I reviewed why cloud computing is used, how AWS provides different cloud services, and how users can access these services through the AWS Management Console.

The main services reviewed in this topic included IAM, EC2, S3, and basic cloud networking.
IAM is used to manage users, permissions, and access control.
EC2 provides virtual servers for running applications.
S3 is used for object storage, such as storing files, static website resources, or raw data.
These services helped me understand the basic structure of a cloud-based system.

### What I Learned

After reviewing this topic, I understood the role of AWS as a cloud computing platform at a basic level.
I also became more familiar with the AWS Management Console and the purpose of common AWS services.
This knowledge was useful when reading the project architecture of the AWS Stock Analyzer system.

---

## Blog 2 - Understanding AWS Serverless Architecture

### Source

* AWS Study Group materials
* YouTube tutorials about AWS Lambda, API Gateway, S3, SQS, and DynamoDB

### Summary

This topic focused on serverless architecture in AWS.
In a serverless system, developers can build application features without directly managing physical or virtual servers.
AWS services such as Lambda, API Gateway, S3, SQS, DynamoDB, and EventBridge can work together to create a flexible application workflow.

In the AWS Stock Analyzer project, serverless architecture is used to support data ingestion, backend processing, asynchronous messaging, data storage, and monitoring.
For example, API Gateway can receive requests from the frontend, Lambda can process logic, S3 can store raw JSON data, SQS can help manage processing messages, and DynamoDB can store analysis results.

### What I Learned

After reviewing this topic, I understood how different AWS services can be connected in a cloud-based application.
I learned that serverless architecture can reduce the need to manage servers directly and can make the system easier to scale during development.
This helped me understand the design of the AWS Stock Analyzer project more clearly.

---

## Blog 3 - Manual Testing for Cloud-Based Applications

### Source

* Online QA testing materials
* Project discussion and testing practice in the AWS Stock Analyzer project

### Summary

This topic focused on basic manual testing activities for a cloud-based application.
As a QA Tester, my responsibility is to check whether the main features of the application work as expected and to record testing observations.

For the AWS Stock Analyzer project, testing activities included checking the frontend interface, backend API behavior, input validation, and frontend-backend integration.
Frontend testing focused on page display, buttons, input fields, and result sections.
Backend testing focused on checking API responses for valid and invalid stock symbol inputs.
Integration testing focused on checking whether the frontend could send requests to the backend and display returned results correctly.

### What I Learned

After reviewing this topic and practicing testing in the project, I understood more clearly the role of a QA Tester in a software project.
I learned how to test basic application flows manually, observe errors, and record testing results.
Although the testing was not advanced automation testing, it helped me support the team during the final project review.

---

## Overall Reflection

The reviewed blogs and learning materials helped me build a basic foundation in AWS Cloud Computing and software testing.
The AWS learning materials helped me understand cloud services and serverless architecture.
The QA testing materials helped me apply manual testing activities to the AWS Stock Analyzer project.

Through these materials, I was able to better understand the project architecture and complete my assigned role as QA Tester at a basic level.
