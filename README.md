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
