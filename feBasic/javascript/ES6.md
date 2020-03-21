### Proxy&Reflect

`Reflect`对象与`Proxy`对象一样，也是 ES6 为了操作对象而提供的新 API。

`Proxy` 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。

`Reflect`对象的设计目的有: (1)  将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。未来的新方法将只部署在`Reflect`对象上。（2） 修改某些`Object`方法的返回结果，让其变得更合理。



`Reflect`对象一共有 13 个静态方法。相应的，`proxy`也有13种方法。它们一一对应。

>- Reflect.apply(target, thisArg, args)
>
>- Reflect.construct(target, args)
>- Reflect.get(target, name, receiver)
>- Reflect.set(target, name, value, receiver)
>- Reflect.defineProperty(target, name, desc)
>- Reflect.deleteProperty(target, name)
>- Reflect.has(target, name)
>- Reflect.ownKeys(target)
>- Reflect.isExtensible(target)
>- Reflect.preventExtensions(target)
>- Reflect.getOwnPropertyDescriptor(target, name)
>- Reflect.getPrototypeOf(target)
>- Reflect.setPrototypeOf(target, prototype)

上面这些方法的作用，大部分与`Object`对象的同名方法的作用都是相同的。

`Reflect.ownKeys`方法用于返回对象的所有属性，基本等同于`Object.getOwnPropertyNames`与`Object.getOwnPropertySymbols`之和。【在深拷贝中很有用】

> TIP
>
> 对象的所有属性甚至包括不可枚举的: [`Object.getOwnPropertyNames`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames)。
>
> 对象的自身可枚举属性组成的数组: **Object.keys()**。 (数组中属性名的排列顺序和使用 [`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 循环遍历该对象时返回的顺序一致 。)

**proxy的应用**

Vue3.0 中通过 `Proxy` 来替换原本的 `Object.defineProperty` 来实现数据响应式。

如果需要实现一个 Vue 中的响应式，需要我们在 `get` 中收集依赖，在 `set` 派发更新，之所以 Vue3.0 要使用 `Proxy` 替换原本的 API 原因在于 `Proxy` 无需一层层递归为每个属性添加代理，一次即可完成以上操作(在proxy内部写个递归即可），性能上更好，并且原本的实现有一些数据更新不能监听到，但是 `Proxy` 可以完美监听到任何方式的数据改变，唯一缺陷可能就是浏览器的兼容性不好了。




### class

Class类的所有方法都定义在类的`prototype`属性上面。

但是如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。（业务代码中私有方法有时候会写成static)

