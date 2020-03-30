## 尺寸

### 浏览器视口高度宽度、滚动条高度宽度、滚动条位置

当document.compatMode等于BackCompat（[Quirks](https://developer.mozilla.org/en/Quirks_Mode_and_Standards_Mode) mode 怪异模式）时，浏览器客户区宽度是document.body.clientWidth；
当document.compatMode等于CSS1Compat（Standard mode 标准兼容模式）时，浏览器客户区宽度是document.documentElement.clientWidth。

```js
var height = document.compatMode=="CSS1Compat" ? document.documentElement.clientHeight : document.body.clientHeight;
```

滚动条高度宽度（scrollWidth）、滚动条的Left（scrollLeft）、滚动条的Top也同理有不同模式判断。

> scrollLeft、scrollTop通常直接用window.scrollY、window.scrollX来写。



### getBoundingClientRect

返回元素的大小及其**相对于视口**的位置。（注意这里相对位置和绝对定位的设置完全不一样）

- top: 元素上边距离页面上边的距离
- left: 元素右边距离页面左边的距离
- right: 元素右边距离页面左边的距离
- bottom: 元素下边距离页面上边的距离
- width: 元素宽度
- height: 元素高度

> 如果你需要获得相对于整个网页左上角定位的属性值，那么只要给top、left属性值加上当前的滚动位置（通过window.scrollX和window.scrollY），这样就可以获取与当前的滚动位置无关的值。



## 滚动

```
window.scrollTo(x-coord,y-coord )
window.scrollY // 文档在垂直方向已滚动的像素值。兼容性写法见 https://developer.mozilla.org/zh-CN/docs/Web/API/window/scrollY
```

另外还有，scrollIntoView 与 scrollIntoViewIfNeeded ：<https://juejin.im/post/59d74afe5188257e8267b03f>



## 延伸

### 实现一个懒加载组件

todo