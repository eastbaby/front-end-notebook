
### UNSAFE_componentWillReceiveProps
`UNSAFE_componentWillReceiveProps()` 会在已挂载的组件接收新的 props 之前被调用。如果你需要更新状态以响应 prop 更改（例如，重置它），你可以比较 `this.props` 和 `nextProps` 并在此方法中使用 `this.setState()` 执行 state 转换。

请注意，**如果父组件导致组件重新渲染，即使 props 没有更改，也会调用此方法。**如果只想处理更改，请确保进行当前值与变更值的比较。

在[挂载](https://zh-hans.reactjs.org/docs/react-component.html#mounting)过程中，React 不会针对初始 props 调用 `UNSAFE_componentWillReceiveProps()`。组件只会在组件的 props 更新时调用此方法。调用 `this.setState()` 通常不会触发 `UNSAFE_componentWillReceiveProps()`。

