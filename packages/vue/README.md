# @tryabby/vue

## createAbby

### Parameters

The `createAbby` function takes an object as a parameter. The object can contain the following properties:

| Name               | Type     | Required | Description                                             | details               |
| ------------------ | -------- | :------: | ------------------------------------------------------- | --------------------- |
| projectId          | `string` |    ✅    | The ID of your project in Abby                          | -                     |
| apiUrl             | `string` |          | The URL of the Abby API. Defaults to the hosted version | -                     |
| currentEnvironment | `string` |    ✅    | The current environment of your application             | [link](/environments) |
| tests              | `object` |          | An object containing your defined A/B Tests             | -                     |
| flags              | `object` |          | An array containing your defined Feature Flags          | -                     |
| settings           | `object` |          | An object with additional settings for Abby             | -                     |

#### tests

The tests property is an object containing your defined A/B Tests. You probably want to use the Copy Button in your dashboard to copy the tests object.
They keys of the object represent the names of your predefined A/B tests. The values are objects containing the following properties:

| Name     | Type            | Required | Description                                             |
| -------- | --------------- | :------: | ------------------------------------------------------- |
| variants | `Array<string>` |    ✅    | An array of strings containing the variants of the test |

##### Example

```ts
const abby = createAbby({
  // ... your config
  tests: {
    "test-abtest": {
      variants: ["control", "variant-a", "variant-b"],
    },
  },
});
```

#### flags

The flags property is an array containing your defined Feature Flags. You probably want to use the Copy Button in your dashboard to copy the flags array.

##### Example

```ts
const abby = createAbby({
  // ... your config
  flags: { "test-flag": "Boolean" },
});
```

#### settings

The settings property is an object containing additional settings for Abby. The following properties are available:

- `flags.defaultValues`: Allows you to set a general default value for each flag type. The keys of the object represent the types of the flags.
  The default value is the following:

  ```json
  {
    "Boolean": false,
    "String": "",
    "Number": 0,
    "JSON": {}
  }
  ```

- `flags.devOverrides`: An object containing the values of feature flags in development mode. The keys of the object represent the names of the flags.
  The values need to be of the type of the flag. This means if your flag is a `String` flag, this needs to be a `string`.

### Return Values

#### useAbby

`useAbby` is a react hook that used to access the value of an A/B Test.
Recurring users will always get the same value for a test.
New users will get a random value for a test depending on the defined weights

##### Parameters

- `string`: The name of the test or flag, needs to be one of the defined tests.

##### Return Values

- `variant` : The variant of the test

- `onAct`: A function to call when the user performs an action associated with the test _Type: `function`_

#### useFeatureFlag

`useFeatureFlag` is a react hook that used to access the value of a Feature Flag.

##### Parameters

The name of the test or flag, needs to be one of the defined flags.

##### Return Value

The value of the flag _Type: `boolean`_

#### AbbyProvider

A react component to wrap your application.

##### Props

- `children`: The children of the component
- `initialData (optional)`: The data (weights, tests, etc). If not provided, the data will be fetched on the client.

#### getFeatureFlagValue

`getFeatureFlagValue` is a function to access the value of a feature flag. This can be called in a non-react scope

##### Parameters

The name of the test or flag, needs to be one of the defined flags.

#### getABTestValue

`getABTestValue` is a function to access the users variant of an A/B Test. This can be called in a non-react scope.
If the user is new, a random variant will be generated based on the weights, persisted in a cookie and returned.
Otherwise the variant will be read from the cookie and returned.

##### Parameters

The name of the test, needs to be one of the defined tests.

##### Return Values

The variant of the test.

#### withDevtools

`withDevtools` is a higher order function to wrap the Devtools from [`@tryabby/devtools`](/devtools) for usage within Reacts.

##### Parameters

The Devtools component from `@tryabby/devtools`

##### Example

```jsx
import { AbbyDevTools } from "@tryabby/devtools";
export const AbbyDevtools = withDevtools(AbbyDevTools);
```

#### getABResetFunction

This is a function which returns a function that can be used to reset the stored variant for the current user.
This means the cookie will be deleted and the user will get a new variant on the next page load.

#### Parameters

The name of the test, needs to be one of the defined tests.

#### Example

```tsx
import { getABResetFunction } from "lib/abby";

export default function SomePage() {
  const onReset = () => {
    const resetCookie = getABResetFunction("SignupButton");
    resetCookie();
    window.reload();
  };

  return (
    <div>
      <h1>Hello World</h1>
      <button onClick={onReset}>Reset Cookie</button>
    </div>
  );
}
```
