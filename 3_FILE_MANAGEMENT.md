# Adding Files to S3

_Step 1_: Create a common file S3.js and paste the code.

```
const AWS = require('aws-sdk');

const s3Client = new AWS.S3();

const S3 = {
    async get(fileName, bucket) {
        const params = {
            Bucket: bucket,
            Key: fileName,
        };

        let data = await s3Client.getObject(params).promise();

        if (!data) {
            throw Error(`Failed to get file ${fileName}, from ${bucket}`);
        }

        if (fileName.slice(fileName.length - 4, fileName.length) == 'json') {
            data = data.Body.toString();
        }
        return data;
    },
    async write(data, fileName, bucket) {
        const params = {
            Bucket: bucket,
            Body: JSON.stringify(data),
            Key: fileName,
        };

        const newData = await s3Client.putObject(params).promise();

        if (!newData) {
            throw Error('there was an error writing the file');
        }

        return newData;
    },
};

module.exports = S3;
```

_Step 2_: Create a endpoint to upload a file:

```
const Responses = require("../common/API_Responses");
const S3 = require("../common/S3");

const bucket = process.env.bucketName;

exports.handler = async (event) => {
  console.log("event", event);

  if (!event.pathParameters || !event.pathParameters.fileName) {
    // failed without an ID
    return Responses._400({ message: "missing the fileName from the path" });
  }

  let fileName = event.pathParameters.fileName;
  const data = JSON.parse(event.body);

  const newUser = await S3.write(data, fileName, bucket).catch((err) => {
    console.log("error in s3 write", err);
    return null;
  });

  if (!newUser) {
    return Responses._400({ message: "Failed to write data by fileName" });
  }

  return Responses._200({ newUser });
};
```

_Step 3_: Create a endpoint to get a file:
```
const Responses = require("../common/API_Responses");
const S3 = require("../common/S3");

const bucket = process.env.bucketName;

exports.handler = async (event) => {
  console.log("event", event);

  if (!event.pathParameters || !event.pathParameters.fileName) {
    // failed without an fileName
    return Responses._400({ message: "missing the fileName from the path" });
  }

  let fileName = event.pathParameters.fileName;

  const file = await S3.get(fileName, bucket).catch((err) => {
    console.log("error in S3 get", err);
    return null;
  });

  if (!file) {
    return Responses._400({ message: "Failed to read data by filename" });
  }

  return Responses._200({ file });
};

```

_Step 4_: Add iamroleStatement for s3 in serverless.yaml file:

```
provider:
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:*
        - s3:*
      Resource: "*"
```

_Step 5_: Add bucketName to the environment in serverless.yaml

```
environment:
    tableName: ${self:custom.tableName}
    bucketName: ${self:custom.bucketName}
```

_Step 6_: Set bucketName in custom and replace all bucketName with **${self:custom.bucketName}**

```
custom:
  bucketName: myserverlessprojectbuckets3upload
```

_Step 7_: Add functions for upload and get in functions of serverless.yaml file:

```
createFile:
        handler: lambdas/endpoints/createFile.handler
        events:
            - http:
                  path: create-file/{fileName}
                  method: POST
                  cors: true
    getFile:
        handler: lambdas/endpoints/getFile.handler
        events:
            - http:
                  path: get-file/{fileName}
                  method: GET
                  cors: true
```
