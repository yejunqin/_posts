---
title: snippet-CSS
date: 2015-10-22 18:26:14
tags:
---
### IE hack
``` css
.all IE{property:value\9;}  
.gte IE 8{property:value\0;}  
.lte IE 7{*property:value;}  
.IE 8/9{property:value\0;}  
.IE 9{property:value\9\0;}  
.IE 7{+property:value;}  
.IE 6{_property:value;}  
.not IE{property//:value;}
```
<!--more-->
### 透明度
``` css
background-color:#000; /*IE6~IE8识别背景色*/  
background-color: rgba(0, 0, 0, 0.8); /*此属性为css不透明度，兼容IE9+ chrome等高级浏览器*/  
filter:Alpha(opacity=80); /*IE6~IE8*/
```
### 单行文本溢出替换为省略号

```
overflow: hidden;  
text-overflow: ellipsis;  
white-space: nowrap;
```
### 多行文本
 1. `display: -webkit-box;`必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
 2. `-webkit-box-orient`必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。
 3. `text-overflow: ellipsis;`可以用来多行文本的情况下，用省略号“…”隐藏超出范围的文本 。

```
overflow : hidden;
text-overflow: ellipsis;
display: -webkit-box;
-webkit-line-clamp: 2;
-webkit-box-orient: vertical;
```

### 清除浮动
```
.clearfix:before,.clearfix:after {
    content:"";
    display:table;
}
.clearfix:after {
    clear:both;
    overflow:hidden;
}
.clearfix {
    zoom:1; /* for ie6 & ie7 */
}
```