## event loop

当遇到异步的代码时，会被**挂起**并在需要执行的时候加入到 Task（有多种 Task） 队列中。一旦执行栈为空，Event Loop 就会从 Task 队列中拿出需要执行的代码并放入执行栈中执行，所以本质上来说 JS 中的异步还是同步行为。



任务源可以分为 **微任务**（microtask/jobs） 和 **宏任务**（macrotask/task）。在 ES6 规范中，microtask 称为 `jobs`，macrotask 称为 `task`。



**微任务**包括 `process.nextTick` ，`promise` ，`MutationObserver`。（其中 `process.nextTick` 为 Node 独有，在node的microtask中最高优。）

**宏任务**包括 同步`script` ， `setTimeout` ，`setInterval` ，`setImmediate` ，`I/O` ，`UI rendering`，`xhr`

。

浏览器执行顺序：

1. 同步代码宏任务
2. 异步微任务
3. 异步宏任务



经典题：

```js
console.log('script start')

async function async1() {
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2 end')
}
async1()

setTimeout(function() {
  console.log('setTimeout')
}, 0)

new Promise(resolve => {
  console.log('Promise')
  resolve()
})
  .then(function() {
    console.log('promise1')
  })
  .then(function() {
    console.log('promise2')
  })

console.log('script end')
```

便于理解，上面 `async`的代码可以改造类似如下：

```js
new Promise((resolve, reject) => {
  console.log('async2 end')
  // Promise.resolve() 将代码插入微任务队列尾部 (async2)
  // resolve 再次插入微任务队列尾部 (async1)
  resolve(Promise.resolve())
}).then(() => {
  console.log('async1 end')
})
```



结果：

script start -> async2 end -> Promise -> script end -> promise1 -> promise2 -> async1 end -> setTimeout

（注意：最新版的 chrome 浏览器对于 await 的处理变快了，async1 会先于 promise 1 打印）。

详细过程解析：<https://juejin.im/post/5c7e3fdbf265da2dca38856e>



> 重绘和回流其实也和 Eventloop 有关。
>
> 当 Eventloop 执行完 Microtasks 后，会判断是否要发生界面更新。
>
> <https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model>



## node中的特殊性

Node 中的 Event Loop 和浏览器中的是完全不相同的东西。

Node 的 Event Loop 分为 6 个阶段，它们会按照**顺序**反复运行。每当进入某一个阶段的时候，都会从对应的回调队列中取出函数去执行。当队列为空或者执行的回调函数数量到达系统设定的阈值，就会进入下一阶段。