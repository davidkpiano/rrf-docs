# Errors Component

## `<Errors />`

The `<Errors />` component provides a handy way of displaying form errors for a given `model`.

```js
// in render...
<Field model="user.email"
  validity={{ isEmail, isRequired }}>
  <input type="email" />
</Field>

<Errors model="user.email"
  messages={{
    isRequired: 'Please provide an email address.',
    isEmail: (val) => `${val} is not a valid email.`,
  }}/>
```

By default, `<Errors />` will display a `<div>` with each error message wrapped in a `<span>` as children:

```html
<div>
  <span>foo@bar is not a valid email.</span>
  <!-- ...other error messages -->
</div>
```

There are many configurable props that will let you control:
- when error messages should be shown
- custom error messages based on the model value
- the wrapper component (default: `<div>`)
- the message component (default: `<span>`)

## Errors Component Props

### `model` prop

_(String | Function)_ - the string representation of the model path to show the errors for that model. A tracking function may be provided, as well.

### `messages` prop

_(Object)_ - a plain object mapping where:
- the keys are error keys (such as `"required"`)
- the values are either strings or functions.

If the message value is a function, it will be called with the model value.

**Example**

```js
<Errors model="user.email"
  messages={{
    required: 'Please enter an email address.',
    length: 'The email address is too long.',
    invalid: (val) => `${val} is not a valid email address.',
  }} />
```

**Tips**
- The `messages` prop is a great place to keep custom error messages that can vary based on the location in the UI, instead of hardcoding error messages in validation fuctions.
- If a message is _not_ provided for an error key, the message will default to the key value in the field `.errors` property.
  - This means if you're using `setErrors` or the `errors` prop in `<Field>` to set error messages, they will automatically be shown in `<Errors />`.


