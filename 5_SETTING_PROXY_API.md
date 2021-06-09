# Setting Proxy API

_Step 1_: Add proxy functions in serverless.yaml as follow:

```
functions:
    proxy:
    handler: lambdas/endpoints/proxy.handler
    events:
      - http:
          path: chuck-norris/{proxy+}
          method: ANY
          integration: http-proxy
          request:
            uri: http://api.icndb.com/{proxy}  #url
            parameters:
              paths:
                proxy: true
```

_Step 2_ create proxy.js file in endpoints:

```
const Responses = require("../common/API_Responses");

exports.handler = async (event) => {
  console.log("event", event);

  return Responses._200();
};
```
