# serverless setup

step 1: npm i -g serverless

step 2: create iam on aws

step 3: serverless config credentials --provider aws --key <ACCESS_KEY> --secret <SECRET_KEY> --profile <NAME>
  
# create serverless project
  
step 1: serverless create --template aws-nodejs --path <PATHNAME>
  
step 2: open code explorer , serverless.yaml, under provider add profile:<IAM_NAME>
  
step 3: In terminal run "sls deploy"
