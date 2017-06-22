title: jquery中serialize方法引起的中文乱码
date: 2016-03-29
tags: jQuery
categories: 编程

keywords: jQuery,serialize()
-----------------------------

### 1.发生的问题
在form表单提交时调用serialize()方法，提交成功后获取数据中文显示乱码(被编码)

### 2.怎么解决
其实之所以出现上述问题，是因为jQuery在调serialize方法时，会去调用encodeURIComponent对序列化后的文本进行编码，如果不想被编码，那么这样写即可：
```javascript
var param = decodeURIComponent($('form').serialize());
```
<!--more-->
### 3.扩展
我们提交表单或许通过ajax提交，参数需要的是json对象，下面我们来看看form表单怎么序列化成json对象：
```javascript
var param = decodeURIComponent($('form').serialize());
var getParam = function(href) {
	href = href || window.location.search;
	var data = {}, reg = /([^?=&]+)(=([^&]*))?/g;
	if (href) {
		href = decodeURIComponent(href);
		href.replace(reg, function($0, $1, $2, $3) {
			data[$1] = $3;
		});
	}
	return data;
};
//获取form表单json对象参数
var data = getParam(param);
```
