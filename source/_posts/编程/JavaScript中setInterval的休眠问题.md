title: JavaScript中setInterval的“休眠”问题
date: 2016-03-03
tags: JavaScript
categories: 编程
keywords: javascript,setinterval休眠,图片轮播，相册轮播
---
   最近工作中有个需求需要个图片轮播，就简易写了个渐变的图片轮播，然而~~意想不到的事情发生了...

## 1.遇到了神马问题？
### DOM结构
```html
  <!--图片轮播 开始-->
  <div class="drug-slider clearfix">
    <img class="pic-one active" src="/static/img/drugs/drug_banner1.jpg" />
    <img class="pic-two" src="/static/img/drugs/drug_banner2.jpg" />
  </div>
  <!--图片轮播 结束-->
```
<!--more-->
### JS代码
```javascript
	/*
	 * @function slider
	 * @description 图片渐变轮播
	 * @param {imgWrap} 图片列表父级对象
	 * @return
	*/
	slider:function(imgWrap){
		setInterval(function(){
      imgWrap.find(".active").animate({opacity: 0}, 2000,function(){
      	$(this).removeClass("active");
      }).siblings().addClass("active").animate({opacity: 1}, 2000);
		},4000);
	}
```
### 遇到的问题
  在最小化浏览器窗口，或者切换tab后片刻，图片轮播直接给我hide掉了，没错= =，是直接给hide了，什么鬼！！
## 2.为什么会引起这个bug
  网上搜索资料之后，其实是这样的：
  > * 在最小化窗口或者切换tab之后，为了提升浏览器性能，setInterval是会暂时进入“休眠”状态,并不是不执行程序,它会把需要执行的操作放在队列中 ，等到下次窗口打开的瞬间把队列里面的全部执行，由于程序处理太快我们大部分时候并没有注意到这个问题。
  > * 我这里的轮播图为什么会全部hide掉呢？当我切换到其他tab或最小化窗口，程序休眠了，但不会影响animate回调的执行，所以仍然remove掉了active，当下次激活tab后，重新只想队列，然后都没有active，所以造成该语句无法执行，最后引起两个img都缺失active类，也就是display为none，所以会hide掉。
```html
  <!--图片轮播 开始-->
  <div class="drug-slider clearfix">
    <img class="pic-one" style="opacity:0" src="/static/img/drugs/drug_banner1.jpg" />
    <img class="pic-two" style="opacity:1" src="/static/img/drugs/drug_banner2.jpg" />
  </div>
  <!--图片轮播 结束-->
```
## 3.怎么解决setInterval(或setTimeout)休眠
```javascript
  /*
   * @function slider
   * @description 图片渐变轮播
   * @param {imgWrap} 图片列表父级对象
   * @return
  */
  slider:function(imgWrap){
    setInterval(function(){
      imgWrap.find("img").not(".active").addClass("active").animate({opacity: 1}, 2000)
      .siblings().animate({opacity: 0}, 2000,function(){
        $(this).removeClass("active");
      })
    },4000);
  }
```
  > * 有了上面的分析，animate回调会始终执行，所以颠倒下之前语句的执行顺序，我们第一次执行时，先给没有active的img添加active类，当程序眠后，也会执行animate回调，所以始终会移除兄弟元素的active类，当tab下次激活时，程序执行自然没有问题。
  > * 当在用setTimeout时，可以利用递归，在animate回调里执行递归调用，即使程序休眠也不会有影响。
## 4.两篇参考文章
  > * [http://www.tuicool.com/articles/32iYJvf](http://www.tuicool.com/articles/32iYJvf)
  > * [http://www.2cto.com/kf/201407/317505.html](http://www.2cto.com/kf/201407/317505.html)
