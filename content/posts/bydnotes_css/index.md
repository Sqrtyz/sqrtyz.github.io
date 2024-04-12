---
title: 【杂记】前端初探：CSS
category: Notes
date: 2024-01-01
tag: Unfinished
---

> 前端三部曲之一。CSS 描述了网页的布局。整理自「菜鸟教程」。

### 2.0 什么是 CSS

CSS（Cascading Style Sheets，层叠样式表），是一种用来为结构化文档（如 HTML 文档或 XML 应用）添加样式（字体、间距和颜色等）的计算机语言，CSS 文件扩展名为 `.css` 。

通过使用 CSS 可以大大提升网页开发的工作效率。

### 2.1 CSS 选择器

如果要在 HTML 元素中设置 CSS 样式，需要在元素中设置一些选择器——**告诉网页在什么地方运用什么样的样式**。

+ 权重 1000：**内联样式表**

  在标签内部加上 `style=""` 属性即可。属性内部的条目以分号分隔。
  
  推荐在比较特殊的个别元素身上使用。
  
  ```html
  <h3 style="color:red;"> Redemption </h3>
  ```
+ 权重 100：**ID 选择器**

    如果我们想让 body 里面的代码看起来更紧凑一些…… 为何不在 head 中事先声明某个 ID 的样式呢？
  
    ```html
    <!DOCTYPE html> <html> <head>
        <meta charset="utf-8"> 
        <style>
        #para1
        {
            text-align:center;
            color:red;
        } 
        </style>
    </head>
    <body>
        <p id="para1">Hello World!</p>
        <p>这个段落不受该样式的影响。</p>
    </body></html>
    ```

  效果是，「Hello world」变成了居中红色文本。

+ 权重 10：**Class 选择器**

    如果想让一类元素都变成一个样式，试试 class 选择器。

    ```html
    <!DOCTYPE html> <html> <head>
        <meta charset="utf-8"> 
        <style>
        .fuck
        {
            text-align:center;
            color:red;
        } 
        </style>
    </head>
    <body>
        <p class="fuck">Hello World!</p>
        <p>这个段落不受该样式的影响。</p>
    </body></html>
    ```

  效果同上。

+ 权重 1：**HTML 标签选择器**
  
    ```html
    <!DOCTYPE html> <html> <head>
        <meta charset="utf-8"> 
        <style>
        p
        {
            text-align:center;
            color:red;
        } 
        </style>
    </head>
    <body>
        <p>Hello World!</p>
        <p>这个段落受该样式的影响。</p>
    </body> </html>
    ```

    效果是，两行字都居中变红了。

+ CSS 优先级法则：
  + **A** 选择器都有一个权值，权值越大越优先；
  + **B** 当权值相等时，后出现的样式表设置要优于先出现的样式表设置；
  + **C** 创作者的规则高于浏览者：即网页编写者设置的 CSS 样式的优先权高于浏览器所设置的样式；
  + **D** 继承的 CSS 样式不如后来指定的 CSS 样式；
  + **E** 在同一组属性设置中标有 `!important` 规则的优先级最大。关于 `!important` 的使用，后面会再次涉及。

### 2.2 CSS 的创建

插入 **样式表** 的方法有三种：

+ 外部样式表（External style sheet）
+ 内部样式表（Internal style sheet）
+ 内联样式（Inline style）

在 2.1 中，我们介绍的四种选择方式，给出的示例代码都属于 **内部样式表** 或者 **内联样式**。而我们最常用的其实是 **外部样式表**，也就是我们常见的 `.css` 文件。

当样式需要应用于很多页面时，外部样式表将是理想的选择。在使用外部样式表的情况下，我们可以通过改变一个文件来改变整个站点的外观。

**每个页面使用 `<link>` 标签即可链接到某个样式表，`<link>` 标签位于文档头部。具体如下：**

```html
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

+ `rel="stylesheet"` 表示这个链接关联的是一个样式表文档。它同时说明这个 link 将在文档初始化时被使用。
+ `type="text/css"` type 属性规定被链接资源的 MIME 类型。*根据试验这个属性没有的时候也能正常工作？*
+ `href="mystyle.css"` 告知了 CSS 的地址。
+ 注意 `<link>` 标签没有对应的结束标签。这意味着你不需要在结尾再来一个 `</link>`。

浏览器会从文件 `mystyle.css` 中读到样式声明，并根据它来格式文档。**你可以想象成，把 CSS 文件插入到 html 文件的 head > style 中。**

下面是一个样式表文件的例子：

```css
hr {color:sienna;}
p {margin-left:20px;}
body {background-image:url("/images/back40.gif");}
```

### 2.3 基本的 CSS 参数表

##### 1）背景 Background

|Keys|Example Values|Meaning|
|---|---|---|
|`background-color`|`#b0c4de`|设置背景颜色|
|`background-image`|`url('bg.png')`|设置背景图片|
|`background-repeat`|`repeat-x`|设置背景平铺方式|
|`background-attachment`|`no` / `fixed`|设置背景图像是否固定或者随着页面的其余部分滚动|
|`background-position`|`right top`|设置背景贴合位置|
  
当使用简写属性时，属性值的顺序为上述 Keys 的列顺序。例如：

```css
body {background:#ffffff url('img_tree.png') no-repeat right top;}
```
##### 2）文本 Text

|Keys|Example Values|Meaning|
|---|---|---|
|`color`|`rgb(255,0,0)`|设置文字颜色|
|`text-align`|`center` / `right` / `justify`（等宽）|文本对齐方式|
|`text-indent`|`3px`|设置文本缩进|
|`line-height`|`200%`|设置文字行高|
|`text-decoration`|`underline`（下划线）/ `blink`（闪烁）|向文本添加修饰|

更多文本属性参见 [这里](https://www.runoob.com/css/css-text.html)。

##### 3）字体 Font

|Keys|Example Values|Meaning|
|---|---|---|
|`font-family`|`"Times New Roman", Times, serif`|设置文本字体系列|
|`font-style`|`italic` / `normal`|设置字体样式属性|
|`font-size`|`14px` / `2.5em` / `90%`|设置文本字体大小|

提示：

+ `font-family` 属性应该设置几个字体名称作为一种「后备」机制，如果浏览器不支持第一种字体，将尝试下一种字体。
+ font-size 换算：px / 16 = em。
+ 同样的，可以使用 `font:15px arial,sans-serif;` 类似语句一次性设定某个文本的字体属性。

##### 4）链接 Link

我们定制链接的样式时，采用下列语句：

```html
<style>
    a:link {color:#000000;}      /* 未访问链接*/
    a:visited {color:#00FF00;}  /* 已访问链接 */
    a:hover {color:#FF00FF;}  /* 鼠标移动到链接上 */
    a:active {color:#0000FF;}  /* 鼠标点击时 */
</style>
```
`{}` 内部的内容和 2）3）通用。不同的是，在进行选择的时候，链接这一元素 `a` 被分为了四类：link, visited, hover & active。它们分别代表链接的四种状态，而我们也可以为这四种状态分别编写不同的样式。

##### 5）列表 List

让我们先复习一下有序列表和无序列表的语法。有序列表是

```html
<ol>
  <li>Coffee</li>
  <li>Tea</li>
</ol>
```

而无序列表是

```html
<ul>
  <li>Coffee</li>
  <li>Tea</li>
</ul>
```

|Keys|Example Values|Meaning|
|---|---|---|
|`list-style-type`|`circle` / `square`|确定 **无序** 列表的编号样式|
|`list-style-type`|`upper-roman` / `lower-alpha`|确定 **有序** 列表的编号样式|
|`list-style-image`|`url('a.gif')`|将无序列表的编号换成一个小图像|

##### 6）表格 Table



### 2.4 CSS 盒子模型

+ 盒子模型（Box Model）简介：
    + **Margin |** 清除边框外的区域，外边距是透明的。
    + **Border |** 围绕在内边距和内容外的边框。
    + **Padding |** 清除内容周围的区域，内边距是透明的。
    + **Content |** 盒子的内容，显示文本和图像。

  <p><img src="box-model.gif" width="40%"></p>
    
+ 盒子模型实例：
  
    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8"> 
    <style>
    div {
        background-color: lightgrey;
        width: 300px;
        border: 25px solid green;
        padding: 25px;
        margin: 25px;
    }
    </style>
    </head>
    <body>

    <h2>盒子模型演示</h2>
    <p>CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容。</p>
    <div>这里是盒子内的实际内容。有 25px 内间距，25px 外间距、25px 绿色边框。</div>

    </body>
    </html>
    ```
    
    运行结果如下（注意 margin 和 padding 是完全不可见的）：

    <div style="background-color: lightgrey;width: 300px;border: 25px solid green;padding: 25px;margin: 25px;">
    这里是盒子内的实际内容。有 25px 内间距，25px 外间距、25px 绿色边框。
    </div>