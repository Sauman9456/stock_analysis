# Stock Analysis Project

This project automates the process of identifying the best-performing stocks over 3 months using a multi-agent workflow system. It can run locally or leverage AWS services for scalability and automation.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Project Structure](#project-structure)
3. [Prerequisites](#prerequisites)
4. [Installation](#installation)
5. [Configuration](#configuration)
6. [Running the Application](#running-the-application)
   - [Running Locally](#running-locally)
   - [Running with AWS Services](#running-with-aws-services)
7. [AWS Resources Setup](#aws-resources-setup)
8. [Agent Details](#agent-details)
9. [Notes](#notes)
10. [Troubleshooting](#Troubleshooting)

---

## Project Overview

The application consists of multiple agents that perform specific tasks:

- **Stock Search Agent**: Retrieves top-performing stock tickers.
- **Data Fetching Agent**: Fetches historical stock data.
- **Data Saving Agent**: Saves stock data to storage.
- **Data Analysis Agent**: Analyzes data to find the best-performing stock.
- **User Notification Agent**: Sends notifications with the analysis results.

The workflow is orchestrated through a main script, allowing for seamless execution without manual intervention.

---

## Project Structure

```
stock_analysis/
├── agents/
│   ├── data_analysis_agent.py
│   ├── data_fetching_agent.py
│   ├── data_saving_agent.py
│   ├── stock_search_agent.py
│   └── user_notification_agent.py
├── config.py
├── main.py
├── requirements.txt
└── README.md
```

- **agents/**: Contains all the agent scripts.
- **config.py**: Configuration settings for the application.
- **main.py**: The main script that runs the workflow.
- **requirements.txt**: Lists Python dependencies.
- **README.md**: This file.

---

## Prerequisites

- **Python 3.8+**
- **AWS Account** (if running with AWS services)
- **AWS CLI** configured with your AWS credentials (for AWS deployment)
- **AWS Resources** (S3 bucket, DynamoDB table, SES setup) if running with AWS services.

---

## Installation

1. **Clone the Repository**

   ```bash
   git clone https://github.com/yourusername/stock_analysis_project.git
   cd stock_analysis_project
   ```

2. **Install Dependencies**

   It's recommended to use a virtual environment:

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use venv\Scripts\activate
   ```

   Install the required packages:

   ```bash
   pip install -r requirements.txt
   ```

---

## Configuration

All configuration settings are located in the `config.py` file.

**config.py**

```python
import os

# Set to 'local' or 'aws'
ENVIRONMENT = 'local'

# AWS Configuration
AWS_REGION = 'your-aws-region'
S3_BUCKET_NAME = 'your-s3-bucket'
DYNAMODB_TABLE_NAME = 'your-dynamodb-table'
SES_SENDER_EMAIL = 'sender@example.com'  # Must be verified in SES
SES_RECIPIENT_EMAIL = 'recipient@example.com'

# Other Configurations
DATA_FOLDER = 'data'  # Folder to store data locally
```

### Steps to Configure

1. **Select Environment**

   - Set `ENVIRONMENT` to `'local'` to run the application locally.
   - Set `ENVIRONMENT` to `'aws'` to use AWS services.

2. **AWS Configuration** (if running with AWS)

   - **AWS_REGION**: Specify the AWS region (e.g., `'us-east-1'`).
   - **S3_BUCKET_NAME**: The name of your S3 bucket.
   - **DYNAMODB_TABLE_NAME**: The name of your DynamoDB table.
   - **SES_SENDER_EMAIL**: The email address verified in SES for sending emails.
   - **SES_RECIPIENT_EMAIL**: The recipient's email address.

3. **Local Configuration** (if running locally)

   - **DATA_FOLDER**: Directory where data will be stored locally.

---

## Running the Application

### Running Locally

1. **Ensure Local Configuration**

   - Set `ENVIRONMENT = 'local'` in `config.py`.
   - Ensure `DATA_FOLDER` is set to a valid directory.

2. **Run the Application**

   ```bash
   python main.py
   ```

3. **Expected Output**

   The application will:

   - Fetch top-performing stock tickers.
   - Download historical stock data.
   - Save data locally in the `data/` folder.
   - Analyze the data to find the best-performing stock.
   - Print the notification email content to the console.

### Running with AWS Services

1. **Ensure AWS Configuration**

   - Set `ENVIRONMENT = 'aws'` in `config.py`.
   - Fill in all AWS configuration variables with your AWS resource details.

2. **AWS Credentials**

   - Ensure your AWS CLI is configured with the necessary permissions.
     ```bash
     aws configure
     ```
   - Alternatively, set environment variables for AWS access keys.

3. **Run the Application**

   ```bash
   python main.py
   ```

4. **Expected Output**

   The application will:

   - Fetch top-performing stock tickers.
   - Download historical stock data.
   - Save data to the specified S3 bucket.
   - Analyze the data and store the report in DynamoDB with TTL.
   - Send an email notification via AWS SES to the recipient email.

---

## AWS Resources Setup

If you're running the application with AWS services, ensure the following resources are set up:

### 1. S3 Bucket

- Create an S3 bucket or use an existing one.
- Update `S3_BUCKET_NAME` in `config.py`.

### 2. DynamoDB Table

- Create a DynamoDB table with a primary key named `timestamp` (String).
- Enable TTL on the `ttl` attribute.
- Update `DYNAMODB_TABLE_NAME` in `config.py`.

### 3. AWS SES (Simple Email Service)

- Verify the sender email address (`SES_SENDER_EMAIL`) in AWS SES.
- Optionally, verify the recipient email address (`SES_RECIPIENT_EMAIL`) if your SES is in sandbox mode.
- Ensure SES is available in your selected AWS region.

### 4. AWS Permissions

- The AWS user or role must have permission to access S3, DynamoDB, and SES.
- Attach the following AWS-managed policies or create custom policies:
  - AmazonS3FullAccess or appropriate S3 permissions.
  - AmazonDynamoDBFullAccess or appropriate DynamoDB permissions.
  - AmazonSESFullAccess or appropriate SES permissions.

---

## Agent Details

### Stock Search Agent

- **File**: `agents/stock_search_agent.py`
- **Function**: Retrieves a list of top-performing stock tickers.
- **Method**: Fetches S&P 500 companies from Wikipedia and selects a sample of tickers.

### Data Fetching Agent

- **File**: `agents/data_fetching_agent.py`
- **Function**: Downloads historical stock data.
- **Method**: Uses `yfinance` to download data for the past 90 days.

### Data Saving Agent

- **File**: `agents/data_saving_agent.py`
- **Function**: Saves stock data to storage.
- **Method**:
  - **Local**: Saves data as CSV files in the `data/` directory.
  - **AWS**: Uploads CSV files to the specified S3 bucket.

### Data Analysis Agent

- **File**: `agents/data_analysis_agent.py`
- **Function**: Analyzes stock data to find the best performer.
- **Method**:
  - Calculates percentage gain for each stock.
  - Sorts stocks by gain.
  - Saves the report locally or to DynamoDB.

### User Notification Agent

- **File**: `agents/user_notification_agent.py`
- **Function**: Sends notification with the analysis results.
- **Method**:
  - **Local**: Prints the notification content to the console.
  - **AWS**: Sends an email via AWS SES.

---

## Notes

- **Data Folder**: If running locally, ensure the `DATA_FOLDER` exists or will be created by the application.
- **Error Handling**: The application includes basic error handling; ensure to check logs for any issues.
- **Time to Live (TTL)**: In DynamoDB, reports will automatically expire after 7 days (configurable in the code).

---

## Troubleshooting

- **Common Issues When Running Locally**:
  - Missing dependencies: Ensure all packages in `requirements.txt` are installed.
  - File permissions: Ensure the script has permission to create and write to the `data/` directory.

- **Common Issues When Running with AWS**:
  - AWS permissions: Ensure your AWS credentials have the necessary permissions.
  - Resource names: Double-check that `S3_BUCKET_NAME` and `DYNAMODB_TABLE_NAME` are correct.
  - SES in Sandbox Mode: If SES is in sandbox mode, you need to verify both sender and recipient emails.

---

## Contact

For any questions or issues, please contact [onfbsm@gmail.com](mailto:onfbsm@gmail.com).

---
