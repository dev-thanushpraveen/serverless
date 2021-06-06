# serverless setup

_step 1_: npm i -g serverless

_step 2_: create iam on aws

_step 3_: serverless config credentials --provider aws --key <ACCESS_KEY> --secret <SECRET_KEY> --profile <NAME>

# create serverless project

_step 1_: serverless create --template aws-nodejs --path <PATHNAME>

_step 2_: open code explorer , serverless.yaml, under provider add profile:<IAM_NAME>

_step 3_: In terminal run "sls deploy"

# Deploy an s3 bucket

_Step 1_: create the below config on serverless.yaml

```
resources:
  Resources:
    DemoBucketUpload:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: <BUCKET_NAME>
```

Note: Bucket name should be unique

# Upload data

_Step 1_: npm i --save serverless-s3-sync

_Step 2_: Create new folder for **UploadData**

- Create new file Uploadme.txt

_Step 3_: add plugin to serverless.yaml

```
plugins:
  - serverless-s3-sync
custom:
  s3Sync:
    - bucketName: <BUCKET_NAME>
      localDir: UploadData
```

# Add Common Functions like API_Responses.js

_Step 1_: Import Common files for repo

# Create an API lambda

_Step 1_: Create Folder lambdas

- Create File as function name. ex. getUser.js
- Create the lambda function
  i.e.

  ```
  const Responses = require("./common/API_Responses");

  exports.handler = async (event) => {
    console.log("event", event);

    if (!event.pathParameters || !event.pathParameters.ID) {
      // failed
      return Responses._400({ message: "ID is missing" });
    }

    let ID = event.pathParameters.ID;

    if (data[ID]) {
      return Responses._200(data[ID]);
      // return data
    }

    // failed as ID not in the data
    return Responses._400({ message: "ID not found" });
  };
  ```

_Step 2_: Add functions to serverless.js

- ```
  functions:
    getUsers:
      handler: <LAMBDA_FUNC_PATH>.handler
      events:
      - http:
          path: get-user/{ID}
          method: GET
          cors: true
  ```

- **example-> handler: lambdas/getUser.handler**

# Add Serverless Webpack to the project

_Step 1_:

```
npm i --save serverless-webpack

```

_Step 2_:

```
npm i --save webpack
```

_Step 3_: Add serverless-webpack to the plugins in serverless.yaml

```
    plugins:
      - serverless-webpack
```

To pack individual lambda as package, add

```
    package:
      individually: true
```

_Step 4_: Create webpack.config.js file

```
module.exports = {
  target: 'node',
  mode:'production'
};
```

Note: Mode can be changed as production or none, none will not compress the code and shows original file in lambda

_Step 5_: To deploy Only on function run

```
sls deploy -f <FUNCTION_NAME>
```
