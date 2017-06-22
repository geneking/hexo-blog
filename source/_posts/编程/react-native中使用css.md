title: React Native中使用CSS
date: 2017-01-28
tags: react native
categories: 编程

keywords: react, react native
-----------------------------
## 一、RN样式与普通CSS样式的差异
* RN的样式是CSS样式的一个子集
* RN中样式的命名规则全部采用驼峰写法，不支持其他写法
* RN要使用绝对或相对定位，父元素不需要设置position的值，直接是相对于父容器定位
* 在View元素中的Text表现为block，能设置margin和padding等用于block元素的属性
* 在Text元素中的Text表现为inline，不能设置其margin,padding等用于block元素的属性
* RN中没有zIndex属性

## 二、RN中样式引用的几种方式
### 1.内联样式
<!--more-->
```html
<View>
    <Text
        style={
            {
                color:'#333',
                fontSize: 12,
            }
        }>
    </Text>
</View>
```
### 2.Stylesheet.Create创建
```javascript
<View style={styles.row}>
    <Text style={styles.item}></Text>
    <Text style={styles.item}></Text>
</View>

const styles = Stylesheet.Create({
    row: {
        flex: 1,
        flexDirection: 'row',
    },
    item: {
        padding: 3,
        color: '#333',
        fontSize: 12,
    },
});
```
### 3.对象创建
```javascript
<View style={styles.row}>
    <Text style={styles.item}></Text>
    <Text style={styles.item}></Text>
</View>

const styles = {
    row: {
        flex: 1,
        flexDirection: 'row',
    },
    item: {
        padding: 3,
        color: '#333',
        fontSize: 12,
    },
};
```
### 4.组合样式
* 组合样式类似我们平常写的class组合，只不过是在React Native中采用数组组合
```javascript
<View style={[styles.row, styles.leftBorder]}>
    <Text style={styles.item}></Text>
    <Text style={styles.item}></Text>
</View>

const styles = {
    row: {
        flex: 1,
        flexDirection: 'row',
    },
    leftBorder: {
        borderLeftWith: 1,
        borderLeftColor: '#ccc',
    },
    item: {
        padding: 3,
        color: '#333',
        fontSize: 12,
    },
};
```

### 5.通过import导入
```javascript
//styles.js, 封装单独的js样式文件
const styles = {
    row: {
        flex: 1,
        flexDirection: 'row',
    },
    item: {
        padding: 3,
        color: '#333',
        fontSize: 12,
    },
};

module.exports = styles;

/*************************************/

//page.js
import styles from './styles';

<View style={styles.row}>
    <Text style={styles.item}></Text>
    <Text style={styles.item}></Text>
</View>
```
--------
总的来说，如果模板稍大，内联样式会使模板越来越臃肿。导入文件需单独新建文件，可能偏向传统的写法。Stylesheet.Create和对象创建类似，所以对象创建和组合样式是目前主流，比较推荐的做法，一般样式对象写在文件尾部。

## 三、RN中可用样式属性
```javascript
Valid style props: [
  "alignItems",
  "alignSelf",
  "backfaceVisibility",
  "backgroundColor",
  "borderBottomColor",
  "borderBottomLeftRadius",
  "borderBottomRightRadius",
  "borderBottomWidth",
  "borderColor",
  "borderLeftColor",
  "borderLeftWidth",
  "borderRadius",
  "borderRightColor",
  "borderRightWidth",
  "borderStyle",
  "borderTopColor",
  "borderTopLeftRadius",
  "borderTopRightRadius",
  "borderTopWidth",
  "borderWidth",
  "bottom",
  "color",
  "elevation",
  "flex",
  "flexDirection",
  "flexWrap",
  "fontFamily",
  "fontSize",
  "fontStyle",
  "fontWeight",
  "height",
  "justifyContent",
  "left",
  "letterSpacing",
  "lineHeight",
  "margin",
  "marginBottom",
  "marginHorizontal",
  "marginLeft",
  "marginRight",
  "marginTop",
  "marginVertical",
  "opacity",
  "overflow",
  "overlayColor",
  "padding",
  "paddingBottom",
  "paddingHorizontal",
  "paddingLeft",
  "paddingRight",
  "paddingTop",
  "paddingVertical",
  "position",
  "resizeMode",
  "right",
  "rotation",
  "scaleX",
  "scaleY",
  "shadowColor",
  "shadowOffset",
  "shadowOpacity",
  "shadowRadius",
  "textAlign",
  "textAlignVertical",
  "textDecorationColor",
  "textDecorationLine",
  "textDecorationStyle",
  "textShadowColor",
  "textShadowOffset",
  "textShadowRadius",
  "tintColor",
  "top",
  "transform",
  "transformMatrix",
  "translateX",
  "translateY",
  "width",
  "writingDirection"]
 ```

## 四、RN中隐藏元素
写过RN的都知道，隐藏元素有时候还真不像平常写个'display: none'就可以，通过项目实践，就目前了解的几种方式大致做个介绍
### 1.利用display
display只针对Text，View不支持该CSS属性
```javascript
<Text style={
    {display: this.state.status ? 'block': 'none'}
}>
</Text>
```
### 2.利用opacity
如果隐藏的元素设置了margin和padding，需要在hide类中重置为0，一般适用于View隐藏
```javascript
<View style={[styles.data, this.state.status ? '' : styles.hide]}>
    <View style={styles.dataItem}>
        <Text style={styles.dataText}>指标1：xxxxx</Text>
    </View>
    <View style={styles.dataItem}>
        <Text style={styles.dataText}>指标2：xxxxx</Text>
    </View>
</View>

const styles = {
    data: {
        marginTop: 10,
        borderColor: '#ccc',
        borderTopWidth: onePixel,
        borderRightWidth: onePixel,
    },
    dataItem: {
        borderColor: '#ccc',
        borderLeftWidth: onePixel,
        borderBottomWidth: onePixel,
    },
    dataText: {
        padding: 3,
        fontSize: 12,
        color: '#333',
    },
    hide: {
        opacity: 0,
        height: 0,
        marginTop: 0,
        overflow: 'hidden',
    }
};
```
## 五、RN中使用ICON
### 1.如若项目中对icon的样式没有特别要求，可以引入react-native-vector-icons模块
### 2.对应的icon类型
* Entypo by Daniel Bruce (411 icons)
* EvilIcons by Alexander Madyankin & Roman Shamin (v1.7.6, 68 icons)
* FontAwesome by Dave Gandy (v4.3, 519 icons)
* Foundation by ZURB, Inc. (v3.0, 283 icons)
* Ionicons by Ben Sperry (v2.0.1, 733 icons)
* MaterialIcons by Google, Inc. (v2.0, 796 icons)
* Zocial by Sam Collins (v1.0, 100 icons)

```javascript
import Icon from 'react-native-vector-icons/FontAwesome';

 // name：icon对应的name
 // size：icon（字体）大小
 // color：icon的颜色
<Icon
    name={'arrow-down'}
    size={12}
    color={'#09b7ff'}
/>
```
### 3.使用的icon name具体查阅[http://www.reactnative.com/customizable-vector-icons-for-react-native/](http://www.reactnative.com/customizable-vector-icons-for-react-native/)

## 六、其他
React Native 采用flex布局，关于flex布局的知识可以参考[http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
