---
title: url中query, hash的相对位置
date: 2017-08-06
---
### 问题
spm项目需要对页面所有url加上spm字段，如原本的url是`https://ezbuy.co.th/`，经过加工后便是`https://ezbuy.co.th/?ezspm=1.10000000.2.3.4`。
<!-- more -->
这里使用了一个生成query-string的方法：

```ts
function genUrlWithSearch(url: string = "", params: {[name: string]: string | number | undefined}): string {
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
好像并没有什么问题，

