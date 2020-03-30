# REACT

### 用react的好处

todo 

### 查漏补缺

REACT 方法绑定this的各个方法：<https://olafcheng.github.io/2017/08/14/this-in-react-with-es6/>



渲染到Native中的叫ReactNative，渲染到Canvas中的叫ReactCanvas，正常用的渲染是ReactDOM。因此会和React库分开使用。



componentDidUpdate 是需要props/state有变化才会触发。如果有子组件有自己的state导致dom更新，不会触发父组件的componentDidUpdate。

用dva的时候由于全局管理model情况可能会好一点，但也没法控制子组件的行为。



函数组件(fuction/无状态) 和类组件是可以混用的。类组件可以成为函数组件的子类。



`props`就是汲取了纯函数的思想。props它最大的特点就是不可变。组件内部不能改变props，否则react报错。注意 props 在function组件中有 capture value 特性（区别于类组件用this.props表现不一样）。（参考：<https://zhuanlan.zhihu.com/p/59558396>）

`props`书写可以用jsx的`...`语法。(<https://zh-hans.reactjs.org/docs/jsx-in-depth.html#spread-attributes>)



不同的router跳转方式<https://www.jianshu.com/p/6a3bfd62bcde>



Hooks 同样具有 capture value 的特性，利用 `useRef` 可以规避 capture value 特性。



必看：<https://overreacted.io/zh-hans/writing-resilient-components/>（里面聊到 update -> setState -> update 循环的这种时候如何正确处理）

### Context

定义context用createContext API。使用的时候可以用consumer消费者使用，也可以利用类的contextType使用。再或者移步用useContext的hooks API。

例子一：

```js
const { Provider, Consumer } = React.createContext(null);
function Bar() {
  return <Consumer>{color => <div>{color}</div>}</Consumer>;
}
function Foo() {
  return <Bar />;
}
function App() {
  return (
    <Provider value={"grey"}>
      <Foo />
    </Provider>
  );
}
```

例子二：

```js
class MyClass extends React.Component {
  static contextType = MyContext;
  render() {
    let value = this.context;
    /* 基于这个值进行渲染工作 */
  }
}
```



优秀网站：

react最佳实践snippet  <https://github.com/30-seconds/30-seconds-of-react>



### Key

当元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key。如果你选择不指定显式的 key 值，那么 React 将默认使用索引用作为列表项目的 key 值。

React16.0开始，如果存在前后两个相同的`key`，React会认为这两个元素其实是一个元素，后一个具有相同key值的元素会被忽略。

React15的时候对于重复的key，会丢弃后面相同的key的item不展示。然而React16的时候对于重复的key全部都展示出来。

When a `key` changes, React will [*create* a new component instance rather than *update* the current one](https://zh-hans.reactjs.org/docs/reconciliation.html#keys). 

<https://juejin.im/post/59abb01c518825243f1b6dad>

<https://zh-hans.reactjs.org/docs/lists-and-keys.html>



### Virtual DOM

https://github.com/livoras/blog/issues/13

<https://xin-tan.com/passages/2019-11-11-wirte-virtual-dom/>

### Redux

原理：一定要提一下provider才算答对。

时间旅行

redux公式：state的变化= ui变化。

<https://juejin.im/post/5b9a2f025188255c48349ec1>

### TOREAD

【React深入】从Mixin到HOC再到Hook ： https://juejin.im/post/5cad39b3f265da03502b1c0a

[Immutable 详解及 React 中实践](https://github.com/camsong/blog/issues/3) 

