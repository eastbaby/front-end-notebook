### new 的理解

- 创建一个全新的对象
- 这个对象被执行 `[[Prototype]]` 连接
- 将这个对象绑定到构造函数中的 `this`
- 如果函数没有返回其他对象，则 `new` 操作符调用的函数则会返回这个对象

### 模拟实现

```js
function Tony () {
    ……
}

// 使用 new
var person = new Tony(……);
// 使用 objectFactory
var person = objectFactory(Tony, ……)
```

```js
// 第二版的代码
function objectFactory() {
    var obj = new Object();
    var Constructor = [].shift.call(arguments);
    obj.__proto__ = Constructor.prototype;
    var ret = Constructor.apply(obj, arguments);
    return typeof ret === 'object' ? ret : obj; // 特别注意如果构造函数返回是一个对象，我们就返回这个对象。
};
```



<https://github.com/mqyqingfeng/Blog/issues/13>

