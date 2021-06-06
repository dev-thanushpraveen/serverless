# serverless setup

*step 1*: npm i -g serverless

*step 2*: create iam on aws

*step 3*: serverless config credentials --provider aws --key <ACCESS_KEY> --secret <SECRET_KEY> --profile <NAME>
  
# create serverless project
  
*step 1*: serverless create --template aws-nodejs --path <PATHNAME>
  
*step 2*: open code explorer , serverless.yaml, under provider add profile:<IAM_NAME>
  
*step 3*: In terminal run "sls deploy"

# Deploy an s3 bucket 
  
*Step 1*: create the below config on serverless.yaml 
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

*Step 1*: npm i --save serverless-s3-sync
  
*Step 2*: Create new folder for **UploadData**
  * Create new file Uploadme.txt
  
*Step 3*: add plugin to serverless.yaml
  ```
  plugins:
    - serverless-s3-sync
  custom:
    s3Sync:
      - bucketName: <BUCKET_NAME>
        localDir: UploadData
