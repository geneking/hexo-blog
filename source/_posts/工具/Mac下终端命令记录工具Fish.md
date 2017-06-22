title: Mac下终端命令记录工具Fish
date: 2016-03-04
tags: mac
categories: 工具
keywords: mac,fish,命令记录
---
   很多情况下，每天我们打开电脑工具后，往往要在终端敲一些重复命令，那么有没有办法记录这些操作命令呢？必然是有的

## 1.安装Fish
  通过npm或者brew的方式安装 
```bash
  npm install fish -g
  或（前提是装了homebrew）
  brew install fish
```
<!--more-->
## 2.怎么记录
```bash
  KingdeMacBook-Pro:blog king$ fish
```
  > * 终端输入fish回车开启,此后输入的命令都会被记录
  > * 下次敲该命令只需敲命令第一个字母它会去自动匹配，如果有多条可以用上下按键去选择，选中后按右方向键回车即可
  > * 此外，按tab键可以显示浏览当前目录下的子文件or文件夹，回车打开

## 3.退出Fish
```bash
  king@KingdeMacBook-Pro /V/w/p/blog> exit
```
  没错，就是这么简单易用~~~
