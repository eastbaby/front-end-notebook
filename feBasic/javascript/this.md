- 当作为构造函数（new）时，构造函数内部的 this 永远指向实例，不会被任何方式改变。

- 箭头函数其实是没有 `this` 的，箭头函数中的 `this` 只取决包裹箭头函数的第一个普通函数的 `this`。另外对箭头函数使用 `bind` 这类函数是无效的。同时，箭头函数的 `this` 一旦被绑定，就**不会再被任何方式所改变**。
- 多次bind：不管我们给函数 `bind` 几次，`fn` 中的 `this` 永远由第一次 `bind` 决定。



this优先级：

首先，`new` 的方式优先级最高，接下来是 `bind` 这些函数，然后是 `obj.foo()` 这种调用方式，最后是 `foo` 这种调用方式。

<https://tech-query.me/programming/javascript-in-one-word/>

