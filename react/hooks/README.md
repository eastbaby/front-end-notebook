# React Hooks

非常好的文章：<https://github.com/happylindz/blog/issues/19>

useState、useEffect、useContext、useReducer、

useCallback（记忆函数，配合pureComponent/memo更佳）、useMemo（记忆组件）、

useRef（功能强大，可以用来避免function组件的capture value特性）、

useImperativeHandle（配合forwardRef使用）、useLayoutEffect （同步执行副作用，时机：Dom更新-> 同步useLayoutEffect -> 异步useEffect ）



**React Hooks 不足**

尽管我们通过上面的例子看到 React Hooks 的强大之处，似乎类组件完全都可以使用 React Hooks 重写。但是当下 v16.8 的版本中，还无法实现 getSnapshotBeforeUpdate 和 componentDidCatch 这两个在类组件中的生命周期函数。官方也计划在不久的将来在 React Hooks 进行实现。



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
  }, []); // 不监听任何参数变化.代替 componentDidMount 和 componentWillUnmount.
  return (
    <div>
      Count: {count}
      <button onClick={() => clearInterval(timer)}>clear</button>
    </div>
  );
}
```



### 模拟class的各个生命周期

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



### useMemo

记住，传入 `useMemo` 的函数会在**渲染期间**执行。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴，而不是 `useMemo`。





## 更多阅读

精度系列 <https://zhuanlan.zhihu.com/p/59558396>

Should I useState or useReducer? <https://kentcdodds.com/blog/should-i-usestate-or-usereducer>

2019年了，整理了N个实用案例帮你快速迁移到React Hooks：<https://juejin.im/post/5d594ea5518825041301bbcb>

