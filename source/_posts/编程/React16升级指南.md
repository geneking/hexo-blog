title: React 16升级指南
date: 2018-04-20
tags: JavaScript
categories: React

keywords: 前端,React 16
-----------------------------

### 一、为什么升级

* #### 文件体积变得更小

| 文件               | v15.5  | v16.2  |
| ---------------- | ------ | ------ |
| react.min.js     | 17.1KB | 6.71KB |
| react-dom.min.js | 122KB  | 99.7KB |

* #### 重写核心算法，渲染性能更优

  React核心代码进行了重构命名为React Fiber，Fiber相较于之前版本最大的不同是它可以支持异步渲染(16.0不支持，16.x支持)，异步渲染能够将渲染任务划分为多块，这意味着几乎所有的行为都是同步发生的。React 16使用原生的浏览器 API来间歇性地检查当前是否还有其他任务需要完成，从而实现了对主线程和渲染过程的管理。例如拖动、onChange等在不考虑防抖情况，频繁setState的场景，相对于之前版本，有一定性能的提升。
<!--more-->
* #### 新API的支持

### 二、React 16新特性简介

#### 1.render支持Array和String渲染

```
render() {
  return [
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}

render() {
  return 'Look ma, no spans!';
}
```

#### 2.Portals API

Portals提供一种新的方式，支持DOM节点渲染在父组件之外，对于Modal、Tooltip、Toast这些组件更为友好

```
render() {
  return ReactDOM.createPortal(
    this.props.children,
    domNode,
  );
}
```

更多：<https://reactjs.org/docs/portals.html>

#### 3.更好的处理错误Error Boundaries

若果组件定义了新的生命周期函数 componentDidCatch(error, info)，将error boundaries（错误边缘）想象成try{}catch(){}语句，只适用React组件

更多：<https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html>

#### 4.更好的服务端渲染

React 16的SSR被完全重写，新的实现非常快，接近3倍性能于React 15，它支持流（streaming），可以很快的向客户端更快地发送字节

更多：<https://reactjs.org/docs/react-dom-server.html>

#### 5.支持自定义DOM属性

```
// 你的代码:
<div mycustomattribute="something" />

// React15 输出:
<div />

// React 16 输出:
<div mycustomattribute="something" />
```

### 三、React15升级React应注意的问题

#### 1.不再支持ES5语法

```
// ES5
var Button = React.createClass({
    getDefaultProp:function() { },
    getInitialState: function() { },
    render: function () {
        return (<button></button>);
    }
});
```

* ##### 方法一：引入create-react-class

```
var createReactClass = require('create-react-class');
var Button = createReactClass({  
    getDefaultProp:function() { },  
    getInitialState: function() { },  
    render: function () {  
        return (<button></button>);  
    }  
});
```

* ##### 方法二：ES6语法

```
class Button extends React.Component {
    static defaultProps = {
    }
    constructor(props) {
        super();
        this.state = {}
    }
    render() {
        return (<button></button>);
    }
 }
```

#### 2.React.PropTypes废弃

React v15.5及以上版本 将PropTypes模块剔除，封装了一单独的包prop-types做类型检查

```
// v15.5.0 以前
import React from 'react'
class Button extends React.Component {
  render() {
    return <button>{this.props.children}</button>;
  }
}

Button.propTypes = {
  children: React.PropTypes.any,
}
 =============================================
// v15.5.0 及以后
import React from 'react'
import PropTypes from 'prop-types'

class Button extends React.Component {
  render() {
    return <button>{this.props.children}</button>
  }
}

Button.propTypes = {
  children: PropTypes.any,
}
```

#### 3.ReactDOM.render() 和 React.unstable_renderSubtreeIntoContainer()

这两个方法在生命周期方法中执行时将会返回 null，可以使用 [portals](https://github.com/facebook/react/issues/10309#issuecomment-318433235)或者 [ref](https://github.com/facebook/react/issues/10309#issuecomment-318434635) 来代替：

* ##### 方法一：createPotal

```
ReactDOM.createPotal((
  <ModalContent />
), document.getElementById('root'))
```

* ##### 方法二：ref

```
ReactDOM.render((
  <div className={`tooltip fade in ${direction}`} ref={dom => {
  	this.tipNode = dom ;
  }}>
    <div className={`tooltip-inner ${className}`} style={style}>
      {content}
    </div>
  </div>
), this.wrapperNode)
```
### 四、其他

#### 1.React 16.3已支持全新的Context API： [React 16.3](https://reactjs.org/blog/2018/03/29/react-v-16-3.html)

#### 2.升级建议:

* ##### 建议新的项目可直接升级至React 16.2+
* ##### 老项目在升级后尽量进行回归性测试，确保线上项目稳定运行，特别是对于一些涉及Modal、Toast、Loading等组件的页面
