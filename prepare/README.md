- nvm -> nvs

<https://zhuanlan.zhihu.com/p/63403762>

- npm 的学习

<https://juejin.im/post/5ab3f77df265da2392364341>

只要有一个 lock 文件（里面制定了node_modules、package.json等内容），不管在那一台机器上执行 npm install 都会得到完全相同的 node_modules 结果。

- Devtools

<https://juejin.im/book/5bdc715fe51d454e755f75ef/section/5be927d06fb9a049d81b5fc0>

- package.json

学习各个含义：<https://zhuanlan.zhihu.com/p/33928507>

项目文件名字需要小写。

package.json中的script会安装一定顺序寻找命令对应位置，本地的node_modules/.bin路径就在这个寻找清单中，所以无论是全局还是局部安装的Webpack，你都不需要写前面那指明详细的路径了。

npm的start命令是一个特殊的脚本名称，其特殊性表现在，在命令行中使用`npm start`就可以执行其对于的命令，不用加`run`。