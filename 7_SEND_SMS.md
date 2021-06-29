# Sending SMS

_Step 1_: Create endpoint sendSMS.js

```
const Responses = require('../common/API_Responses');
const AWS = require('aws-sdk');

const SNS = new AWS.SNS({ apiVersion: '2010-03-31' });

exports.handler = async event => {
    console.log('event', event);

    const body = JSON.parse(event.body);

    if (!body || !body.phoneNumber || !body.message) {
        return Responses._400({ message: 'missing phone number or messsage from the body' });
    }

    const AttributeParams = {
        attributes: {
            DefaultSMSType: 'Promotional',
        },
    };

    const messageParams = {
        Message: body.message,
        PhoneNumber: body.phoneNumber,
    };

    try {
        await SNS.setSMSAttributes(AttributeParams).promise();
        await SNS.publish(messageParams).promise();
        return Responses._200({ message: 'text has been sent' });
    } catch (error) {
        console.log('error', error);
        return Responses._400({ message: 'text failed to send' });
    }
};

```

_Step 2_: Set serverless.yaml

```
provider:
  name: aws
  runtime: nodejs12.x
  profile: serverlessUser
  lambdaHashingVersion: 20201221
  iam:
    role:
      statements:
        - Effect: Allow
          Action:
              - sns:SetSMSAttributes
              - sns:Publish
          Resource: "*"
functions:
  sendSMS:
    handler: lambdas/endpoints/sendSMS.handler
    events:
            - http:
                  path: send-sms
                  method: POST
                  cors: true

```