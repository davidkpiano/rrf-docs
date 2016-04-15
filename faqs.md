# FAQs

**How can I update the value of my component externally?**

Dispatch the `actions.change(model, value)` to change the model value. For the component to show this changed value, keep in mind that components are _uncontrolled_ by default. To make it controlled, pass in the value to the `value={...}` prop:

```js
// in render():
const { user } = this.props;

return
<Field model="user.name">
  <input type="text" value={ user.name } />
</Field>
```

**Why are controls inside `<Field>` uncontrolled?**

To give you, the developer, greater flexibility. It allows for:
- Change on `blur`, `focus`, debounced/throttled changes, etc.
- A custom value `parser` without having to specify an ad-hoc `formatter` prop