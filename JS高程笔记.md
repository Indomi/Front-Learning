> 仅仅记录自己觉得重要的小细节点

## 1. defer和async属性

defer：脚本执行时不会影响页面的构造，脚本将延迟在遇到`</html>`后再执行，并且先于`DOMContentLoaded`事件执行

async：标记的脚本并不保证按照指定的先后顺序执行，并且不让页面等待脚本下载和执行，从而异步加载页面的其他内容，会在`load`事件前执行，可能会在`DOMContentLoaded`前或后执行

## 2. 严格模式

`use strict`开启严格模式，可以在函数内部声明，指明函数在严格模式下执行

## 3. `typeof`

未声明的变量同样返回`undefined`

```javascript
// 未声明
// var age

typeof age // undefined
```

## 4. `for in`

可以用来枚举对象的属性

- 循环顺序不确定
- 建议使用之前先确定对象是否为null或者undefined（es5前会报错）

## 5. ``