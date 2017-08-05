---
title: ES6学习笔记
date: 2016-07-20
tag: 

---
最近的项目终于用上了ES6，感觉真的很好，写起来舒服了很多。目前用到的大部分是被频繁提起的特性，如**箭头函数**、**解构赋值**、**let, const命令**……等等。
真正写es6之前，我是在阮一峰的[es6入门](http://es6.ruanyifeng.com/)和Babel的[es6教程](https://babeljs.io/docs/learn-es2015/)学习语法的。
下面对这段时间使用的es6做一个归纳整理。
<!--more-->
### let, const 命令
#### 块级作用域
在es5里，我们用`var`命令在声明变量，但var命令的作用域是函数作用域，也就是说通过var声明的变量在声明它的函数内都是有效的。
es6新增了`let`命令，不同的是，声明的变量，只在声明它的**代码块**内有效，称之为**块级作用域**。
``` javascript
if(true){
	let a = 0;
	var b = 1;
}
a //ReferenceError: a is not defined(…)
b //1
```
在书上、或面试题中，肯定见过这样的题
``` 
var foo = []
for(var i = 0; i < 10; i++){
	foo[i] = function(){
		console.log(i)
	}
}
foo[3]() // 10
```
这是函数作用域的锅，因为for循环里面的i都是同一个变量，当循环结束后，i的值是10。
当然，可以用闭包解决这个问题
```
var foo = []
for(var i = 0; i < 10; i++){
	foo[i] = function(i){
		return function(){
			console.log(i)
		}
	}(i)
}
foo[3]() // 3
```
在es6里面，基本上就是用let命令代替var
```
var foo = []
for(let i = 0; i < 10; i++){
	foo[i] = function(){
		console.log(i)
	}
}
foo[3]() // 3
```
let声明的变量只在当次循环有效，因此每次循环声明的都是新的变量
#### 没有变量提升
let不像var那样存在变量提升，因此以下代码会报错
```
console.log(a) // undefined
console.log(b) // ReferenceError: b is not defined
var a
let b
```
所以要使用let变量必须先声明，否则会抛出错误
#### const命令
`const`用于声明一个只读的常量，一旦声明，常量的值不能改变。
```
const abc = 0
abc = 1 //TypeError: Assignment to constant variable
```
并且const声明变量时必须同时赋一个初始值，不能之后再给初始值
```
const abc
//SyntaxError: Missing initializer in const declaration
```
#### 小结

 1. 用let命令替代var命令来声明变量
 2. 用const命令声明常量
 3. 尽量使用const声明函数

### 箭头函数
使用函数时，可以用箭头函数这种简写的形式
```
arrs.forEach(val => val + 1)

//es5
arrs.forEach(function(val){
	return val + 1
})

var add = (a, b) => {
	return a + b
}
//es5
var add = function(a, b){
	return a + b
}
```
需注意几点：

 1. 传入参数只有一个时，可以省略圆括号，没有参数或多于一个时，需要圆括号；
 2. 箭头函数接受一个表达式或语句块，函数内容是表达式时，不需要花括号，并且函数会直接返回表达式的值；内容是语句块时，需要花括号；
#### **箭头函数默认绑定外部作用域**
以前需要将this传入调用的方法内，一般都这么写
```
var self = this
foo(arg, function(){
	self...
})
```
箭头函数内将作用域绑定至包含它的函数的作用域，即this值的指向与外部是一致的
```
var self = this
foo(arg, val => {
	this === self // true
	this...
})

```
因此再也不需要写`var  self = this`这种丑陋的代码了
### 增强的对象字面量表达式
es6规定了使用**字面量**形式定义对象时的一些特性
#### 属性简写
``` javascript
let foo = 'abc'
let obj = {
	foo
}
//es5
var obj = {
	foo: foo
}
```
#### 方法简写
对象的属性为一个函数时，提供简写形式。注意，**方法简写并不会绑定this值到外层**
``` javascript
let obj = {
	fun(x){
		return x + 1 
	}
}
//es5
var obj = {
	fun: function(x){
		return x + 1
	}
}
```
#### 属性名表达式
定义对象属性有两种方法
```
var b = 'c'
//方法一
foo.a = '123'
//方法二
foo['a' + b] = '456'
```
一是`.`后面直接跟属性名，二是方括号内放入表达式，表达式的值将会作为对象的属性名
但是如果用**字面量**形式定义属性，则只能使用方法一
ES6允许**字面量**形式定义属性时，也可以使用表达式定义属性
```
let b = 'c'
let key = 'oneProp'
let obj = {
	['a' + b] : '456',
	[key] : 456
}
```
### 模版字符串
#### 字符串内嵌入变量
我相信拼接字符串应该是前端er都很烦的事情， 例如
```
if(!data.success){
	var message = '请求失败！' + data.msg + '，请稍后重试'
}
```
es6加入了模版字符串，它是增强版的字符串，用**反引号(`)**标识
```
if(!data.success){
	var message = `请求失败！${data.msg}，请稍后重试`
}
```
拼接的内容少还好，一但多起来，就能体会到模版字符串带来的方便了。
#### 多行字符串
es6还允许输入多行字符串，同样是用**反引号(`)**标识
``` javascript
var msg = `this is line 1
		   this is line 2`
//es5
var html =  'this is line 1' +
			'this is line 2'
```
### 解构赋值
ES6允许按照一定模式，从数组或对象中提取值， 对变量进行赋值，这就是解构。
#### 数组的解构
我们以前是这么进行赋值的
``` javascript
var a = 1
var b = 2
var c = 3
```
通过解构赋值，可以这样
```
var [a, b, c] = [1, 2, 3]
var [a, , c] = [1, 2, 3]
a//1
c//3
var [a, b, c] = [1, 2]
a//1
b//2
c//undefined
```
如果解构不成功，则它的值会变为`undefined`
解构赋值还允许指定默认值
``` javascript
let [a, b = 1] = [2]
a// 2
b // 1
```
#### 对象的解构
解构除了可以用在数组上，也可以以对象的形式解构。
```
var {foo, bar} = {foo: 1, bar: 2}
foo //1
var //2
```
和数组的解构赋值不一样的地方是，数组的解构按照顺序进行匹配，而对象的解构是按照**属性名**进行解构的。
```
var {bar, foo} = {foo:1, bar:2}
bar //2
foo //1
```
也就是说，es6会把对象的属性赋值给同名的变量。

如果变量名和解构的属性名不一致，则需要指定
```
var {foo : pop} = {foo: '123', bar: '456'}
foo // error, not defined
pop //'123'
```
以上说明，现在对象中找到foo属性，然后把foo属性的值赋值给pop变量。也就是说，解构赋值在内部先找到同名的属性，然后把属性的值赋给变量。

在函数传参或返回值的时候，也可以用解构。
``` javascript
function foo({pop, bar}){
	console.log(pop, bar)
}
foo({
	pop: 'aaa',
	bar: 'bbb'
})
```

还可以指定默认值
``` javascript
function foo({x, y, z = 10}){
	return x + y + z
}
```
是否取默认值，取决于`z === undefined`，也就是说，当没有传`z`参数，或传了`undefined`，取默认值，其他情况则无视默认值