> 收纳 webpack 4.0 知识
>
> - [x] 2019.12 webpack掘金小册 <https://juejin.im/book/5a6abad5518825733c144469> 



## 基础概念

### 1. 关键词

#### entry

如果是单页面应用，那么可能入口只有一个；如果是多个页面的项目，那么经常是一个页面会对应一个构建入口。

#### loader

 loader 理解为是一个转换器，负责把某种文件格式的内容转换成 webpack 可以支持打包的模块。

同一个rule下，loader执行顺序是从最后配置的 loader 开始，**一步步往前**。

- 最后的 loader 最早调用，传入原始的资源内容（可能是代码，也可能是二进制文件，用 buffer 处理）
- 第一个 loader 最后调用，期望返回是 JS 代码和 sourcemap 对象（可选）
- 中间的 loader 执行时，传入的是上一个 loader 执行的结果

```js
module: {
  // ...
  rules: [
    {
      test: /\.jsx?/, // 匹配文件路径的正则表达式，通常我们都是匹配文件类型后缀
      include: [
        path.resolve(__dirname, 'src') // 指定哪些路径下的文件需要经过 loader 处理
      ],
      use: 'babel-loader', // 指定使用的 loader
      type: 'javascript/esm', // 这里指定模块类型
    },
  ],
}
```

#### plugin

模块代码转换的工作由 loader 来处理，**除此之外的其他任何工作**都可以交由 plugin 来完成。多用于**构建**。

1. uglifyjs-webpack-plugin

   webpack4.x，Uglifyjs在production模式下有集成，不需要额外引入plugin。

2. definePlugin（内置）

   definePlugin使用的时候，建议使用` process.env.NODE_ENV: ... `的方式来定义 `process.env.NODE_ENV`，而不是使用 `process: { env: { NODE_ENV: ... } } `的方式，因为这样会覆盖掉 process 这个对象，可能会对其他代码造成影响。

3. ExtractTextWebPackPlugin(webpack
   webpack 4更换成mini-css-extract-plugin，用于output生成css文件)

4. CopyWebpackPlugin

   有些文件没经过 webpack 处理，但是我们希望它们也能出现在 build 目录下，这时就可以使用 CopyWebpackPlugin 来处理了。

5. ProvidePlugin（内置）

   该组件用于引用某些模块作为应用运行时的变量，从而不必每次都用 `require` 或者 `import`。

6. IgnorePlugin（内置）

   ` new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)`

#### output

```js
// 路径中使用 hash，每次构建时会有一个不同 hash 值，避免发布新版本时线上使用浏览器缓存
module.exports = {
  // ...
  output: {
    filename: '[name].js',
    path: __dirname + '/dist/[hash]',
  },
}
```

使用webpack生成一个或多个包含你源代码最终版本的“打包好的文件”（[bundles](https://webpack.js.org/glossary/#b)），（概念上我们当作）它们由（一个一个的）[chunks](https://webpack.js.org/glossary/#c)组成。

### 2. 模块解析

resolve 字段。

webpack 中有一个很关键的模块 [enhanced-resolve](https://github.com/webpack/enhanced-resolve/) 就是处理依赖模块路径的解析的，这个模块可以说是 Node.js 那一套模块路径解析的增强版本。

resolve.alias、resolve.extensions (后缀可以省略）、resolve.modules（可以简化模块的查找）、resolve.mainFields、

## 常用配置

### 1. 关联页面

- html-webpack-plugin

构建时 html-webpack-plugin 会为我们创建一个 HTML 文件，其中会引用构建出来的 JS 文件。通过 html-webpack-plugin 就可以将我们的页面和构建 JS 关联起来，从页面（html）开始开发。

eg：<https://github.com/xiezipei/webpack-start/tree/master/05-webpack-html-template>

该插件还可以对css和html进行压缩。

### 2. css

css-loader、style-loader；mini-css-extract-plugin（用于抽离css文件）。

- css-loader 负责解析 CSS 代码，主要是为了处理 CSS 中的依赖，例如 `@import` 和 `url()` 等引用外部文件的声明；
- style-loader 会将 css-loader 解析的结果转变成 JS 代码，运行时动态插入 `style` 标签来让 CSS 代码生效。

### 3. 图片

file-loader、url-loader

[url-loader](https://github.com/webpack-contrib/url-loader) 和 [file-loader](https://github.com/webpack-contrib/file-loader) 的功能类似，但是在处理文件的时候，可以通过配置指定一个大小，当文件小于这个配置值时，url-loader 会将其转换为一个 base64 编码的 DataURL。

### 4. clean-webpack-plugin

由于用了 `hash` 命名资源文件，所以每次构建打包都不会替换原来的文件，这里我们就需要用到 `clean-webpack-plugin` 来清理目录。

### 5. 本地调试


**关于webpack-dev-server**（基于express开发的）：

devServer字段。

有几个配置选项：public（指定域名）、port、publicPath（建议和output设置一致）、proxy（比如可以把target代理到本机，本地联调很有用）

**关于webpack-dev-middleware**：

webpack-dev-middleware 的好处是可以在既有的 Express 代码基础上快速添加 webpack-dev-server 的功能，同时利用 Express 来根据需要添加更多的功能，如 mock 服务、代理 API 请求等。

以上两个概念的区别：<https://stackoverflow.com/questions/42294827/webpack-vs-webpack-dev-server-vs-webpack-dev-middleware-vs-webpack-hot-middlewar>

webpack-dev-server就是一个Express 和 webpack-dev-middleware的实现。

**mock服务**：

webpack-dev-server 的 `before` 或 `proxy` 配置，又或者是 webpack-dev-middleware 结合 Express，都可以帮助我们来实现简单的 mock 服务。eg：https://github.com/teabyii/webpack-examples/blob/1651405feaaf3f4fa531677630dece51b38f182b/details/webpack.config.js#L94

### 6. 区分环境（prod/dev）

**webpack 3.x做法：**

由于webpack 的运行时环境是 Node.js，我们可以通过 Node.js 提供的机制给要运行的 webpack 程序传递环境变量，来控制不同环境下的构建行为。

```js
// package.json
{
  "scripts": {
    "build": "NODE_ENV=production webpack",
    "develop": "NODE_ENV=development webpack-dev-server"
  }
}

// webpack配置

 new webpack.DefinePlugin({
      // webpack 3.x 一定要用definePlugin定义
      'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
    }),
...
if (process.env.NODE_ENV === 'production') { xxx
```

**webpack 4.x做法（兼容3.x 做法）**：

具体用法见官方文档：<https://webpack.docschina.org/concepts/mode/>

```js
// package.json
"script": {
    "build": "webpack --mode production",
    "start": "webpack-dev-server --mode development"
}
// webpack配置
argv.mode === 'production' ? xxx
OR 
if (process.env.NODE_ENV === 'production') { xxx
```

webpack 4.x 的 mode 已经提供了差异配置的大部分功能。比如mode 为 production 时默认使用 JS 代码压缩，而 mode 为 development 时默认启用 hot reload，等等。

> 可以使用cross-env库帮助设置。

**webpack-merge**：

[webpack-merge](https://github.com/survivejs/webpack-merge)



## HMR

Hot Module Replacement。热替换。（热更新与热替换不一样。热更新是更新整个页面，热替换是替换修改的某个模块）

**如果在 webpack-dev-server 的启动中配置了 hot 为 true，就不需要手动引入 HotModuleReplacementPlugin 插件了。**

webpack 内部运行时，会维护一份用于管理构建代码时各个**模块**之间交互的**表数据**，webpack 官方称之为 **Manifest**，其中包括入口代码文件和构建出来的 bundle 文件的对应关系。可以使用 [WebpackManifestPlugin](https://github.com/danethurber/webpack-manifest-plugin) 插件来输出这样的一份数据。

开启了 hot 功能的 webpack 会往我们应用的主要代码中添加 WS 相关的代码，用于和服务器保持连接，等待更新动作。

项目里可以用`module.hot` API 操作热更新的api。例子可以看 ：<https://webpack.js.org/guides/hot-module-replacement/#other-code-and-frameworks>



## 优化前端资源加载

### 1. 图片、压缩

css sprites、image-webpack-loader（利用[imagemin](https://github.com/imagemin) 做图片压缩）、svg-sprite-loader

### 2. 浏览器缓存、分离代码文件

分离css抽出来打包的好处是：一，避免“当仅仅改变css就要重新加载整个js"；二，css可以在多个页面间共享，非首次的页面加载的时候可以拿到css缓存。

除了公共的 CSS 文件或者图片资源等，当我们的 JS 代码文件过大的时候，也可以用代码文件拆分的办法来进行优化。

建议将公共使用的第三方类库（如react、angular、或者整个npm包）显式地配置为公共的部分，而不是 webpack 自己去判断处理。因为公共的第三方类库通常升级频率相对低一些，这样可以避免因公共 chunk 的频繁变更而导致缓存失效。

```js
// webpack 4.x 用optimization/splitChunks实现
module.exports = {
  optimization: {
    splitChunks: {
      cacheGroups: {
        vendor: {
          chunks: "initial",
          test: path.resolve(__dirname, "node_modules") // 路径在 node_modules 目录下的都作为公共部分
          name: "vendor", // 使用 vendor 入口作为公共部分
          enforce: true,
        },
      },
    },
  },
}
module.exports = {
  // ... webpack 配置

  optimization: {
    splitChunks: {
      //  Providing all can be particularly powerful, because it means that chunks can be shared even between async and non-async chunks.
      chunks: 'all'
    },
    // 将webpack运行时生成代码打包到runtime.js
    runtimeChunk: true
  },
}
```

cacheGroups其实是splitChunks里面最核心的配置。splitChunks就是根据cacheGroups去拆分模块的。

默认的cacheGroups配置会将`node_modules`中的所有模块分配到一个名为`vendors`的缓存组；并将所有被引用2次及以上的模块分配到一个名为`default`的变更组。

详细参考官方文档：<https://webpack.docschina.org/plugins/split-chunks-plugin/#src/components/Sidebar/Sidebar.jsx>

### 3. 按需动态加载

在 webpack 的构建环境中，遵循 ES 标准的动态加载语法 [dynamic-import](https://github.com/tc39/proposal-dynamic-import) 来编写代码即可，webpack 会自动处理使用该语法编写的模块。如果你使用了 [Babel](http://babeljs.io/) 的话，还需要 [Syntax Dynamic Import](https://babeljs.io/docs/plugins/syntax-dynamic-import/) 这个 Babel 插件来处理 `import()` 这种语法。另外，需要添加 promise 的 [polyfill](https://github.com/stefanpenner/es6-promise) 。

webpack 构建时会自动把 lodash 模块分离出来，并且在代码内部实现动态加载 lodash 的功能。动态加载代码时依赖于网络，其模块内容会**异步**返回，所以 `import` 方法是返回一个 **promise** 来获取动态加载的模块内容。

```js
// import 作为一个方法使用，传入模块名即可，返回一个 promise 来获取模块暴露的对象
// 注释 webpackChunkName: "lodash" 可以用于指定 chunk 的名称，在输出文件时有用
import(/* webpackChunkName: "lodash" */ 'lodash').then((_) => { 
  console.log(_.lash([1, 2, 3])) // 打印 3
})

------

output: {
  path: path.resolve(__dirname, 'dist'),
  filename: '[name].[hash:8].js',
  chunkFilename: '[name].[hash:8].js' // 指定分离出来的代码文件的名称
},
```

### 4. Tree Shaking

起源于rollup，es6的静态模块特性。

```js
// .babelrc
{
  "presets": [["env", { "modules": false }]] // 这样可以把 import/export 的这一部分模块语法交由 webpack 处理，否则没法使用 Tree shaking 的优化。
}
```

tree shaking 优秀文章：

1. 你的tree shaking没有用 <https://zhuanlan.zhihu.com/p/32831172> （补充：这篇文章最近没有更新，但是 uglify 的相关 issue [Class declaration in IIFE considered as side effect](https://github.com/mishoo/UglifyJS2/issues/1261) 是有进展的，现在如果你在 Babel 配置中增加 `"loose": true` 配置的话，`Person` 这一块代码就可以在构建时移除掉了。）

**sideEffects**

当某个模块的 `package.json` 文件中有了`sideEffects: false`这个声明之后，webpack 会认为这个模块没有任何副作用，只是单纯用来对外暴露模块使用，那么在打包的时候就会做一些额外的处理。



##提升构建速度

避免本地开发环境启动时候过慢。

1. 减少 `resolve` 的解析
2. 把 loader 应用的文件范围缩小
3. 减少 没必要的plugin 
4. 图片压缩可以在pre-commit的时候处理（避免用image-webpack-loader造成速度降低）
5. [DLLPlugin](https://doc.webpack-china.org/plugins/dll-plugin) 是 webpack 官方提供的一个插件，也是用来分离代码的。（术语 ddl：动态链接库）

**4.x 的构建性能对比 3.x 是有很显著的提高。**



## webpack内部原理

webpack 本质上就是一个 JS Module Bundler，用于将多个代码模块进行打包。

bundler 从一个构建入口出发，解析代码，分析出代码模块依赖关系，然后将依赖的代码模块组合在一起，在 JavaScript bundler 中，还需要提供一些胶水代码让多个代码模块可以协同工作，相互引用。

webpack 会利用 JavaScript Function 的特性提供一些代码来将各个模块整合到一起，即是将每一个模块包装成一个 JS Function，提供一个引用依赖模块的方法，如下面例子中的 `__webpack__require__`，这样做，既可以避免变量相互干扰，又能够有效控制执行顺序。

简单例子：

```js
// 分别将各个依赖模块的代码用 modules 的方式组织起来打包成一个文件
// entry.js
modules['./entry.js'] = function() {
  const { bar } = __webpack__require__('./bar.js')
}

// bar.js
modules['./bar.js'] = function() {
  const foo = __webpack__require__('./foo.js')
};
...

// 已经执行的代码模块结果会保存在这里
const installedModules = {}

function __webpack__require__(id) {
  // ... 
  // 如果 installedModules 中有就直接获取
  // 没有的话从 modules 中获取 function 然后执行，将结果缓存在 installedModules 中然后返回结果
}
```

```js
创建 Compiler -> 
调用 compiler.run 开始构建 ->
创建 Compilation -> 
基于配置开始创建 Chunk -> 
使用 Parser 从 Chunk 开始解析依赖 -> 
使用 Module 和 Dependency 管理代码模块相互关系 -> 
使用 Template 基于 Compilation 的数据生成结果代码
```

webpack 主要的构建处理方法都在 `Compilation` 中，我们要了解 loader 和 plugin 的机制，就要深入 `Compilation` 这一部分的内容。



优秀文章：

1. 深入理解 webpack 文件打包机制 <https://github.com/happylindz/blog/issues/6>
2. <https://juejin.im/book/5a6abad5518825733c144469/section/5a6abcdc51882535a5546e8e>



## 创建自己的loader/plugin

### 1. loader

我们可以在 webpack 配置中直接使用路径来指定使用本地的 loader（将rules中的loader指定为路径）; 或者在 loader 路径解析中加入本地开发 loader 的目录：

```js
// 在 resolveLoader 中添加本地开发的 loaders 存放路径
// 如果你同时需要开发多个 loader，那么这个方式会更加适合你
resolveLoader: {
  modules: [
    'node_modules',
    path.resolver(__dirname, 'loaders')
  ],
},
```

**同步loader**

eg：markdown-loader。基本上，webpack loader 都是基于一个实现核心功能的类库来开发的（比如markdown-loader基于marked)

```js
"use strict";

const marked = require("marked");
const loaderUtils = require("loader-utils");

module.exports = function (markdown) {
    // 使用 loaderUtils 来获取 loader 的配置项
    // this 是构建运行时的一些上下文信息
    const options = loaderUtils.getOptions(this);

    this.cacheable();

    // 把配置项直接传递给 marked
    marked.setOptions(options);

    // 使用 marked 处理 markdown 字符串，然后返回
    return marked(markdown);
};
```

**异步loader**

<https://webpack.docschina.org/api/loaders/#%E5%BC%82%E6%AD%A5-loader>

**Pitch（跳过）**

<https://webpack.docschina.org/api/loaders/#%E8%B6%8A%E8%BF%87-loader-pitching-loader->



### 2. plugin

 plugin 实例中最重要的方法是 `apply`，该方法在 webpack compiler 安装插件时会被调用一次，`apply` 接收 webpack compiler 对象实例的引用，你可以在 compiler 对象实例上注册各种事件钩子函数，来影响 webpack 的所有构建流程，以便完成更多其他的构建任务。

```js
class FileListPlugin {
  constructor(options) {}

  apply(compiler) {
    // 在 compiler 的 emit hook 中注册一个方法，当 webpack 执行到该阶段时会调用这个方法
    compiler.hooks.emit.tap('FileListPlugin', (compilation) => {
        ...
  })
}
```



当开发 plugin 需要时，我们可以查阅官方文档中提供的事件钩子列表：[compiler 的事件钩子](https://doc.webpack-china.org/api/compiler/#%E4%BA%8B%E4%BB%B6%E9%92%A9%E5%AD%90) 和 [compilation 的事件钩子](https://doc.webpack-china.org/api/compilation/)。(钩子特别多)。钩子 基于  [tapable](https://github.com/webpack/tapable) 这个工具库。

事件钩子的分类：

- 同步/异步
- 并行 parallel
- bail
- waterfall
- ...



## 个人项目经验

设置 alias 避免引用无法识别。 <https://github.com/umijs/umi/issues/1109#issuecomment-423380125>





## 参考

深入浅出webpack 电子书/实体书 <https://webpack.wuhaolin.cn/>

webpack掘金小册 <https://juejin.im/book/5a6abad5518825733c144469> (配套部分代码<https://github.com/xiezipei/webpack-start>)

一步一步 <https://juejin.im/post/5de06aa851882572d672c1ad>

webpack官方demo库：<https://github.com/webpack/webpack/tree/master/examples>