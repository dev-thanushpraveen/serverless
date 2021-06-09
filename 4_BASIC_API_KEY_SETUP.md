# Basic API Key setup for our functions

_Step 1_: Create apikeys under provider below iamRoleStatements in serverless.yaml file

```
provider:
  apiKeys:
    - MyFirstApiKey
```

_Step 2_: To enable api key to the function edit functions serverless.yaml as follow:

```
functions:
  getUsers:
    handler: lambdas/endpoints/getUser.handler
    events:
      - http:
          private: true
```

_Step 3_: Deploy and when calling the private api from postman
 - use authentication as api key and set header X-API-KEY:<API_KEY(which you will get when deployed in terminal)>

# Advanced API_KEY setup

_Step 1_: We can set limit to the api call as follow:

```
provider:
  usagePlans:
    quota:
      limit: 1000
      period: MONTH
```
- To set how many request can perform per second add below config:
 ```
 usagePlans:
     throttle:
      rateLimit: 5
      burstLimit: 20
  ```
  
  > This can call api 5 times per second and can burst upto 20times for short time

_Step 2_: If you have different level of usage plans we can use different setup as follow:
```
  apiKeys:
    - free
      - myFreeApiKey
    - paid
      - myPaidApiKey
  usagePlans:
    - free:
        quota:
          limit: 1000
          period: MONTH
        throttle:
          rateLimit: 5
          burstLimit: 20
    - paid:
        quota:
          limit: 10000
          period: MONTH
        throttle:
          rateLimit: 50
          burstLimit: 200
```
