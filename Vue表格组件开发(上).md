---
title: 基于Vue的Table组件-上(滚动加载、固定表头、鼠标拖拽改变宽度)
date: 2016-07-25
tag:

---
### 背景
最近开发的项目对于表格的使用要求很高，产品要求要有固定表头、能排序、左右滚动时能冻结某些列、隐藏某些列、滚动加载（也就是不要分页按钮）、拖拽改变宽度这些功能（我了个去）。项目是用Vue+webpack写的，基于Vue的有这些功能的组件暂时还没找到，所以就自己写一个了。
自己也基本上是一边开发业务模块，一边开发组件…到现在表格组件已经写的七七八八了，下面逐个整理一下自己是怎么实现的。
### 滚动加载
<!--more-->
要点：

 1. 监听`scroll`事件，计算是否滚动到底部；
 2. 节流函数解决短时间内多次触发`scroll`事件的问题

其实本质上跟后台请求时还是按照分页来的(滚动加载一次就请求下一页的数据)。
#### 计算是否到了底部
``` javascript
scroll(e){
  throttle(function(){
    const target = e.target
    //计算是否滚动到底并且不是横向滚动
    const {clientHeight: height, scrollHeight, scrollTop, scrollLeft} = target
    if(height + Math.ceil(scrollTop) >= scrollHeight){
      this.loadData(e)
    }
  }, 100, this) 
}
```

 1. `clientHeight`：元素的高度
 2. `scrollTop`：设置或者获取一个元素距离他容器顶部的像素距离，通俗点讲，就是获取或设置元素已经滚动了的距离（这里是垂直距离，相应的`scrollLeft`指已经滚动了的水平距离）
 3. `scrollHeight`：这是个只读属性，返回一个元素的**完整高度**

如下图：
![scrollHeight](https://developer.mozilla.org/@api/deki/files/840/=ScrollHeight.png)
假如一个元素是滚动的，scrollHeight即为它的全部高度（但不包含margin）
所以只要计算**容器高度 + 已经滚动的高度 >= 容器的全部高度**，满足表示到了底部。用`Math.ceil`对`scrollTop`向上去整避免因为小数位的误差导致不满足条件
#### 节流函数
``` javascript
 /**
   * 节流函数，用于短时间内连续触发大量dom计算
   * @param  {[Function]} method  [需要调用的函数]
   * @param  {[Number]} timeout [定时器时长, 毫秒]
   * @param  {[Object]} context [指定函数的上下文，如果没有，则上下文是全局作用域]
   */
var throttle = function(){
  let timeoutId
  return function(method, timeout = 100, context = window){
    clearTimeout(timeoutId)
    timeoutId = setTimeout(function(){
      method.call(context)
    }, timeout)
  }
}()
```
通过**函数节流**可以避免不必要的dom计算
### 固定表头
关于如何在滚动表格时固定表头，就像这样
![frozenHead](http://7xnjm0.com1.z0.glb.clouddn.com/frozen-head.gif)
以前做过一个项目也有这种需求，当时采用的策略是：
向下滚动表格时，把`thead`改为绝对定位，固定在表格最上方。不过当时做完后体验不是很好（大概就是改变定位时会闪一下）
现在采用的策略是：**渲染两个`table`**，表头一个，内容一个，这样滚动内容表格时，自然不会影响到表头。
### 鼠标拖拽
这个表格组件还有个需求，就是可以通过鼠标拖拽改变列的宽度。首先要知道什么是 拖放：用户点击某个对象，并按住鼠标不放，将鼠标移动到另一个地方，释放鼠标按钮。也就是：鼠标按下→鼠标移动→鼠标释放这么一个过程。
因此，需要监听3个事件：`mousedown`、`mousemove`、`mouseup`。

