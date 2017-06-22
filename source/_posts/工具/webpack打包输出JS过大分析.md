title: webpack打包输出JS文件过大
date: 2016-07-05
tags: webpack
categories: 工具
keywords: webpack, 打包图片
---
   最近在做项目的性能优化，项目采用webpack构建，其中有个js文件打包输出后为1.5M，什么鬼？怎么可以这么大~~~~让我们下面来看看原因

## 1.主要原因分析

### （1）图片的base64编码
```javascript
  { test: /\.(?:jpg|gif|png)$/, loader: 'url' }
```
* 不对打包图片做限制，会将所有依赖图片打包成base64，前面说的1.5M主要就是4张图片打包成base64造成的
* base64比原图要大一些，大约大1/3，所以base64只适合小图标，不适合偏大的图

### （2）图片打包成base64为什么会变大
base64编码使用八比特表示六比特的内容，所以会偏大
<!--more-->

### （3）base64图片的优缺点
优点：
> * 减少HTTP请求；
> * 某些文件可以避免跨域的问题;
> * 图片更新不需要打版本号，不用担心缓存问题（不能缓存其实反过来也是优点）；

缺点：
> * 图片偏大，比原图大1/3；
> * 如果工具不支持，需要手动转换导入base64[在线工具](http://www.bejson.com/ui/image2base64/)，也可以用canvas去转；
> * 增加了打包文件大小；
> * 浏览器兼容情况，IE6/7不支持；
> * 不能缓存；

## 2.解决webpack打包base64引起的文件偏大
只需要对打包图片做个限制
```javascript
  { test: /\.(?:jpg|gif|png)$/, loader: 'url?limit=10240&name=images/[name]-[hash:8].[ext]' }
```
* 图片大于10KB不打包成base64，直接输出图片
* name后跟图片输出路径
* 可以给图片加hash版本[hash]或[hash:8]（8位长度的hash）

## 3.webpack减小打包文件体积其他方法
### (1) 提取公共库文件
```javascript
{
  entry: {
    bundle: 'app'
  },
  output: {
    path: 'build',
    filename: '[name].js'
  },
  plugins: [
    {
       new webpack.optimize.CommonsChunkPlugin('vendor.js');
    }
  ]
}
```
会在build目录下打包出公共文件vendor.js

### (2)代码压缩
webpack自带的文件压缩插件配置：
```javascript
  plugins: [
    new webpack.optimize.UglifyJsPlugin({
        minimize: true
    })
  ]
```
