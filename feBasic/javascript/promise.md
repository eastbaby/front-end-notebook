## promise基本用法

`Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果.

Promise标准中仅指定了Promise对象的then方法的行为，其它一切我们常见的方法/函数都并没有指定，包括catch，race，all等常用方法，甚至也没有指定该如何构造出一个Promise对象，另外then也没有一般实现中（Q, $q等）所支持的第三个参数，一般称onProgress。

then方法返回一个新的Promise Promise的then方法返回一个新的Promise，而不是返回this。



```js
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```







参考：

30分钟，让你彻底明白Promise原理：<https://juejin.im/post/59dd8b3851882578e04aa05e> （ 从promise链式开始就看晕了）

Promise原理讲解 && 实现一个Promise对象 (遵循Promise/A+规范：<https://juejin.im/post/5aa7868b6fb9a028dd4de672>

从零一步一步实现一个完整版的Promise：<https://juejin.im/post/5d59757f6fb9a06ae76405c6> （介绍的超级详细）

八段代码彻底掌握promise：https://juejin.im/post/597724c26fb9a06bb75260e8





## async/await

代码片段。

可以看到如果await后面跟着的promise函数走向了reject，会掉入catch的error中。

```jsx
async function handleSubmit(values) {
  dispatch({type: types.SUBMIT_STARTED})
  try {
    const responseJson = await fetch(/* stuff */).then(r => r.json())
    dispatch({type: types.SUBMIT_COMPLETE, response: responseJson})
  } catch (error) {
    dispatch({type: types.SUBMIT_ERROR, errorMessage: 'Something went wrong!'})
  }
}
```

