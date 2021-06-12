# Sending a Mail

_Step 1_: Create a new endpoint sendEmail.js
```
const Responses = require("../common/API_Responses");
const AWS = require("aws-sdk");
const SES = new AWS.SES();

exports.handler = async (event) => {
  console.log("event", event);
  const { to, from, subject, body } = JSON.parse(event.body);

  if (!to || !from || !subject || !body) {
    Responses._400({ message: "To, from, subject, body all are required" });
  }

  const params = {
    Destination: {
      ToAddresses: [to],
    },
    Message: {
      Body: {
        Text: { Data: body },
      },
      Subject: {
        Data: subject,
      },
    },
    Source: {
      from,
    },
  };

  try {
    await SES.sendEmail(params).promise();
    Responses._200({});
  } catch (e) {
    Responses._400({ message: "Email Failed to send" });
  }
};
```

_Step 2_: Add the sendEmail function to serverless.yaml

```
functions:
    sendEmail:
    handler: lambdas/endpoints/sendEmail.handler
    events:
      - http:
          path: send-email
          method: POST
          cors: true
```

_Step 3_: Add SES is iam role Statement in serverless.yaml
```
  iam:
    role:
      statements:
        - Effect: Allow
          Action:
              - dynamodb:*
              - s3:*
              - ses:*
          Resource: "*"
```

_Step 4_: Active Email and verify from SES on aws console website
