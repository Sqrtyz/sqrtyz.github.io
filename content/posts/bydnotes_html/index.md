---
title: 【杂记】前端初探：HTML
category: Notes
tag: Unfinished
date: 2024-01-01
---

> 前端三部曲之一。HTML 定义了网页的内容。整理自「菜鸟教程」。

### 1.0 什么是 HTML

HTML（HyperText Markup Language）是用来描述网页的一种语言。它不是一种编程语言，而是一种 **标记语言**。

标记语言是一套使用 **标记标签（markup tag）** 来描述网页的语言。

值得一提的是，HTML 语言可以使用在 Markdown 文档中。

### 1.1 HTML 的基本构成是标签

下面是一个可视化的 HTML 结构（请在 Light Mode 下查看）：

<div style="width:99%;border:1px solid grey;padding:3px;margin:0;background-color:#ddd">&lt;html&gt;
<div style="width:90%;border:1px solid grey;padding:3px;margin:20px">&lt;head&gt;
<div style="width:90%;border:1px solid grey;padding:5px;margin:20px">&lt;title&gt;页面标题&lt;/title&gt;
</div>
&lt;/head&gt;
</div>
<div style="width:90%;border:1px solid grey;padding:3px;margin:20px;background-color:#fff">&lt;body&gt;
<div style="width:90%;border:1px solid grey;padding:5px;margin:20px">&lt;h1&gt;这是一个标题&lt;/h1&gt;</div>
<div style="width:90%;border:1px solid grey;padding:5px;margin:20px">&lt;p&gt;这是一个段落。&lt;/p&gt;</div>
<div style="width:90%;border:1px solid grey;padding:5px;margin:20px">&lt;p&gt;这是另外一个段落。&lt;/p&gt;</div>
&lt;/body&gt;
</div>
&lt;/html&gt;
</div>

+ HTML 标签是由尖括号包围的关键词，比如 `<html>`。它通常是成对出现的，比如 `<b>` 和 `</b>`。标签对中的第一个标签称为 **开始标签**，第二个标签称为 **结束标签**。
+ 「HTML 标签」 和 「HTML 元素」通常都是描述同样的意思。
+ 没有内容的 HTML 元素被称为空元素，例如 `<br>`（表示换行）。在开始标签中添加斜杠，比如 `<br />`，是关闭空元素的正确方法。

### 1.2 常见的那些标签

#### 1) 大框架

|Tags|Functions|
|---|---|
|`<html></html>`|定义了整个 HTML 文档，它们位于最外围|
|`<head></head>`|包含了所有的头部标签元素；这之中可以插入脚本（JS）, 样式文件（CSS），及各种 meta 信息|
|`<body></body>`|定义了文档的主体|

#### 2) Header

|Tags|Functions|
|---|---|
|`<title></title>`|定义了文档的标题，就是选项卡上的玩意儿|
|`<style></style>`|定义了 HTML 文档的样式引用|
|`<meta></meta>`|定义了文档的一些基本的元数据|
|`<script></script>`|用于加载脚本文件（如 JavaScript）|
|`<link></link>`|定义了文档与外部资源之间的关系|

+ meta 示例：
    + 定义网站作者：`<meta name="author" content="mihoyo">`；
    + 每 30s 刷新当前页面：`<meta http-equiv="refresh" content="30">`；
    + 为搜索引擎定义关键词：`<meta name="keywords" content="HTML, CSS, XML, XHTML, JavaScript">`。

+ `<link></link>` 标签常用于链接到样式表，例如：
  
  ```html
  <head>
  <link rel="stylesheet" type="text/css" href="mystyle.css">
  </head>
  ```

#### 3) Body

Attributes 一栏显示了和标签相关的除 style 外的常用属性。

+ 常用的基础标签

  |Tags|Functions|Attributes|
  |---|---|---|
  |`<h1></h1>`|HTML 标题，对应 Markdown 中的 `#`；此外还有 h2~h6|/|
  |`<p></p>`|定义 HTML 段落|/|
  |`<a></a>`|定义 HTML 链接|`href=""` 表示链接内容；`target="_blank"` 表示在新标签页打开|
  |`<img />`|定义 HTML 图像（注意是空元素）|`src` 表示地址；`alt` 表示替代文本（加载失败显示）；`height` & `width` 表示规模|
  |`<br />`|换行|/|

+ 文本格式化

  |Tags|Functions|Attributes|
  |---|---|---|
  |`<b></b>` / `<strong></strong>`|加粗段落|/|
  |`<i></i>` / `<em></em>`|斜体段落|/|
  |`<sub></sub>`|下标|/|
  |`<sup></sup>`|上标|/|

关于「文本格式化」的更多标签，参见 [这里](https://www.runoob.com/html/html-formatting.html)。

+ HTML 列表

  |Tags|Functions|Attributes|
  |---|---|---|
  |`<ul></ul>`|定义无序列表|/|
  |`<ol></ol>`|定义有序列表|/|
  |`<li></li>`|定义有（无）序列表中的每一项|/|

+ HTML 表格