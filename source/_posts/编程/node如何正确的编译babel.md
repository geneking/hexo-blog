title: node如何正确的编译babel
date: 2016-12-21
tags: node
categories: 编程
keywords: babel, node
---
&nbsp;&nbsp;&nbsp;&nbsp;ES6虽然现在在逐渐普及，但几乎没有浏览器或者node能完全支持ES6代码，相信babel现在对于前端开发者来说并不陌生。最近在写一个小工具，npm安装babel后，在node下build后ES6源码未编译，内心是奔溃的，为啥呢？

## 一、引起原因
* 其实是没有添加.babelrc文件，那么.babelrc到底能做什么呢

## 二、正确编译流程

### 1.安装babel及插件
```javascript
npm install babel-core          //babel6
npm install babel-polyfill      //支持新API
npm install babel-preset-es2015 //插件
//如果用了异步函数async function, await等，同时需要安装下面插件
npm install babel-preset-stage-2
```
<!--more-->
### 2.添加.babelrc文件
```javascript
{
    "presets": ["es2015"]
    //"presets": ["es2015", "stage-2"]
}
```
### 3.引入babel-polyfill
因为babel只编译语法，不编译API，所以如果用到一些ES6的API需要引入该模块
```javascript
require('babel-polyfill');
// or
import 'babel-polyfill';
```

### 4.编译
* 目录结构
```javascript
.
├── bin           //命令配置
├── build         //es6编译后的代码
├── README.md     //说明文档
├── index.js      //主入口
├── src           //es6代码
├── .babelrc      //babel编译配置文件
└── package.json    
```
* 配置编译命令
```javascript
//npm run build
"scripts": {
    "build": "babel src -d build"
},
```
babel src -d build 等价于 babel src --out-dir build

## 三、解释

### 1. babelrc文件
* rc结尾的文件通常代表运行时自动加载的文件、配置等，如.eslintrc,.zshrc。
.babelrc也是同样的作用，在babel编译中这个文件必不可少。

### 2. babel-preset-x
```javascript
babel-preset-es2015
babel-preset-stage-0
babel-preset-stage-1
babel-preset-stage-2
babel-preset-stage-3
babel-preset-react
```
* babel-preset-x这个是babel6新加的，告诉babel应该按照哪种预设方式转码，babel6做了一系列模块化，不像babel5一样把所有的内容都加载，比如需要编译ES6，我们需要设置presets为"es2015"，需要编译react语法，我们需要设置presets为"react"。
* stage-x代表[emac](https://github.com/tc39/ecma262)草案的不同阶段，stage-0包含所有,即stage0-3，stage-1包含stage1-3。
* stage-0是对ES7提案的一些支持，通过设置presets,让babel支持编译ES7。
