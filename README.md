# CRUD API with AWS Lambda, DynamoDB, and API Gateway

## Overview
This guide will walk you through the steps to build a CRUD API using AWS Lambda, DynamoDB, and API Gateway. You will learn how to create, read, update, and delete records in a DynamoDB table using HTTP methods via API Gateway.

## Prerequisites
- An AWS account
- Basic knowledge of AWS services like Lambda, API Gateway, and DynamoDB
- AWS CLI installed and configured
- Node.js installed (for writing Lambda functions)

## Steps
### 1. Create a DynamoDB Table
- Open AWS Management Console.
- Navigate to DynamoDB and click on **Create Table**.
- Define a table name (e.g., `UsersTable`).
- Set **Primary Key** as `userId` (String).
- Choose on-demand capacity mode.
- Click **Create Table**.

### 2. Create an AWS Lambda Function
- Navigate to AWS Lambda.
- Click **Create Function** and select **Author from scratch**.
- Provide a function name (e.g., `CRUDLambdaFunction`).
- Choose **Runtime** as Node.js.
- Set up an execution role with permissions to access DynamoDB.
- Click **Create Function**.

### 3. Write Lambda Code for CRUD Operations
- Go to the **Code** tab of the Lambda function.
- Replace the default code with the following:

```javascript
const AWS = require("aws-sdk");
const dynamoDb = new AWS.DynamoDB.DocumentClient();
const TABLE_NAME = "UsersTable";

exports.handler = async (event) => {
  let response;
  switch (event.httpMethod) {
    case "GET":
      response = await getUser(event);
      break;
    case "POST":
      response = await createUser(event);
      break;
    case "PUT":
      response = await updateUser(event);
      break;
    case "DELETE":
      response = await deleteUser(event);
      break;
    default:
      response = { statusCode: 400, body: JSON.stringify("Invalid Request") };
  }
  return response;
};

async function getUser(event) {
  const params = { TableName: TABLE_NAME, Key: { userId: event.pathParameters.id } };
  const data = await dynamoDb.get(params).promise();
  return { statusCode: 200, body: JSON.stringify(data.Item) };
}

async function createUser(event) {
  const item = JSON.parse(event.body);
  const params = { TableName: TABLE_NAME, Item: item };
  await dynamoDb.put(params).promise();
  return { statusCode: 201, body: JSON.stringify(item) };
}

async function updateUser(event) {
  const item = JSON.parse(event.body);
  const params = { TableName: TABLE_NAME, Item: item };
  await dynamoDb.put(params).promise();
  return { statusCode: 200, body: JSON.stringify(item) };
}

async function deleteUser(event) {
  const params = { TableName: TABLE_NAME, Key: { userId: event.pathParameters.id } };
  await dynamoDb.delete(params).promise();
  return { statusCode: 200, body: JSON.stringify("Deleted successfully") };
}
```

### 4. Deploy API Gateway
- Navigate to API Gateway and create a **REST API**.
- Create a new resource and enable **proxy integration**.
- Connect it to the Lambda function.
- Deploy the API and note down the **Invoke URL**.

### 5. Test the API
Use Postman or CURL to test your API:
```sh
# Create User
curl -X POST https://your-api-id.execute-api.region.amazonaws.com/dev \
-H "Content-Type: application/json" \
-d '{"userId": "123", "name": "John Doe"}'

# Get User
curl -X GET https://your-api-id.execute-api.region.amazonaws.com/dev/123

# Update User
curl -X PUT https://your-api-id.execute-api.region.amazonaws.com/dev \
-H "Content-Type: application/json" \
-d '{"userId": "123", "name": "Jane Doe"}'

# Delete User
curl -X DELETE https://your-api-id.execute-api.region.amazonaws.com/dev/123
```

## Results
Students will be able to:
- Define an AWS Lambda function.
- Create and configure a DynamoDB table.
- Develop a serverless API triggered by HTTP requests from the client side.
- Handle API calls to perform CRUD operations on a database.
- Implement basic HTTP methods like GET, PUT, and DELETE.

## Observations
During this lab, students should focus on:
- Configurations for AWS Lambda functions.
- Setting up and managing an AWS DynamoDB table.
- Integrating and managing API Gateway routes and integrations.

## References
- [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [AWS API Gateway Documentation](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)
- [AWS DynamoDB Documentation](https://docs.aws.amazon.com/dynamodb/latest/developerguide/Introduction.html)

This comprehensive guide ensures students understand the end-to-end process of creating a scalable, serverless backend using AWS services.

