## promise

### 基本用法

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



### 注意点

当我们在构造 `Promise` 的时候，构造函数内部的代码是**立即执行**的:

```js
new Promise((resolve, reject) => {
  console.log('new Promise')
  resolve('success')
})
console.log('finifsh')
// new Promise -> finifsh
```



### 优缺点

优点：避免地狱回调

缺点：无法取消 `Promise`，错误需要通过回调函数捕获。



### promise实现

最cuo版本

```js
const PENDING = 'pending'
const RESOLVED = 'resolved'
const REJECTED = 'rejected'
function myPromise(fn) {
  const that = this
  that.state = PENDING
  that.value = null
  that.resolvedCallbacks = []
  that.rejectedCallbacks = []
  const resolve = (value) => {
    if (that.state === PENDING) {
        that.state = RESOLVED
        that.value = value
        that.resolvedCallbacks.map(cb => cb(that.value))
    }
  };
    
  const reject = (value) => {
    if (that.state === PENDING) {
        that.state = REJECTED
        that.value = value
        that.rejectedCallbacks.map(cb => cb(that.value))
    }
  };

  try {
      fn(resolve, reject)
  } catch (e) {
      reject(e)
  }
  
}

MyPromise.prototype.then = function(onFulfilled, onRejected) {
  const that = this
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
  onRejected = typeof onRejected === 'function' ? onRejected : 
  		r => {
          throw r
        }
  if (that.state === PENDING) {
    that.resolvedCallbacks.push(onFulfilled)
    that.rejectedCallbacks.push(onRejected)
  }
  if (that.state === RESOLVED) {
    onFulfilled(that.value) // 进阶版要改成异步
  }
  if (that.state === REJECTED) {
    onRejected(that.value) // 进阶版要改成异步
  }
}
```



继续改造

```js
function resolve(value) {
  if (value instanceof MyPromise) {
    return value.then(resolve, reject)
  }
  // 规范规定，这里的几个关键操作要setTimeout异步
  setTimeout(() => {
    if (that.state === PENDING) {
      that.state = RESOLVED
      that.value = value
      that.resolvedCallbacks.map(cb => cb(that.value))
    }
  }, 0)
}
// 主要then要大改。里面有个特别关键的函数resolvePromise
// then 要返回 promise；期间各种操作包装try-catch；
```



>  resolvePromise即为根据x的值（onFulFilled、onRejected）返回值来决定promise2的状态的函数。
>
> see. <https://juejin.im/post/5d59757f6fb9a06ae76405c6#heading-10>



参考：

小册方法。(不是特别详细，思路正常)

从零一步一步实现一个完整版的Promise：<https://juejin.im/post/5d59757f6fb9a06ae76405c6> （介绍的超级详细，建议看这个）

Promise原理讲解 && 实现一个Promise对象 (遵循Promise/A+规范：<https://juejin.im/post/5aa7868b6fb9a028dd4de672>

八段代码彻底掌握promise：https://juejin.im/post/597724c26fb9a06bb75260e8





## async/await

其实 `await` 就是 `generator` 加上 `Promise` 的语法糖，且内部实现了自动执行 `generator`。如果你熟悉 co 的话，其实自己就可以实现这样的语法糖。

优点：处理 `then` 的调用链，能够更清晰准确的写出代码，毕竟写一大堆 `then` 也很恶心，并且也能优雅地解决回调地狱问题。

缺点：因为 `aa` 将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了 `await` 会导致性能上的降低。



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



由于promise里面一旦状态改变，就不会再变，任何时候都可以得到这个结果。所以aa化的时候内部返回`new promise`，新建一个promise。



阅读参考[to read]：<https://segmentfault.com/a/1190000007535316>

## 定时

常见的定时器函数有 `setTimeout`、`setInterval`、`requestAnimationFrame`。

通常来说不建议使用 `setInterval`。第一，它和 `setTimeout` 一样，不能保证在预期的时间执行任务。第二，它存在执行累积的问题。如果你有循环定时器的需求，其实完全可以通过 `requestAnimationFrame` 来实现。

首先 `requestAnimationFrame` 自带函数节流功能，基本可以保证在 16.6 毫秒内只执行一次（不掉帧的情况下）。

参考：你需要知道的requestAnimationFrame <https://juejin.im/post/5a82f0626fb9a06358657c9c>

