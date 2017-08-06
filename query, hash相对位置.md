---
title: url中query, hash的相对位置
date: 2017-08-06
---
### 问题
spm项目需要对页面所有url加上spm字段，如原本的url是`https://ezbuy.co.th/`，经过加工后便是`https://ezbuy.co.th/?ezspm=1.10000000.2.3.4`。
<!-- more -->
这里使用了一个生成query-string的方法：

```ts
export function genUrlWithSearch(url: string = "", params: {[name: string]: string | number | undefined}): string {
	if (url === undefined || url === "") {
		return "";
	}
	const searchString = convertParams(params);
	let startChat = "?";
	if (url.includes("?")) {
		startChat = url.endsWith("&") ? "" : "&";
	}
	return !!searchString ? `${url}${startChat}${searchString}` : url;
}
```
好像并没有什么问题，然而确实是有问题的。如果传入url有`hash`，那结果会是`https://ezbuy.co.th/#anchor?ezspm=1.10000000.2.3.4`。
`window.location.hash`的结果是`#anchor?ezspm=1.10000000.2.3.4`，很明显这会影响页面锚点的定位。而且`window.location.search`输出结果是空字符串`""`。


RFC对于路径，query，hash的规则：[https://tools.ietf.org/html/rfc3986#section-4.1](https://tools.ietf.org/html/rfc3986#section-4.1)
`relative-ref  = relative-part [ "?" query ] [ "#" fragment ]`
所以`query`需要在`hash`前面，否则引起解析错误，所以上述的方法需要对hash部分做一点处理。
### 解决
检测url是否有`hash`，如果有则取出来，放在生成的url的最后。

```ts
const hashReg = /#[^?]*/;
function genUrlWithSearch(url: string = "", params: {[name: string]: string | number | undefined}): string {
    if (url === undefined || url === "") {
        return "";
    }
    let postUrl = url;
    let hash = "";
    const hashResult = url.match(hashReg);
    if (hashResult !== null) {
        postUrl = url.replace(hashReg, "");
        hash = hashResult[0];
    }
	const searchString = convertParams(params);
	let startChat = "?";
	if (postUrl.includes("?")) {
		startChat = postUrl.endsWith("&") ? "" : "&";
	}
	return !!searchString ? `${postUrl}${startChat}${searchString}${hash}` : `${url}${hash}`;
}
```
`https://ezbuy.co.th/?ezspm=1.10000000.2.3.4#anchor`这样就没有问题了。


