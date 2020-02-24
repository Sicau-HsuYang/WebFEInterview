## 1、清除浮动的方法？它们的优缺点？

## 2、编写一个方法，去掉数组里面重复的内容 var arr = ['abc', 'abcd', 'sss', '2', 'd', 't', '2', 'ss', 'f','22', 'd' ]

## 3、写一个function，清除字符串前后的空格。（兼容所有浏览器）

```js
typeof String.prototype.trim !== 'function' && String.prototype.trim = function() {
 return this.replace(/(^\s*)|(\s*$)/g, '')
}
```

## 4、这里有一个url： https://www.baidu.com/s?id=111&name=yourname,写一个函数获取query的参数和值存放在一个对象中

## 5、一个200*200的div在不同的分辨率屏幕上下左右居中，用css实现
