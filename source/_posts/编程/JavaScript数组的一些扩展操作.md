title: JavaScript数组的一些扩展操作
date: 2016-04-14
tags: JavaScript
categories: 编程
keywords: javascript,js数组操作
---

## 1.怎么判断一个变量是数组
在写代码的过程中，多多少少会遇到这种情况
```javascript
function isArray(arr) {
   return Object.prototype.toString.call(arr) === '[object Array]';
}
//其实jquery中$.isArray也是这么实现
```
原理:
(1)ECMA中对Object.prototype.toString的解释：
> * Get the [[Class]] property of this object.
> * Compute a string value by concatenating the three strings “[object “, Result (1), and “]”.
> * Return Result (2)
其实就是返回：'[object type]'

<!--more-->
(2)call(arr)改变了toString的this指向，所以Object.prototype.toString.call(arr)返回'[object Array]'
> * arr为null返回:    '[object Null]'
> * arr为1返回:       '[object Number]'
> * arr为true返回:    '[object Boolean]'
> * arr为'King'返回:  '[object String]'
> * arr为{age:20}返回:'[object Object]'
> * arr为函数返回:     '[object Function]'
> * arr为/^\d*$/返回: '[object RegExp]'
> * arr为日期对象返回: '[object Date]'
也就是说Object.prototype.toString.call()是可以判断任意数据类型的

## 2.删除数组中特定元素
主要会用到splice方法
```javascript
var arr = ['Tom','King','Lucy','Amy'];
function delFromArr(item,arr) {
  var index = arr.indexOf(item);
  index!=-1 ? arr.splice(index,1) : arr;
  return arr;
}
delFromArr('King',arr);
//此时arr
['Tom','Lucy','Amy']
```
扩展：JavaScript中splice、slice方法
> * [http://www.w3school.com.cn/jsref/jsref_splice.asp](splice方法)
> * [http://www.w3school.com.cn/jsref/jsref_slice_string.asp](slice方法)
> * 其实最大的区别，splice会对原数组进行操作直接改变原数组，而slice不会改变原数组

## 3.数组元素去重
用hash对象或者indexOf去实现去重是比较高效的一种方法,不推荐双层for循环
### (1)hash实现, O(n)
```javascript
var arr = ['Tom','King','King','Amy'];
function delRepeat(arr) {
  var hash = {},
      newArr = [];
  for(var i=0; i<arr.length; i++){
    if(!hash[arr[i]]){
      newArr.push(arr[i]);
      hash[arr[i]] = true;//关键
    }
  }
  return newArr;
}
delRepeat(arr);
//此时arr
['Tom','King','Amy']
```
### (2)indexOf实现, O(n)
```javascript
var arr = ['Tom','King','King','Amy'];
function delRepeat(arr) {
  var newArr = [];
  for(var i=0; i<arr.length; i++){
    if (newArr.indexOf(arr[i]) == -1) {
      newArr.push(arr[i]);  
    }
  }
  return newArr;
}
delRepeat(arr);
//此时arr
['Tom','King','Amy']
```
