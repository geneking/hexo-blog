title: React的JSX语法简单支持if判断
date: 2017-08-18
tags: React
categories: 编程

keywords: React, JSX
-----------------------------
### 1.首先说说JSX
* JSX是官方推荐的一种写法，实质上这只是一个语法糖
* 在React中开发中，JSX并不是唯一选择
* 最终JSX都会编译解析，通过React.createElement实现[https://facebook.github.io/react/docs/react-api.html](https://facebook.github.io/react/docs/react-api.html)
```javascript
//使用JSX
class App extend React.Component {
    render() {
        return (
            <div>
                <div>拯救不开心</div>
            </div>
        );
    }    
}
ReactDom.render(<App />, document.getElementById('app'));

//不使用JSX
class App extend React.Component {
    render() {
        return (
            React.createElement('div', null,
                React.createElement('div', null, '拯救不开心')
            )
        );
    }
}
ReactDom.render(<App />, document.getElementById('app'));
```
<!--more-->
### 2.JSX中不支持逻辑判断
```javascript
// 毫无疑问这样写会有问题
class App extend React.Component {
    render() {
        return (
            <div>
            {
                if (bool) {
                    <div>拯救不开心<div/>
                }
            }
            </div>
        );
    }
}
```
### 3.利用闭包特性，简单支持if判断
* 直接贴代码
```javascript
// If.js--ES5
exports.If = function (bool) {
    return function (jsx) {
        return bool ? jsx : null;
    };
};

//If.js--ES6
export default option => bool => bool ? jsx : null;
```
```javascript
//使用
class App extend React.Component {
    render() {
        return (
            <div>
            {
                If (list.length === 0) (
                    <div>暂无数据</div>
                )
            }
            </div>
        );
    }
}
```
* 注意，代码块是用()包裹，而不是{}，因为返回的是一个函数，需要再次调用
* bool ? jsx : null这里通过单元表达式最终判断返回的内容

### 4.有人可能会问，上述判断直接写三元表达式JSX不是也支持么
* 是的，说的没有问题
* 主要是看取舍了，三元表达式在html结构比较大的情况下代码可读性并不好，看自己的取舍和编程习惯吧
