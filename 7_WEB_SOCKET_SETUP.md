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

