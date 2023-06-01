---
layout: post
title:  js的call和apply的用法
date:   2016-10-08 00:58:08 +0800
categories: jekyll javascript
tags: javascript
---

# js的call和apply的用法

一直都对js的call和apply的用法记忆模糊，今天梳理一下当成备忘。  

> 1.**《javascript高级程序设计第三版》** *P116*

每个函数都包含两个非继承而来的方法：`apply()`和`call()`。这两个方法的用途都是在特定的作用域中调用函数，实际上等于设置函数体内**this** 对象的值。首先，`apply()`方法接收两个参数：一个是在其中运行函数的作用域，另一个是参数数组。其中，第二个参数可以是**Array**的实例，也可以是
**arguments** 对象。例如：  

```javascript
function sum(num1, num2) {
  return num1 + num2;
}
function callSum1(num1, num2) {
  return sum.apply(this, arguments); // 传入arguments 对象
}
function callSum2(num1, num2) {
  return sum.apply(this, [num1, num2]); // 传入数组
}
alert(callSum1(10,10)); //20
alert(callSum2(10,10)); //20
```
在上面这个例子中，`callSum1()`在执行`sum()`函数时传入了**this** 作为**this** 值（因为是在全局作用域中调用的，所以传入的就是**window** 对象）和**arguments** 对象。而**callSum2** 同样也调用了`sum()`函数，但它传入的则是**this** 和一个参数数组。这两个函数都会正常执行并返回正确的结果。

*在严格模式下，未指定环境对象而调用函数，则**this** 值不会转型为**window**。除非明确把函数添加到某个对象或者调用`apply()`或`call()`，否则**this** 值将是**undefined**。*

`call()`方法与`apply()`方法的作用相同，它们的区别仅在于接收参数的方式不同。对于`call()`方法而言，第一个参数是**this** 值没有变化，变化的是其余参数都直接传递给函数。换句话说，在使用`call()`方法时，传递给函数的参数必须逐个列举出来，如下面的例子所示。

```javascript
function sum(num1, num2) {
  return num1 + num2;
}
function callSum(num1, num2) {
  return sum.call(this, num1, num2);
}
alert(callSum(10,10)); //20
```

在使用`call()`方法的情况下，`callSum()`必须明确地传入每一个参数。结果与使用`apply()`没有什么不同。至于是使用`apply()`还是`call()`，完全取决于你采取哪种给函数传递参数的方式最方便。如果你打算直接传入**arguments** 对象，或者包含函数中先接收到的也是一个数组，那么使用`apply()`肯定更方便；否则，选择`call()`可能更合适。（在不给函数传递参数的情况下，使用哪个方法都无所谓。）

事实上，传递参数并非`apply()`和`call()`真正的用武之地；它们真正强大的地方是能够扩充函数赖以运行的作用域。下面来看一个例子。

```javascript
window.color = "red";
var o = { color: "blue" };
function sayColor() {
  alert(this.color);
}
sayColor(); //red
sayColor.call(this); //red
sayColor.call(window); //red
sayColor.call(o); //blue
```

这个例子是在前面说明**this** 对象的示例基础上修改而成的。这一次，`sayColor()`也是作为全局函数定义的，而且当在全局作用域中调用它时，它确实会显示"red"——因为对**this.color** 的求值会转换成对**window.color** 的求值。而`sayColor.call(this)`和`sayColor.call(window)`，则是两种显式地在全局作用域中调用函数的方式，结果当然都会显示"red"。但是，当运行`sayColor.call(o)`时，函数的执行环境就不一样了，因为此时函数体内的**this** 对象指向了**o**，于是结果显示的是"blue"。

使用`call()`（或`apply()`）来扩充作用域的最大好处，就是对象不需要与方法有任何耦合关系。在前面例子的第一个版本中，我们是先将`sayColor()`函数放到了对象**o** 中，然后再通过**o** 来调用它的；而在这里重写的例子中，就不需要先前那个多余的步骤了。

> 2.关于call和apply的思考

### 严格模式下：

这个call和apply方法如果在严格模式下（在chrome浏览器下），运行如下代码:

```javascript
(function() {
  "use strict";
  var a = function(b, c) {
    console.log(this);
    return b + c;
  };
  var b = function(c, d) {
    console.log(a(c, d));
  };
  var c = function(d, e) {
    console.log(a.apply(this, arguments));
  };
  b(1, 2);
  c(1, 2);
  return 'ok';
})();
```

浏览器会返回:

```javascript
undefined
3
undefined
3
"ok"
```

如果把`"use strict"`语句去掉，**undefined** 会变成**window** 对象。

### 非严格模式下：  

*以下为自己的想法：  
call和apply方法其实就是把函数体内的this改变成第一个参数，然后根据第二个参数运行这个函数。如果函数体内没有和this有关的语句，除了this变量改变，其实和直接调用函数一样。就是函数体内的this值变了。使用call和apply可以缓解耦合。*
