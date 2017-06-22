title: Web应用log输出留'后门'
date: 2017-04-23
tags: log
categories: 编程
keywords: log输出
---

## 一、log输出现状
![线上log](log_1.png)
* 在我们调试代码的时候有时候会加入一些日志输出，一般用console.log()去打log，一般在上线的时候会删除
* 但对于一些逻辑复杂的代码，我们希望保留，便于线上追踪代码和定位一些问题，但又不希望用户在控制台看到log输出
<!--more-->
## 二、'后门'应便于开启/关闭
* 顾名思义，‘后门’是留给特定人员的，也就是开发者，我们希望做一个类似开关的东西，易于开启
* 比如脉脉APP【我->设置->关于脉脉→长按意见反馈，会弹出Log查看入口】，PC也可以做类似的入口
![脉脉](log_2.png)

## 三、开发npm包es-log
* npm install es-log --save
```javascript
var Log = require('es-log'); //es5
import Log from 'es-log';    //es6
```
* 方法调用
```javascript
Log.log   <===> console.log
Log.info  <===> console.info
Log.warn  <===> console.warn
Log.error <===> console.error
此外，加了Log.line方法，主要用来输出字符行做log块的分隔：
Log.line('#');
输出：
##########################################################
Log.line('=');
输出：
==========================================================
```
## 四、实例应用
* 默认关闭
![默认关闭](log_3.png)
* 开启：url加参数logger=1
![开启](log_4.png)
