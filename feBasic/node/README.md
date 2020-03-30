### npm脚本通配符

npm 脚本就是 Shell 脚本，因为可以使用 Shell 通配符（非正则符号）。

> ```javascript
> "lint": "jshint *.js"
> "lint": "jshint **/*.js"
> ```

上面代码中，`*`表示任意文件名，`**`表示任意一层子目录。

如果要将通配符传入原始命令，防止被 Shell 转义，要将星号转义。

> ```javascript
> "test": "tap test/\*.js"
> ```



参考：<http://www.ruanyifeng.com/blog/2018/09/bash-wildcards.html>



### egg ts化

<https://github.com/whxaxes/egg-plugin-ts-demo>



### 其他

Node.js 练习题：<http://blog.acwong.org/2015/01/29/node-draw-number-on-head-image/>

七天node 见笔记

