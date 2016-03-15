### window.onload
```
function windowOnload (fn){
	var oldload;
	if(window.onload){
		oldload = window.onload;
		window.onload = function(){
			oldload();
			fn();
		}
	}
	else{
		window.onload = fn;
	}
}
```
### 获取url参数
```
$.getUrlParam = function(name) {
	var reg = new RegExp("(^|&)"+ name +"=([^&]*)(&|$)");
	var r = window.location.search.substr(1).match(reg);
	if (r != null) return unescape(r[2]); return null;
}
```
### 判断手机号
```
function isMobile(str) {
	var reg = /^(1)[0-9]{10}$/;
	return reg.test(str);
}
```
### 本地存储
```
var storage = {
	set: function(key, value) {					//保存数据至本地
		if(typeof(localStorage)=="undefined") {	
			return;
		}
		else {
			try {
				localStorage.setItem(key, value);
			}
			catch (e) {
				alert("Error:"+e);
			}
		}
	},
	get: function(key) {						//从本地获取保存数据
		if(typeof(localStorage)=="undefined") {
			return false;
		}
		else {
			try {
				return localStorage.getItem(key);
			}
			catch (e) {
				return false;
			}
		}
	}
};
```
### 判断数组
```
var isArray = function(value){
	return Object.prototype.toString.apply(value) === '[object Array]';
}
```
### 事件监听器
```
var myevent = {
	//页面加载完成执行
	readyEvent : function(fn){
		var oldload = window.onload;
		if(oldload){
			window.onload = function(){
				oldload();
				fn();
			}
		}
		else{
			window.onload = fn;
		}
	},

	// 添加事件监听
	addEvent : function(element, type, handler){
		if(element.addEventListener){
			element.addEventListener(type, handler, false);
		}
		else if(element.attachEvent){
			element.attachEvent('on' + type, function(){
				// attachEvent下this值是window, 所以要绑定this
				handler.call(element);
			})
		}
		//传统事件注册
		else{
			element['on' + type] = handler;
		}
	},

	// 移除事件
	removeEvent : function(element, type, handler){
		if(element.removeEventListener){
			element.removeEventListener(type, handler, false);
		}
		else if(element.detachEvent){
			element.detachEvent('on' + type, handler);
		}
		else{
			element['on' + type] = null;
		}
	},

	// 阻止事件
	stopPropagation : function(event){
		if(event.stopPropagation){
			event.stopPropagation();
		} else{
			event.cancelBubble = true;
		}
	},

	// 取消事件默认行为
	preventDefault : function(event){
		if(event.preventDefault){
			event.preventDefault();
		}else{
			event.returnValue = false;
		}
	},

	// 获取事件目标
	getTarget : function(event){
		return event.target || event.srcElement;
	},

	// 获取event对象的引用，取到事件的所有信息，确保随时能使用event；
	getEvent : function(e) {
		var ev = e || window.event;
		if (!ev) {
			var c = this.getEvent.caller;
			while (c) {
				ev = c.arguments[0];
				if (ev && Event == ev.constructor) {
					break;
				}
				c = c.caller;
			}
		}
		return ev;
	}
}
```