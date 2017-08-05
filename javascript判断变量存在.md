---
title: Javascript判断变量存在的问题
date: 2016-03-29
tag:

---
### 报错了
之前写了一段代码，判断某个变量是否声明了，如果存在，则去做某一些事情，然后这段代码报错了。一时间没想起来`if` 语句是否能判断变量有没有被声明，因为之前判断变量是否为空时也是这么写的，查了资料之后才发现是不能这么写的。
```  javascript
if(getCLodop){
                LODOP = getCLodop();
                LODOP.SET_LICENSES("","","","");
            }
```
### 正确的写法
#### 写法一
```
if(typeof a === 'undefined'){
	doSomething();
}
```
`typeof` 对未声明的变量会返回 `'undefined'`
#### 写法二
假设我们在浏览器端，那么可以这么写
```
if(window.a){
	doSomething();
}
```
#### 写法三
根据写法二，可以变一下形式。
```
if(a in window){
	doSomething();
}
```
#### 写法四
```
if(a){
	var a = xxx;
}
```
这段代码不会报错，原因是Javascript的变量声明提升。
上面几种应该是是最常用的写法了，查了下资料发现还有非常多种的写法，参考阮一峰老师的文章[如何判断Javascript对象是否存在](http://www.ruanyifeng.com/blog/2011/05/how_to_judge_the_existence_of_a_global_object_in_javascript.html)
