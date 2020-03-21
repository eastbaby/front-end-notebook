## call/apply

`call/apply`使用例子：

fn.call(context, param1, param2, ...);

fn.call(context, paramArr);

```js
// 实现call
Function.prototype.mycall = function (context) {
    context = context || 'window'; // null 的时候指向window
    context.fn = this;
    const args = [];
    for(let i=1; i<arguments.length; i++) {
        args.push(`arguments[${i}]`);
    }
    const result = eval('context.fn(' + args + ')');
    delete context.fn;
    return result;
}
```

思路：

先给context对象添加上要用的方法属性，再删除。注意call入参的个数不定，可以用eval解决（es6语法）。

apply实现类似。

参考：

https://github.com/mqyqingfeng/Blog/issues/12

## bind

```js
var foo = {
    value: 1
};

function bar(name, age) {
    console.log(this.value);
    console.log(name);
    console.log(age);

}

var bindFoo = bar.bind(foo, 'daisy'); // 可以这样直接调用，也可以用new关键字做构造函数
bindFoo('18');
// 1
// daisy
// 18
```



一句话介绍 bind:

> bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。(来自于 MDN )



### bind的模拟实现

```js
Function.prototype.mybind = function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);

    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        // 当作为构造函数时，this 指向实例，此时结果为 true，将绑定函数的 this 指向该实例，可以让实例获得来自绑定函数的值(如例子中的this.habit)（eg：如果改成 `this instanceof fBound ? null : context`，实例只是一个空对象）
        // 当作为普通函数时，this 指向 window，此时结果为 false，将绑定函数的 this 指向 context
        return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
    }
    // 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承绑定函数的原型中的值
    fBound.prototype = this.prototype;
    return fBound;
}
```

这里绑定函数指的是上面例子的`bar`函数，代码中的self。



更进一步改进：

1. 在上面写法中，我们直接将 fBound.prototype = this.prototype，我们直接修改 fBound.prototype 的时候，也会直接修改绑定函数(this.prototype)的 prototype。这个时候，我们可以通过一个空函数来进行中转。
2. 绑定函数必须是函数，进行function类型判断。



最终版本：

```js
Function.prototype.mybind = function (context) {

    if (typeof this !== "function") {
      throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
    }

    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);

    var fNOP = function () {};

    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(this instanceof fNOP ? this : context, args.concat(bindArgs));
    }

    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();
    return fBound;
}
```



> 【思考】多次bind：不管我们给函数 `bind` 几次，`fn` 中的 `this` 永远由第一次 `bind` 决定。（由于嵌套apply的源码决定）



参考：

<https://github.com/mqyqingfeng/Blog/issues/12>