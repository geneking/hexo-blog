title: 移动端pdf预览解决方案
date: 2016-06-04
tags: 移动端
categories: 编程

keywords: pdf预览，H5
-----------------------------
&nbsp;&nbsp;&nbsp;&nbsp;pdf预览在前端开发中比较常见，但是移动端预览pdf，这种需求应该不是很多。
### 1.主流的pdf预览插件

#### （1）pdfObject.js
调用方式:
```javascript
var mynode = document.getElementById("someID");
PDFObject.embed("myfile.pdf", mynode);
```
具体参数配置请查看API:[查看](https://pdfobject.com/)
<!--more-->
#### （2）pdf.js
调用方式:
```javascript
PDFJS.getDocument('helloworld.pdf');
//prommise方式
PDFJS.getDocument('helloworld.pdf').then(function(pdf) {
  // you can now use *pdf* here
});
```
* 相关资料:[查看](http://blog.csdn.net/xiangcns/article/details/42089189)
* 官方网站:[查看](https://mozilla.github.io/pdf.js/)

### 2.PC、移动端支持情况
#### （1）pdfObject.js移动端不支持；
#### （2）pdf.jst移动端支持；

### 3.事情并没有想象的那么完美
pdf.js虽然在移动端能够实现pdf的预览，然而存在很多问题：
* 插件本身太大，全部库文件大致1.2M左右，加上pdf文件本身接近2M，正常3G情况下，load完要7s+，对用户来说显然难以接受【摒弃】;
* 盖章无法正常显示（盖章由server端打至指定位置，非文件本身，可能与这个有关）;

### 4.到底应该选择怎样的方案
#### （1）简单粗暴
a标签直链资源文件，ios可以在新标签直接预览，Android下载后用浏览器查看【估计PM很难接受】;
#### （2）最终选择的方案
![pdf](pdf.jpg)
> * 有没有觉得还是很low，image。。。对，最终是image方案，但确实是一种折中的解决方案;
> * 由于项目pdf中有些动态数据，所以会有一层ftl->html->pdf,如果没有动态数据，可以直接pdf->image;
> * pdf-img-viewer是自己写的一个展示pdf图片的小组件,打个广告：[pdf-img-viewer](https://github.com/geneking/pdf-img-viewer);
