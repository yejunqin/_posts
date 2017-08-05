---
title: SVG学习笔记
date: 2015-10-19 14:22:33
tags:
---
### SVG
可缩放矢量图形(Scalable Vector Graphics，SVG)是一种用来描述二维矢量图形的XML标记语言。
### 为什么选择SVG来描绘图形？

 - SVG 是可伸缩的矢量图形，在浏览器中改变尺寸，其图形质量不会有所损失；
 - 相比其它位图，SVG图像文件更小，可压缩性更强；
 - SVG 可以被记事本等阅读器、搜索引擎访问；
 - SVG 图像中的文本是可选的，同时也是可复制的；
 - SVG 图像可以与DOM，CSS和JavaScript交互；
 - SVG 可以制作成整体或局部动画。
<!--more-->
 
### 浏览器兼容性
 |功能|Chrome|Firefox(Gecko)|Internet Explorer|Opera|Safari|
 |---|---|---|---|---|---|
 |基本支持|1.0|1.5(1.8)|9.0|8.0|3.0.4|

也就是说，SVG在现代浏览器及IE9.0+版本都能得到支持，如果不需要对低版本IE兼容,使用SVG是个很好的解决方案。
当然，如果需要兼容旧版本IE，又想用SVG，可以使用Polyfill来解决(遇到html5浏览器直接显示SVG，遇到旧版本浏览器(IE6,7,8)则改用Flash解析SVG图像)。
### [SVG练习]用SVG做一个圆环进度图
假如要制作这样一个
![enter image description here](http://7xnjm0.com1.z0.glb.clouddn.com/circle.jpg)
SVG代码如下:
``` javascript
<svg width="440" height="440">
    <circle cx="220" cy="220" r="170" stroke-width="50" stroke="#D1D3D7" fill="none"></circle>
    <circle cx="220" cy="220" r="170" stroke-width="50" stroke="#00A5E0" fill="none" stroke-dasharray="200 1069"></circle>
</svg>
```
`circle`是SVG的一个基本形状(还有直线`line`, 椭圆`ellipse`, 折线`polyline`, 多边形`polygon` 等等…);

SVG内的元素能定义属性, 用来描述形状.

 - `cx`：圆的中点的**x坐标**;
 - `cy` : 圆的中点的**y坐标**;
 - `r` : 圆的**半径**;
 - `stroke` : 定义**颜色**, 线/文本/元素轮廓颜色;
 - `skroke-width` : 定义**厚度**, 线/文本/元素轮廓厚度;
 - `stroke-dasharray` : 表示描边为虚线, 两个值, 第一个值表示虚线的宽度, 第二个值表示虚线之间的间距;
 - [更多属性参考](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Attribute)

实现圆环进度的重点是, 我们用两个圆环, 表示进度的圆环覆盖表示100%的圆环, 同时表示进度圆环的轮廓用虚线来表示, 虚线的长度设为进度的百分比, 再使虚线的间隔大于等于圆环的长度(实际上就是利用虚线的第一段来覆盖原始圆环).
![enter image description here](http://7xnjm0.com1.z0.glb.clouddn.com/circle2.jpg)

可以看到, 现在这个圆环有点问题, 就是它的起始位置不是由0°开始,而是由90°开始.那就就需要用到`transform`这个属性了.

利用`transform`可以对图形进行图像**变换, 旋转, 缩放, 移动**, 和**倾斜**. 关于更多`transform`属性的了解, 可参考这篇文章[理解SVG坐标系统和变换: transform属性](http://www.w3cplus.com/html5/svg-transformations.html).

ok, 加上`transform`属性,大功告成,代码如下:
``` javascript
<svg width="440" height="440">
    <circle cx="220" cy="220" r="170" stroke-width="50" stroke="#D1D3D7" fill="none"></circle>
    <circle cx="220" cy="220" r="170" stroke-width="50" stroke="#00A5E0" fill="none" transform="rotate(-90 220 220)" stroke-dasharray="200 1069"></circle>
</svg>
```
![enter image description here](http://7xnjm0.com1.z0.glb.clouddn.com/circle.jpg)

[demo地址](http://somedemo.github.io/SVG-circle-progress-bar/)

### [再做一个]用SVG做一个直线进度图
有了前一个例子的经验,换成直线也很简单. 代码如下:
``` javascript
<svg width="440" height="200" style="margin-left:100px;">
    <line x1="0" y1="0" x2="440" y2="0" stroke-width="50" stroke="#d1d3d7" fill="none"></line>
    <line x1="0" y1="0" x2="440" y2="0" stroke-width="50" stroke="#00a5e0" fill="none" stroke-dasharray="0 440">
</svg>
```
![enter image description here](http://7xnjm0.com1.z0.glb.clouddn.com/line.jpg)

原理是一样的.
[demo地址](http://somedemo.github.io/SVG-circle-progress-bar/)

---
