---
title: 编写一个Vue组件
date: 2016-07-16
tag:

---
### 前言
最近在开发的项目（一个给汽配供应商使用的erp系统）中用上了Vue，在这学习并使用的过程中，**组件**的概念已经深入脑海，因为项目本身的某些需求比较特殊，样式也是特定的，加上基于Vue的ui库本身就很少，因此项目中用到的组件都是自己写的，一边参考别人是怎么写组件的，然后实现适用于自己项目的组件。
组件的基本格式可以参考Vue的[官方文档](http://cn.vuejs.org/guide/components.html)。
<!--more-->

### 写一个冒泡提示组件
#### 这个组件的功能
当用户进行某些操作之后，页面需要给出操作成功，或失败的提示，或错误的提示，或仅仅是提示一些信息，所以命名该组件为`vToast`
提示组件用起来大概是这样子的
![](https://lh3.googleusercontent.com/--tu6KCzlG4w/V4oZIOon_lI/AAAAAAAAAAM/OlaqIHFYHGc6vqnrIzwKJh-TBjiMoydxQCLcB/s0/toast.gif "toast.gif")
#### `html`代码
一个Vue文件形式的组件由3个标签组成，`Template`标签里包含该组件的html代码，`style`标签包含该组件的样式，`script`包含该组件的`javascript`代码。
html代码：

```
<template>
	<div class="v-toast" v-show="show" transition="v-toast">
		<div class="v-toast-wrap" :class="[type]">
			<span class="v-toast-inner">
				<i class="v-toast-ico">
				</i>
				<span class="v-toast-text">{{text}}</span>
			</span>
		</div>
	</div>
</template>
```
从结构看到，组件动态绑定了一个与`type`属性关联的**类名**，这是用来标识提示的类型，切换`class`；
同时绑定了`text`文本，用来显示提示内容，从父组件传入。
#### `javascript`代码
```
<script>
	export default {
		name: 'vToast',
		data(){
			return {
				text: '数据加载中...',
				type: 'warn',//'ok' 'warn' 'err' 'info'
				timeout: 1500,
				show: false,
				timer: null//储存计时器
			}
		},
		methods: {
			showTip({type = 'warn', word, timeout = 1500}){
				this.text = word
				this.type = type
				this.show = true
				this.timer = window.setTimeout(() => {
					this.show = false
				}, timeout)
			}
		},
		ready(){
			this.$on('toast',(param) => {
				window.clearTimeout(this.timer)
				this.showTip(param)
			})
		}
	}
</script>
```
组件初始化后，`data`中会有几个属性，其中`text`绑定文本，`type`绑定提示类型，`show`绑定组件的显示和隐藏状态，`timeout`绑定组件显示提示持续的事件。

这里还有个`timer`属性：每次父组件调用该组件，该组件根据传入参数设定类型、文本、持续时间，然后设置`show`显示，再利用`setTimeout`方法设置`show`属性为`false`使组件隐藏。那么如果当前这次冒泡提示尚未结束，用户又触发了一次冒泡提示，就会出现问题。
![](http://7xnjm0.com1.z0.glb.clouddn.com/toast-bug.gif)

 1. 触发了一次提示，紧接着触发第二次提示，却因为第一个提示的隐藏时间到了而关闭了；
 2. 第三次提示也提前关闭了。
原因是我们每一个提示用的都是同一个Vue实例，所以造成了`setTimeout`异步关闭错乱的问题。
所以每次触发提示时，将`setTimeout`方法的返回的回调`id`存到`timer`上，每一次触发提示前将上一次的回调清空。
#### 调用组件
我们在组件内定义了触发冒泡提示的方法，然后定义一个自定义监听事件来调用组件的方法。子组件和父组件是通过**dispatch(派发)**和**broadcast(广播)**来达到通信目的的。
所以在父组件这样调用：
```
this.$broadcast('toast', {type: 'ok', word: '添加成功'})
```

