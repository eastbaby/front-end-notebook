模拟实现:

```js
function Otaku () {
    ……
}

// 使用 new
var person = new Otaku(……);
// 使用 objectFactory
var person = objectFactory(Otaku, ……)
```

```js
// 第二版的代码
function objectFactory() {
    var obj = new Object(), Constructor = [].shift.call(arguments);
    obj.__proto__ = Constructor.prototype;
    var ret = Constructor.apply(obj, arguments);
    return typeof ret === 'object' ? ret : obj; // 特别注意如果构造函数返回是一个对象，我们就返回这个对象。
};
```



<https://github.com/mqyqingfeng/Blog/issues/13>

