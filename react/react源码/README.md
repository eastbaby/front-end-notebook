###1.  组件构成、挂载、类型。

只有在挂载流程开始后，才会触发组件的生命周期，生成`ReactElement`类型的js对象，通过解析组件对象内部所携带的信息，获得对应的HTML信息，插入指定的DOM容器中，最终完成视图的渲染。



`ReactDOM.render()`方法根据传入的参数不同，在内部通过工厂方法生成四种不同类型的封装组件：

- ReactEmptyComponent
- ReactTextComponent
- ReactDOMComponent
- ReactCompositeComponent

通过执行每种封装组件内部的`mountComponent`方法解析出html。（只有ReactCompositeComponent的mountComponent会触发生命周期）



###2.  基于`setState`方法来探究事务机制和更新队列

setState方法中会执行`enqueueSetState`。回调函数由`enqueueCallback`处理。

`enqueueSetState`：

判断当前组件对象的state更新队列是否存在，如果存在则将`partialState`也就是新的state值加入队列；如果不存在，则创建该对象的更新队列。（队列是一个数组）。接着，更新队列。

实际上，React内部采用了"状态机"的概念，组件处于不同的状态时，所执行的逻辑也并不相同。以组件更新流程为例，React以事务+状态的形式对组件进行更新，因此接下来我们探讨事务的机制。

#### 事务(transaction)

用wrapper包裹原method

```js
transaction.perform(method);
//执行initialize方法
//执行method
//执行close方法
```
**两种事务**：
`RESET_BATCHED_UPDATES`---用于更改`isBatchingUpdates`的布尔值`false`或者`true`。

`FLUSH_BATCHED_UPDATES`---用于更新组件，处理回调。

细节：

`RESET_BATCHED_UPDATES`这个`wrapper`的作用是设置`isBatchingUpdates`也就是组件更新状态的值，组件有更新要求的话则设置为更新状态，更新结束后重新恢复原状态。这样做有什么好处呢？当然是为了避免组件的重复render，提升性能啦~

`FLUSH_BATCHED_UPDATES`这个`wrapper `的作用是：一是更新组件（更新之前，在一个生命周期内，在`componentShouldUpdate`执行之前，所有的`state`变化都会被**合并**，最后统一处理。）；二是若`setState`方法传入了回调函数则将回调函数存入`callbackQueue`队列。

> 一个生命周期的理解tip：不能在`componentWillUpdate`中调用`setState`的原因，就是`setState`会令`_pendingStateQueue`为`true`，导致再次执行`updateComponent`,而后会再次调用`componentWillUpdate`,最终循环调用`componentWillUpdate`导致浏览器的崩溃。



### 3. 事件系统（event）

**React实现了SyntheticEvent层处理事件。**与原生事件不同的点，只在于React对事件进行统一而不是分散的存储与管理，捕获事件后内部生成合成事件提高浏览器的兼容度，执行回调函数后再进行销毁释放内存，从而大大**提高网页的响应性能**。



详细来说，React并不像原生事件一样将事件和DOM一一对应，而是将所有的事件都绑定在网页的document，通过统一的事件监听器处理并分发，找到对应的回调函数并执行。（这样可以减少内存开销）按照官方文档的说法，事件处理程序将传递SyntheticEvent的实例。

大致功能有**事件注册，事件存储，事件分发，事件处理**。

组件挂载的时候，React就已经开始通过`mountComponent`内部，执行的是`enqueuePutListener`方法去注册事件。

因为事件回调函数执行后可能导致DOM结构的变化，那么React先将当前的结构以数组的形式存储起来，依次遍历执行。

代码中出现了新角色：`EventPluginHub.extractEvents`。查阅相关资料，得知`extractEvents`方法是用于合成事件的，也就是根据事件类型的不同，合成不同的跨浏览器的`SyntheticEvent`对象的实例，比如`SyntheticClickEvent`。接着执行`runEventQueueInBatch`批处理方法。

...etc...





参考：

<https://juejin.im/post/5a84682ef265da4e83266cc4>

<<https://yuchengkai.cn/react/#%E4%BB%8B%E7%BB%8D>>

