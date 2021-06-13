# Create a new serverless setup
_Step 1_: Create a new serverless setup with common files same as the boilerplate and reset serverless.yaml file as follow

```
# Welcome to Serverless!

service: myWebSocketServerless
# app and org for use with dashboard.serverless.com
#app: your-app-name
#org: your-org-name

# You can pin your service to only deploy with a specific Serverless version
# Check out our docs for more details
frameworkVersion: '2'

provider:
  name: aws
  runtime: nodejs12.x
  lambdaHashingVersion: 20201221
  profile: serverlessUser

functions:


```

_Step 2_: Create a folder for websockets under lambdas folder and create some files as connect.js default.js disconnect.js message.js
 In terminal
 > cd lambdas/

 > mkdir websockets
 
 > cd websockets/
 
 > touch connect.js default.js disconnect.js message.js

Connect.js
```
const Responses = require("../common/API_Responses");
const Dynamo = require("../common/Dynamo");

const tableName = process.env.tableName;

exports.handler = async (event) => {
  console.log("event", event);

  const {
    connectionId: connectionID,
    domainName,
    stage,
  } = event.requestContext;

  const data = {
    ID: connectionID,
    date: Date.now(),
    messages: [],
    domainName,
    stage,
  };

  await Dynamo.write(data, tableName);

  return Responses._200({ message: "connected" });
};


```

disconnect.js
```
const Responses = require("../common/API_Responses");
const Dynamo = require("../common/Dynamo");

const tableName = process.env.tableName;

exports.handler = async (event) => {
  console.log("event", event);

  const { connectionId: connectionID } = event.requestContext;

  await Dynamo.delete(connectionID, tableName);

  return Responses._200({ message: "disconnected" });
};


```

default.js
```
const Responses = require("../common/API_Responses");

exports.handler = async (event) => {
  console.log("event", event);

  return Responses._200({ message: "default" });
};

```

message.js
```
const Responses = require("../common/API_Responses");
const Dynamo = require("../common/Dynamo");
const WebSocket = require("../common/websocketMessage");

const tableName = process.env.tableName;

exports.handler = async (event) => {
  console.log("event", event);

  const { connectionId: connectionID } = event.requestContext;

  const body = JSON.parse(event.body);

  try {
    const record = await Dynamo.get(connectionID, tableName);
    const { messages, domainName, stage } = record;

    messages.push(body.message);

    const data = {
      ...record,
      messages,
    };

    await Dynamo.write(data, tableName);

    await WebSocket.send({
      domainName,
      stage,
      connectionID,
      message: "This is a reply to your message",
    });
    console.log("sent message");

    return Responses._200({ message: "got a message" });
  } catch (error) {
    return Responses._400({ message: "message could not be received" });
  }

  return Responses._200({ message: "got a message" });
};


```

_Step 3_: Update the serverless.yaml

```
environment:
  tableName: ${self:custom.tableName}
iam:
    role:
      statements:
        - Effect: Allow
          Action:
              - dynamodb:*
          Resource: "*"
custom:
  tableName: WebsocketUsers
functions:
  websocket-connect:
    handler: lambdas/websockets/connect.handler
    events:
      - websocket:
          route: $connect # $ refers to inbuild route
  websocket-disconnect:
    handler: lambdas/websockets/disconnect.handler
    events:
      - websocket:
          route: $disconnect
  websocket-default:
    handler: lambdas/websockets/default.handler
    events:
      - websocket:
          route: $default
  websocket-message:
    handler: lambdas/websockets/message.handler
    events:
      - websocket:
          route: message
resources:
    Resources:
      WebsocketUserTable:
        Type: AWS::DynamoDB::Table
        Properties:
          TableName: ${self:custom.tableName}
          AttributeDefinitions:
            - AttributeName: ID
              AttributeType: S
          KeySchema:
            - AttributeName: ID
              KeyType: HASH
          BillingMode: PAY_PER_REQUEST
```

_Step 4_: Deploy and check in **http://websocket.org/echo.html**
