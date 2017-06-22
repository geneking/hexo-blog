title: PM2在ES6代码下运行cluster模式
date: 2017-05-18
tags: pm2
categories: 编程

keywords: node, pm2
-----------------------------
### 1.fork模式下启动正常
* app.json："exec_mode": "fork"
### 2.cluster模式遇到的问题
* app.json配置文件
```javascript
{
    "apps": [{
        "name": "app",
        "script": "./app.js",
        "node_args": "--harmony",
        "autorestart": false,
        "watch": true,
        "exec_interpreter": "babel-node",
        "instances": "max",
        "exec_mode": "cluster",
        "log_date_format": "YYYY - MM - DD HH: mm Z",
        "error_file": "./logs/app-err.log",
        "out_file": "./logs/app-out.log",
        "env": {
            "NODE_ENV": "production"
        }
    }]
}
```
* pm2 start app.json无法正常启动应用
<!--more-->
* pm2 logs
```javascript
/Volumes/work/meituan/waimai_mfe_crm_analytics/app.js:1
(function (exports, require, module, __filename, __dirname) { import koa from 'koa';
                                                              ^^^^^^
SyntaxError: Unexpected token import
    at Object.exports.runInThisContext (vm.js:76:16)
    at Module._compile (module.js:542:28)
    at Object.Module._extensions..js (module.js:579:10)
    at Module.load (module.js:487:32)
    at tryModuleLoad (module.js:446:12)
    at Function.Module._load (module.js:438:3)
    at /usr/local/lib/node_modules/pm2/lib/ProcessContainer.js:301:23
    at /usr/local/lib/node_modules/pm2/node_modules/async/lib/async.js:52:16
    at /usr/local/lib/node_modules/pm2/node_modules/async/lib/async.js:1209:30
    at WriteStream.<anonymous> (/usr/local/lib/node_modules/pm2/lib/Utility.js:147:13)
/Volumes/work/meituan/waimai_mfe_crm_analytics/app.js:1
```
### 3.定位问题
pm2 issues：
* [https://github.com/Unitech/pm2/issues/2614](https://github.com/Unitech/pm2/issues/2614)
* 根据issues建议，修改启动方式
```javascript
//server.js
require('babel-register');
require('babel-polyfill');
require('./app.js');
```
```javascript
//.babelrc配置
{
    "presets": ["es2015", "stage-1"]
}
```
* app.json的启动脚本script改为server.js，然而问题并没有解决
* 也有其他原因说是pm2的cluster模式会中断babel-node的编译进程

### 4.简单直接的解决方法
* 基于上述第3点，用babel将node ES6代码编译成ES5代码，pm2 start app.json启动正常
* 编译方法参考本博客另一篇文章：[node如何正确的编译babel](http://www.keycode.me/2016/12/21/%E7%BC%96%E7%A8%8B/node%E5%A6%82%E4%BD%95%E6%AD%A3%E7%A1%AE%E7%9A%84%E7%BC%96%E8%AF%91babel/)
