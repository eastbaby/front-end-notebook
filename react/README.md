# REACT

REACT 方法绑定this的各个方法：<https://olafcheng.github.io/2017/08/14/this-in-react-with-es6/>



componentDidUpdate 是需要props/state有变化才会触发。如果有子组件有自己的state导致dom更新，不会触发父组件的componentDidUpdate。

用dva的时候由于全局管理model情况可能会好一点，但也没法控制子组件的行为。



函数组件(fuction/无状态) 和类组件是可以混用的。类组件可以成为函数组件的子类。



`props`就是汲取了纯函数的思想。props它最大的特点就是不可变。组件内部不能改变props，否则react报错。注意 props 在function组件中有 capture value 特性（区别于类组件用this.props表现不一样）。（参考：<https://zhuanlan.zhihu.com/p/59558396>）

`props`书写可以用jsx的`...`语法。(<https://zh-hans.reactjs.org/docs/jsx-in-depth.html#spread-attributes>)





Hooks 同样具有 capture value 的特性，利用 `useRef` 可以规避 capture value 特性。



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

<https://juejin.im/post/59abb01c518825243f1b6dad>

<https://zh-hans.reactjs.org/docs/lists-and-keys.html>



### Redux

时光机？



