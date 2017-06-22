title: React Native 0.32版本以下升级xcode8报错解决办法
date: 2016-11-08
tags: react native
categories: 编程

keywords: react, react native
-----------------------------
![xcode8](xcode8.png)
### 1.RCTSRWebSocket.m报错
* 报错: Ignoring return value of function declared with warn_unused_result attribute
```javascript
//两处报错的地方
SecRandomCopyBytes(kSecRandomDefault, sizeof(uint32_t), (uint8_t *)mask_key);
```
* 怎么修改
```javascript
//前面加void
(void)SecRandomCopyBytes(kSecRandomDefault, sizeof(uint32_t), (uint8_t *)mask_key);
```
<!--more-->
### 2.RCTScrollView.m报错
* 报错: Use of undeclared identifier '_refreshControl'; did you mean 'refreshControl'?
* 怎么修改
```javascript
@implementation RCTCustomScrollView
{
  __weak UIView *_dockedHeaderView;
  //加入下面语句
  RCTRefreshControl *_refreshControl;
}
```
### 3.注：react native0.32版本以上不会存在该问题
