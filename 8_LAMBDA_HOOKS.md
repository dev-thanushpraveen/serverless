# Implementing lambda hooks in myserverlessproject repo

_Step 1_: Install lambda-hooks

> npm install --save lambda-hooks

_Step 2_: create hooks.js in common folder and import from lambda-hooks

```
const {
  useHooks,
  logEvent,
  parseEvent,
  handleUnexpectedError,
} = require("lambda-hooks");

const withHooks = useHooks({
  before: [logEvent, parseEvent],
  after: [],
  onError: [handleUnexpectedError],
});

exports.module = {
  withHooks,
};
```

_Step 4_: Change handle as follow on all endpoints
```
const handler = async () => {}

exports.handler = withHooks(handler)


```

_Step 3_: Remove event log if any because we are using logEvent.

_Step 4_:  const user = JSON.parse(event.body); can be replaced with const user = event.body because parseEvent hooks is used

_Step 5_: Remove error handling as handleUnexpectedError is used