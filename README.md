# AWS Serverless Portfolio Website

A personal portfolio website built and deployed entirely on AWS cloud infrastructure. Features a live visitor counter, HTTPS delivery via a global CDN, and a fully automated CI/CD pipeline that deploys on every GitHub push.

**Live URL:** https://d3c6fuu7it96ic.cloudfront.net

---

## What This Project Demonstrates

- Hosting a static website on AWS S3 with public access policies
- Delivering content securely over HTTPS using CloudFront CDN
- Building a serverless backend with API Gateway, Lambda, and DynamoDB
- Connecting a frontend to a backend API using JavaScript fetch
- Automating deployment using CodePipeline triggered by GitHub pushes

---

## Architecture

Six AWS services working together as one system.

### Visitor Flow
1. Visitor opens the CloudFront URL in their browser
2. CloudFront fetches the HTML/CSS files from S3 and serves them
3. The page loads and JavaScript silently calls the API Gateway endpoint
4. API Gateway triggers the Lambda function
5. Lambda reads the current count from DynamoDB, increments it, and saves it back
6. The updated count is returned to the browser and displayed on the page

### CI/CD Flow
1. Code changes are pushed to the GitHub repository
2. CodePipeline detects the push automatically
3. Pipeline pulls the latest files and deploys them to S3
4. CloudFront serves the updated site — no manual upload needed

---

## AWS Services Used

| Service | Role |
|---|---|
| Amazon S3 | Stores and hosts the static HTML/CSS files |
| CloudFront | Global CDN — delivers the site over HTTPS |
| API Gateway | HTTP endpoint that the browser calls for the visitor count |
| AWS Lambda | Python function that reads and writes the visitor count |
| DynamoDB | Serverless NoSQL database storing the visitor count |
| CodePipeline | CI/CD pipeline — auto-deploys on every GitHub push |

---

## Lambda Function (Python)

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('visitor-count')

def lambda_handler(event, context):
    response = table.update_item(
        Key={'id': 'visitors'},
        UpdateExpression='ADD visitor_count :inc',
        ExpressionAttributeValues={':inc': 1},
        ReturnValues='UPDATED_NEW'
    )
    count = int(response['Attributes']['visitor_count'])
    return {
        'statusCode': 200,
        'headers': {
            'Access-Control-Allow-Origin': '*',
            'Content-Type': 'application/json'
        },
        'body': json.dumps({'count': count})
    }
```

---

## Project Structure

```
aws-serverless-portfolio/
├── index.html       # Portfolio page with embedded CSS and JavaScript
└── README.md        # This file
```

---

## Setup Overview

1. Create an S3 bucket with static website hosting enabled
2. Create a CloudFront distribution pointing to the S3 bucket
3. Create a DynamoDB table with partition key `id` (String)
4. Deploy the Lambda function with IAM permissions to read/write DynamoDB
5. Create an API Gateway HTTP API with a `GET /count` route linked to Lambda
6. Enable CORS on the API Gateway
7. Connect CodePipeline to this GitHub repository with S3 as the deploy target
8. Push code — pipeline handles all future deployments automatically

---

## Cost

All six services operate within the AWS permanent free tier at typical student traffic levels. Realistic monthly cost: **₹0**.

---

*Built by Aakash Ramadurai B — B.E. Mechanical Engineering, St. Joseph's College of Engineering, Chennai*
