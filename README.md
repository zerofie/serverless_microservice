# serverless-microservice

In this project , I made a serverless microservice using API Gateway , Lambda and DynamoDB

I made a resource DynamoDBManager and defined POST method on it . The method is backed by a lambda function which is invoked by the lambda function 

The method on the DynamoDBManager supports the following DynamoDB operations 
- create , update ,delete
- read
- scan
& other operations like echo and ping
<br><br>

**SETUP** 
<br><br>
**Create IAM role** 
<br><br>
Create role with trusted entity as lambda and role name lambda-apigateway-role and custom polucy 

  ```
{
"Version": "2012-10-17",
"Statement": [
{
  "Sid": "Stmt1428341300017",
  "Action": [
    "dynamodb:DeleteItem",
    "dynamodb:GetItem",
    "dynamodb:PutItem",
    "dynamodb:Query",
    "dynamodb:Scan",
    "dynamodb:UpdateItem"
  ],
  "Effect": "Allow",
  "Resource": "*"
},
{
  "Sid": "",
  "Resource": "*",
  "Action": [
    "logs:CreateLogGroup",
    "logs:CreateLogStream",
    "logs:PutLogEvents"
  ],
  "Effect": "Allow"
}
]
}
```
**Create Lambda function** 


<img width="1263" alt="1" src="https://github.com/japn3et/serverless-microservice/assets/140784801/ad55c35a-1c96-456a-869c-5bf5f01725bc">
<br><br>


replace boilerplate code with this 
<br><br>

```
from __future__ import print_function

import boto3
import json

print('Loading function')


def lambda_handler(event, context):
    '''Provide an event that contains the following keys:

      - operation: one of the operations in the operations dict below
      - tableName: required for operations that interact with DynamoDB
      - payload: a parameter to pass to the operation being performed
    '''
    #print("Received event: " + json.dumps(event, indent=2))

    operation = event['operation']

    if 'tableName' in event:
        dynamo = boto3.resource('dynamodb').Table(event['tableName'])

    operations = {
        'create': lambda x: dynamo.put_item(**x),
        'read': lambda x: dynamo.get_item(**x),
        'update': lambda x: dynamo.update_item(**x),
        'delete': lambda x: dynamo.delete_item(**x),
        'list': lambda x: dynamo.scan(**x),
        'echo': lambda x: x,
        'ping': lambda x: 'pong'
    }

    if operation in operations:
        return operations[operation](event.get('payload'))
    else:
        raise ValueError('Unrecognized operation "{}"'.format(operation))
```
<br><br>
<img width="1185" alt="2" src="https://github.com/japn3et/serverless-microservice/assets/140784801/db058f56-457b-49cf-babb-b0f9bbde80bf">
<br><br>
Now configure a test event with this JSON code 
<br><br>
```
{
    "operation": "echo",
    "payload": {
        "somekey1": "somevalue1",
        "somekey2": "somevalue2"
    }
}
```
<br><br>
<img width="800" alt="3" src="https://github.com/japn3et/serverless-microservice/assets/140784801/619b4da8-582c-4346-9639-c3c804954f3f">
<br><br>
Now click on test to see output 
<br><br>
<img width="1135" alt="test run" src="https://github.com/japn3et/serverless-microservice/assets/140784801/d4017b34-b994-458f-8913-7c80f50ff0a1">
<br><br>
**Creating DynamoDB table**
<br><br>
<img width="745" alt="5" src="https://github.com/japn3et/serverless-microservice/assets/140784801/0cf28eef-34c7-4ce2-bfad-29c37a3027b5">
<br><br>
<img width="1004" alt="6" src="https://github.com/japn3et/serverless-microservice/assets/140784801/36d75cd1-ac35-48ec-9228-a41238b7c4a2">
<br><br>
**Create API** 
<br><br>
<img width="318" alt="7" src="https://github.com/japn3et/serverless-microservice/assets/140784801/dcad43eb-4243-4043-b34e-9ad67b9e6c7e">
<br><br>
<img width="425" alt="8" src="https://github.com/japn3et/serverless-microservice/assets/140784801/a111d5a4-86e4-4fdd-b65c-2844fa0a359f">
<br><br>
Click on actions and then create resource 
<br><br>
<img width="607" alt="9" src="https://github.com/japn3et/serverless-microservice/assets/140784801/9a3e219f-f14a-4dc7-b06c-3c7fa63e486c">
<br><br>
<img width="950" alt="10" src="https://github.com/japn3et/serverless-microservice/assets/140784801/ed894c4c-3850-4f68-a8c4-1d670b728f12">
<br><br>
Create a post method by creating action again and click on create method 
<br><br>
<img width="617" alt="11" src="https://github.com/japn3et/serverless-microservice/assets/140784801/34c75f4b-8cea-462f-9f3e-7af6b7eea225">
<br><br>
<img width="1003" alt="12" src="https://github.com/japn3et/serverless-microservice/assets/140784801/c8618011-4c54-4c3e-930f-b1e4e744d574">
<br><br>
**Deploy the API** 
<br><br>
click actions then deploy api
<br><br>
<img width="291" alt="rtges" src="https://github.com/japn3et/serverless-microservice/assets/140784801/599ad6f3-5362-4e3a-8f55-9705ab91bde0">
<br><br>
<img width="419" alt="4" src="https://github.com/japn3et/serverless-microservice/assets/140784801/7172be6b-62f1-472a-addc-d9c2eef9d859">
<br><br>
<img width="1254" alt="image" src="https://github.com/japn3et/serverless-microservice/assets/140784801/83432b45-4d1e-4eac-800a-782a06e70d09">
<br><br>
Now open postman to execute API from local machine 
Select post method and paste url from API invoke URL  and select raw option 
and enter this JSON code to crete item in DynamDB database 
<br><br>
```
{
    "operation": "create",
    "tableName": "lambda-apigateway",
    "payload": {
        "Item": {
            "id": "1234ABCD",
            "number": 5
        }
    }
}
```
<br><br>
<img width="565" alt="image" src="https://github.com/japn3et/serverless-microservice/assets/140784801/b1ef28a5-0994-4a0f-a82e-4b63b164fbc5">
<br><br>
This should give output as HTTPStatusCode 200
<br><br>
check this by going to dynamo db itesm summary , it should be 0
<br><br>
<img width="781" alt="gvbhjn" src="https://github.com/japn3et/serverless-microservice/assets/140784801/9ef796d9-4cf7-4ccb-950c-9c420c149dc7">
<br><br>
Click on "get live item count"
<br><br>
<img width="778" alt="hngbvfd" src="https://github.com/japn3et/serverless-microservice/assets/140784801/1149c857-e996-4fd5-bbad-91b936bfc972">
<br><br>
It is showing 2 items here because i ran it twice 
<br><br>
No we have made a serverless microservice using API Gateway , Lambda and DynamoDB
<br><br>
**Cleanup** 
<br><br>
Delete the table "lambda-apigateway", the lambda function "LambdaFunctionOverHttps", API "DynamoDBOperations"
