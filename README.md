# Chaining-AWS-Lambdas

## A Simple Example of One AWS Lambda Calling Another One
The following is a simple example of  2 AWS lambdas. The first one is called scheduler_lambda, and the second worker_lambda. scheduler_lambda calls worker lambda and passes in a username and a password as payload.

Here's the code for `scheduler_lambda`:

```python
import boto3

def lambda_handler(event, context):
    # Define the payload data (username and password)
    payload = {
        "username": "your_username",
        "password": "your_password"
    }

    # Define the AWS Lambda client
    lambda_client = boto3.client('lambda')

    # Invoke the worker_lambda with the payload
    response = lambda_client.invoke(
        FunctionName='worker_lambda',
        InvocationType='RequestResponse',  # Use 'Event' for asynchronous invocation
        Payload=json.dumps(payload)
    )

    # Parse and return the response from worker_lambda
    response_payload = json.loads(response['Payload'].read())
    
    return {
        "statusCode": 200,
        "body": "scheduler_lambda successfully invoked worker_lambda",
        "worker_lambda_response": response_payload
    }
```

Here's the code for `worker_lambda`:

```python
import json

def lambda_handler(event, context):
    # Extract the username and password from the incoming event
    body = json.loads(event['body'])
    username = body['username']
    password = body['password']

    # Perform some work using the username and password (e.g., authenticate)
    # Replace this with your actual worker Lambda logic

    # For demonstration purposes, we'll simply return a success message
    response = {
        "message": "Worker Lambda successfully processed the request",
        "username": username,
        "authenticated": True  # You can set this based on your logic
    }

    return {
        "statusCode": 200,
        "body": json.dumps(response)
    }
```
<span style="color:red;">**Note:**</span>  Remember to replace `"your_username"` and `"your_password"` with the actual values you want to pass from `scheduler_lambda` to `worker_lambda`.
