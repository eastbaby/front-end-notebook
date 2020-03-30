# css

### 1. 定位方案

有三种：普通流、浮动、绝对定位

### 2. BFC(block formatting content)

<https://zhuanlan.zhihu.com/p/25321647>

BFC 就是块级格式上下文，是页面盒模型布局中的一种 CSS 渲染模式，相当于一个独立的容器，里面的元素和外部的元素相互不影响。

触发BFC的条件：

- body 根元素
- 浮动元素：float 除 none 以外的值
- 绝对定位元素：position (absolute、fixed)
- display 为 inline-block、table-cells、flex
- overflow 除了 visible 以外的值 (hidden、auto、scroll)

BFC的应用：

1. 解决margin叠加问题
   给第一个p元素添加一个父元素(overflow:hidden)，让它触发BFC。

2. 用于布局
   并列的两个div元素。原本将第一个float:left会叠加在第二个div上。用overflow:hidden触发第二个div元素的BFC之后，效果立马出现了（因为BFC的区域不会与float box叠加）,一个两栏布局就这么妥妥的搞定。

3. 用于清除浮动，计算BFC高度

   防止父元素的高度坍塌。

### 3. CSS选择器

- 猫头鹰选择器`+` （介绍<https://juejin.im/entry/5965c4c66fb9a06bc340b387>）

### 4. 盒子模型

Quirks怪异模式：`box-sizing：border-box`

标准模式：`box-sizing：content-box`



