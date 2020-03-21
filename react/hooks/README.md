# React Hooks

### 1. 基础用法

非常好的文章：<https://github.com/happylindz/blog/issues/19>

useState、useEffect、useContext、useReducer、

useCallback（记忆函数，配合pureComponent/memo更佳）、useMemo（记忆组件）、

useRef（功能强大，可以用来避免function组件的capture value特性）、

useImperativeHandle（配合forwardRef使用）、useLayoutEffect （同步执行副作用，时机：Dom更新-> 同步useLayoutEffect -> 异步useEffect ）



**React Hooks 优缺点**

缺点：尽管我们通过上面的例子看到 React Hooks 的强大之处，似乎类组件完全都可以使用 React Hooks 重写。但是当下 v16.8 的版本中，还无法实现 getSnapshotBeforeUpdate 和 componentDidCatch 这两个在类组件中的生命周期函数。官方也计划在不久的将来在 React Hooks 进行实现。

| Class                                          | Hooks                |
| ---------------------------------------------- | -------------------- |
| 代码逻辑清晰（构造函数、componentDidMount 等） | 需要配合变量名和注释 |
| 不容易内存泄漏                                 | 容易发生内存泄漏     |



**Hooks基础用法**

```js
import React, { useState, useEffect } from "react";
let timer = null;
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = "componentDidMount" + count;
  },[count]); // 监听count变化
  
  useEffect(() => {
    timer = setInterval(() => {
      setCount(prevCount => prevCount + 1);
    }, 1000);
    return () => {
      document.title = "componentWillUnmount";
      clearInterval(timer);
    };
  }); // case1 不监听任何参数变化.代替 componentDidMount componentDidUpdate 和 componentWillUnmount.
  // case2 监听一个空数组. 代替 componentDidMount 和 componentWillUnmount.
  // case3 如果有监听数据的话，能类似参与componentDidUpdate的比较阶段，跳过effect进行性能优化 [See. https://zh-hans.reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects] 但是要注意一个坑！（见下方）
  return (
    <div>
      Count: {count}
      <button onClick={() => clearInterval(timer)}>clear</button>
    </div>
  );
}
```

#### useState

原理：<https://xin-tan.com/passages/2019-10-21-react-hooks/>

基于 Array+Cursor 来实现。

#### useEffect

原理：<https://xin-tan.com/passages/2019-10-21-react-hooks/>

**useEffect 会在每次渲染后都执行吗？** 是的，默认情况下，它在第一次渲染之后*和*每次更新之后都会执行（即：**didmount + didupdate**）。（我们稍后会谈到[如何控制它](https://zh-hans.reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)。）你可能会更容易接受 effect 发生在“渲染之后”这种概念，不用再去考虑“挂载”还是“更新”。React 保证了每次运行 effect 的同时，DOM 都已经更新完毕。



一个好处：为什么每次更新的时候都要运行 Effect <https://zh-hans.reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update>，可以编写更多bug free代码。



使用case3的自定义监听元素此优化方式的时候，请确保数组中包含了**所有外部作用域中会随时间变化并且在 effect 中使用的变量**，否则你的代码会引用到先前渲染中的旧变量。（比如用swiper的时候有踩坑）。参阅文档，了解更多关于[如何处理函数](https://zh-hans.reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)以及[数组频繁变化时的措施](https://zh-hans.reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)内容。

[如何处理函数](https://zh-hans.reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies) 这里提到，如果是在没有办法，可以用useCallback包裹。



比较deps如果是对象object，不是深度比较，而是用的`is`。see.<https://github.com/facebook/react/blob/c1d3f7f1a9/packages/shared/objectIs.js?spm=ata.13261165.0.0.6dee5600znB8bR#L14>



用useEffect的时候一定要小心闭包的坑。如果在useEffect里面监听一个事件，这时候用`setCount(num + 1)`  的时候拿到的`num` 只是第一次的值，并不会每次触发事件的时候更新到新的`num`。原因：state在每次更新的时候都是一个新的值。解决方案：用函数的写法写`setCount`，即使销毁事件或者用useRef迂回。



> 与 `componentDidMount` 或 `componentDidUpdate` 不同，使用 `useEffect` 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。在个别情况下（例如测量布局），有单独的 [`useLayoutEffect`](https://zh-hans.reactjs.org/docs/hooks-reference.html#uselayouteffect) Hook 供你使用，其 API 与 `useEffect` 相同。



### 2. 模拟class的各个生命周期

- componentDidUpdate 

```js
function useUpdate(fn) {
    // useRef 创建一个引用
    const mounting = useRef(true);
    useEffect(() => {
      if (mounting.current) {
        mounting.current = false;
      } else {
        fn();
      }
    });
}
```

- shouldComponentUpdate

<https://juejin.im/post/5c8eec1bf265da67cb619e79#heading-5>

- forceUpdate

<https://juejin.im/post/5c8eec1bf265da67cb619e79#heading-7>

- usePrevious


```js
// 理解核心：useEffect是在渲染DOM结束以后才会调用！
const usePrevious = (value) => {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  }, value);
  return ref.current;
}

----
// 帮助理解的例子（from知乎）：
const exampleComponent = (value) => {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  }, value);
  return <h1>{ref.current}</h1>
}
```

第一次exampleComponent被执行，函数参数value为1，ref.current先是undefined   （因为这时useEffect还没有被调用），然后根据return的JSX，渲染DOM，页面上被渲染出"undefined".
接着 useEffect被调用，此时ref 的current值被赋值，是这次渲染的props -> value，也就是1。 

第二次exampleComponent被再次调用，函数参数value变成了2，依旧是先根据return的JSX渲染DOM，还记得吗，第一次exampleComponent被调用的后期，ref.current变成了1，因为没有任何**页面上display**改变ref.current的操作，所以页面中渲染出 1 ，渲染完成以后，开始老步骤，执行useEffect，这一次的函数参数value是2，所以ref.current变成了2。

> 参考：zhihu.com/question/346140951

- 不足

当下 v16.8 的版本中，还无法实现 getSnapshotBeforeUpdate 和 componentDidCatch 这两个在类组件中的生命周期函数。



### 更多理解

#### useRef

`useRef` 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）（如果什么都没传是`undefined`)。返回的 ref 对象在组件的整个生命周期内**保持不变**。

useRef 神奇功能：

- 可以模拟class的实例变量 <https://zh-hans.reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables>
- 避免function的capture value特点。因为， ref 在所有 Render 过程中保持着唯一引用，因此所有对 ref 的赋值或取值，拿到的都只有一个最终状态，而不会在每个 Render 间存在隔离。

#### capture value

例子一：state  https://github.com/happylindz/blog/issues/19

例子而：props <https://juejin.im/post/5c8eec1bf265da67cb619e79#heading-2>

#### useCallback

<https://zhuanlan.zhihu.com/p/56975681>

当deps是函数的时候，建议都包裹一下。

#### useMemo

记住，传入 `useMemo` 的函数会在**渲染期间**执行。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴，而不是 `useMemo`。

#### useContext

推荐用unstated-next更好用且安全。<https://github.com/jamiebuilds/unstated-next/blob/master/README-zh-cn.md>

#### hooks编码注意事项

- hooks api前面不能有return（否则可能多次渲染的时候执行hooks的数量不一致，引发error）

https://stackoverflow.com/questions/53472795/uncaught-error-rendered-fewer-hooks-than-expected-this-may-be-caused-by-an-acc>

换句话说，hooks有个要求：在使用 Hook 的时候，请在函数组件顶部使用！不能在循环、判断内部使用 Hook。

原理：[《React hooks: not magic, just arrays》](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)中提及，React Hook 看起来非常 Magic 的实现，本质上还是通过 Array 来实现的。

- 一定要记得开启hooks lint <https://www.npmjs.com/package/eslint-plugin-react-hooks>





## 更多阅读

精度系列 <https://zhuanlan.zhihu.com/p/59558396>

Should I useState or useReducer? <https://kentcdodds.com/blog/should-i-usestate-or-usereducer>

2019年了，整理了N个实用案例帮你快速迁移到React Hooks：<https://juejin.im/post/5d594ea5518825041301bbcb>

[TODO] React Hooks 深入不浅出：<https://juejin.im/post/5bfe93566fb9a049c30af2db>

一文彻底搞懂react hooks的原理和实现：<https://xin-tan.com/passages/2019-10-21-react-hooks/>

[TODO] react hooks 原理：<https://github.com/brickspert/blog/issues/26>

