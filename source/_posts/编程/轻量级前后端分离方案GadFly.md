title: 轻量级前后端分离方案GadFly
date: 2017-06-09
tags: 前后端分离
categories: 编程
keywords: 前后端分离方案
---

## 一、背景
### 1.“刀耕火种”的时代
![ftl](ftl.jpg)
<!--more-->
* 前后端代码一个工程，耦合比较严重
* 前端需要开发ftl，jsp等，增加开发成本
### 2.重前端时代
![node](node.jpg)
* 前端当家做主，前后端分离，架设Node中间层
* 前端能做的越来越多，然而另一方面，需搭建Node服务，资源的开销，服务维护成本变高

## 二、为什么要前后端分离
### 1.前后端分工不明确
部分业务逻辑难以划分责任，如FreeMarker，JSP等，到底是由前端开发，还是后端开发
### 2.开发效率比较低
* 前端开发较强依赖于后端，需要安装Java环境，这对于后端基础较为薄弱的同学来说，比较容易出错
* 新增页面路由，需要找后端同学添加
* 工作中需要增加沟通成本，如前后端代码冲突需要共同解决
* 前后端难以并行开发，存在前端等后端，后者后端等前端的情况
### 3.前端比较局限
* 对于一些技术方案（如性能优化等），需要前后端共同配合来完成
* 技术拓展比较局限，页面依赖于FreeMarker，JSP等，难以用一些业界比较好的技术方案优化业务逻辑
### 4.维护成本比较高
* 前后端代码耦合，各自物理代码不独立。在业务逻辑复杂的系统里，因为没有约束，日积月累，维护性将变得越来越差
* 如果对现有业务进行重构，需要梳理业务逻辑，开发成本也比较大

## 三、GadFly
### 1.实现原理
![gadfly](gadfly.jpg)
* GadFly集成了vane 也就是数据mock，eslint等基础开发辅助工具
* 数据调用后端HTTP接口，页面由前端渲染，不存在ftl，也不存在Node中间层渲染
* 本地开发，用webpack走代理，请求代理到后端dev server（开发机）
* 部署时将build的html文件async后端Java服务静态目录，同时将html引用的静态文件通过部署脚本上传CDN
* 后端Java服务只需要配置html目录的Map映射
### 2.开发流程
* 用GadFly项目脚手架生成项目工程，目前仅支持webpack + React项目
* 安装项目生成脚手架[generator-gadfly](hhttps://github.com/geneking/generator-gadfly)
```bash
npm install -g yo
npm install -g generator-gadfly
```
* yo gadfly xxx
![gadfly_init](gadfly_init.jpg)
* 配置后端Java服务代理
```javascript
//webpack.dev.config.js
devServer: {
    hot: true,
    inline: true,
    host: process.env.HOST,
    port: "8808",
    proxy: {
        '/brain/**': {
            target: 'http://10.4.233.139:8413/', //dev
            //target: 'http://10.20.114.242:8413/',//qa
            secure: false
        },
        '/uicomponent/**': {
            target: 'http://10.4.233.139:8413/',
            secure: false
        }
    }
},
```
* 新建页面，进入当前项目文件夹根目录：yo gadfly:page Home
![gadfly_create](gadfly_create.jpg)
* npm start启动项目
### 3.部署上线
* 目前采用的是Jenkins部署，部署脚本为根目录predeploy.sh脚本，部署脚本根据项目实际情况可以做适当调整

### 4、GadFly的优势
* 前后端物理代码分离，减少前后端代码耦合，使得前后端分工更加明确
* 快速生成项目工程，新建页面，减少项目搭建时间
* 前后端分离轻量化，不用维护Node服务，节约资源，降低运维成本
* 集成数据mock工具，前后端并行开发，提升开发效率

## 四、其他
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在国内，大部分一二线互联网公司，对于前后端的工作分工还是比较明确。然而在一些国外公司可能部分前前端还是后端，比如FaceBook，只分Product和Infrastructure。Product的通常都是FullStack，不需要对特定的技术非常精通，但要求学习能力和灵活性足够好，不能只做自己工作区域以内的事，更希望从技术角度使你的产品更加优秀。通常聪明的应届生都会先进入Product，因为他们学什么都很快，也不会说浪费了在某个领域的积累。而Infrastructure拥有更多各个领域的技术专长，前端只是其中之一。Infrastructure就是Product，要做的就是让Product开发实际产品时觉得爽。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;总之，在钻研前端技术的同时，扩充自己的其他技术储备，尽量去向FullStack工程师看齐，既能服务于产品，又能提升自己的技术素养。
