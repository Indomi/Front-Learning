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

## 5. `with`

将当前作用域设置到一个特定的对象之中

```javascript
with(location) {
  var qs = search.substring(1)
  var hostName = hostname
  var url = href
}
```

## 6. 没有重载

没有像java一样的重载方法来对同样的方法名写出不同的处理

如果同一个函数名的函数被定义多次，只取最后一个定义的函数

## 7. 垃圾回收机制

- 标记清除：对所有变量进行标记，再对局部变量和函数内引用的变量去除标记，最后将有标记的变量作为准备清除的变量
- 引用计数：跟踪被引用的次数，引用次数为0时在下一次垃圾回收时清除，存在`循环引用`的弊端，导致计数不能为0，无法被清除

- 管理内存：在对象不被使用时手动赋值为`null`

> 随着 IE7 的发布，其 JavaScript 引擎的垃圾收集例程改变了工作方式：触发垃圾收集的变量分配、
字面量和（或）数组元素的临界值被调整为动态修正。 IE7 中的各项临界值在初始时与 IE6 相等。如果
垃圾收集例程回收的内存分配量低于 15%，则变量、字面量和（或）数组元素的临界值就会加倍。如果
例程回收了 85%的内存分配量，则将各种临界值重置回默认值。这一看似简单的调整，极大地提升了 IE
在运行包含大量 JavaScript 的页面时的性能。

## 8. 数组

- 最多包含4, 294, 967, 295项

## 9. 数组ES5方法

- every()
- filter()
- forEach()
- map()
- some()
- reduce()
- reduceRight()
- indexOf()
- lastIndexOf()
- push()
- pop()
- shift()
- unshift()
- splice()
- slice()
- concat()
- reverse()
- sort()

## 10. `Date`

- Date.now()
- Date.UTC()
- Date.parse()

## 11. Function

- 函数声明创建的函数会进行提升，在任何地方都可以使用
- 函数表达式创建的函数只有在解析到所在代码行后才可以正常使用
- arguments拥有属性callee指向拥有arguments的函数

## 12. 基本包装类型

- 引用类型与基本包装类型的主要区别就是对象的生存期。使用 new 操作符创建的引用类型的实例，
在执行流离开当前作用域之前都一直保存在内存中。而自动创建的基本包装类型的对象，则只存在于一
行代码的执行瞬间，然后立即被销毁。这意味着我们不能在运行时为基本类型值添加属性和方法。

## 13. 转型函数

- 使用`new`调用的基本包装类型的构造函数，和直接调用的转型函数不一样
```javascript
typeof new Number(1) // 'object'
typeof Number(1) // 'number'
```

## 14. `Object.defineProperty()`

- IE8是第一个实现`Object.defineProperty()`的浏览器版本，但限制较多，只能在DOM对象上使用（Vue只兼容到IE9原因？）实现不够彻底
- `Object.defineProperty()`
- `Object.defineProperties()`
- `Object.getOwnPropertyDescriptor`读取属性的描述符
```javascript
const obj = { a: 1 }
const descriptor = Object.getOwnPropertyDescriptor(obj, 'a')
```
## 15. 构造函数
### new 的作用
### 1）创建一个新对象
### 2）将构造函数的作用域赋给新对象（this）
### 3）执行构造函数中的代码
### 4）返回这个新对象

