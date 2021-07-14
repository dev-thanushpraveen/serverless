# Write a custom lambda hook

_Step 1_: npm i --save yup

_Step 2_: create new hooks named validateEventBody and validatePaths in hooks.js as follow

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

const hooksWithValidation = ({ bodySchema, pathSchema }) => {
  return useHooks(
    {
      before: [logEvent, parseEvent, validateEventBody, validatePaths],
      after: [],
      onError: [handleUnexpectedError],
    },
    {
      bodySchema,
      pathSchema,
    }
  );
};

exports.module = {
  withHooks,
  hooksWithValidation,
};

const validateEventBody = async (state) => {
  const { bodySchema } = state.config;

  if (!bodySchema) {
    throw Error("missing the requires body schema");
  }

  try {
    const { event } = state;

    await bodySchema.validate(event.body, { strict: true });
  } catch (e) {
    console.log("validation error of event.body", e);
    state.exit = true;
    state.response = {
      statusCode: 400,
      body: JSON.stringify({ error: e.message }),
    };
  }

  return state;
};

const validatePaths = async (state) => {
  const { pathSchema } = state.config;

  if (!pathSchema) {
    throw Error("missing the requires path params");
  }

  try {
    const { event } = state;

    await pathSchema.validate(event.pathParameters, { strict: true });
  } catch (e) {
    console.log("validation error of event.pathParameters", e);
    state.exit = true;
    state.response = {
      statusCode: 400,
      body: JSON.stringify({ error: e.message }),
    };
  }

  return state;
};

```

_Step 3_: Create bodySchema and pathSchema in endpoints as follow

```

const bodySchema = yup.object().shape({
  name: yup.string().required(),
  score: yup.number().required(),
});

const pathSchema = yup.object().shape({
  ID: yup.string().required(),
});
```

and

```

exports.handler = hooksWithValidation({bodySchema, pathSchema})(handler);

```

_Step 4_: Remove event.pathParameters check conditions because validatePaths hooks is handling it.