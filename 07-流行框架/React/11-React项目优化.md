# 11-React 项目优化

## 一 Component 类的问题

使用 Component 实现的组件，在状态更新术后会有以下问题：

- 只要执行了 setState()，即使该函数内没做任何事情，组件依然会重新 render
- 父组件 render 之后，子组件即使没有用到父组件数据也会 render！

Component 组件只有在组件的 state 或者 props 真正发生改变触发 render 时，效率才会变高。

render 一直被触发的原因是：shouldComponentUpdate() 这个阀门总是返回 true。所以我们可以在该生命周期内手动进行原状态、修改状态的对比，决定是否返回 true。

但是对于一些引用数据，对比起来较为复杂，React 已经提供了代替 Component 的方案，其内部原理也是对比状态的变化情况。

```js
import React, {PureComponent}

export default Comp extends PureComponent {}
```
