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

### `

