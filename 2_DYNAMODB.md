# Create a serverless database

_Step 1_: Add resources and custom on serverless.yaml as below

```
custom:
    tableName: <TABLE_NAME>
resources:
  Resources:
    MyDynamoDbTable:
      Type: AWS::DynamoDB::Table
      Properties:
        TableName: ${self:custom.tableName}
        AttributeDefinitions:
          - AttributeName: ID
            AttributeType: S
        KeySchema:
          - AttributeName: ID
            KeyType: Hash
        BillingMode: PAY_PER_REQUEST
```

_Step 2_: Add below code to Dynamo.js to common file

```
const AWS = require("aws-sdk");

const documentClient = new AWS.DynamoDB.DocumentClient();

const Dynamo = {
  async get(ID, TableName) {
    const params = {
      TableName,
      Key: {
        ID,
      },
    };

    const data = await documentClient.get(params).promise();

    if (!data || !data.Item) {
      throw Error(
        `There was an error fetching the data for ID of ${ID} from ${TableName}`
      );
    }
    console.log(data);

    return data.Item;
  },
};
module.exports = Dynamo;

```

_Step 3_: Get the data from table as follow:

```
 let ID = event.pathParameters.ID;

  const user = await Dynamo.get(ID, tableName).catch((err) => {
    console.log("error in Dynamo Get", err);
    return null;
  });
```

_Step 4_: Set environment on serverless.yaml under provider

```
provider:
  environment:
    tableName: ${self:custom.tableName}
```

_Step 5_: Give permission for lambda to access Dynamo db as follow

```
provider:
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:*
      Resource: "*"
```
