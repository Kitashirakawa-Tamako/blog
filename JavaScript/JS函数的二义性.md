# 函数的二义性
在Javascript中，调用一个函数可以有以下两种方式：
```javascript
function f(){}
// 构造函数调用
let a = new f() // f {}
// 普通函数调用
let b = f() // undefined
```
不同的调用方式体现出对函数`f`的不同理解，并且有着不同的调用结果，这种情况被称作函数的二义性。

但大多数情况下，这种歧义都是可以避免的。JavaScript命名规范中建议使用大写字母开头的函数名来表示构造函数，而使用小写字母开头的函数名来表示普通函数或函数表达式，使代码更易于理解和维护。例如以上代码在IDEA中存在提示信息：
![QQ截图20240218232611.png](/image/49377cd0-2c85-4cfc-883f-247ebf639d0d.png)

*构造函数名称通常以大写字母开头。打字错误？*

但这不是强制要求的，这种歧义依然存在。

# ES6

ES6标准中扩展出了箭头函数和类的概念去消除函数的二义性。

## 箭头函数 ()=>{}

箭头函数表达式的语法比传统的函数表达式更简洁，但在语义上有一些差异，在用法上也有一些限制：

* 箭头函数没有独立的 `this`、`arguments` 和 `super` 绑定，并且不可被用作方法。
* 箭头函数不能用作构造函数，当使用 `new` 调用时会出错。它们也没有 `prototype` 属性。
* 箭头函数不能在其主体中使用 `yield`，也不能作为生成器函数创建。

测试：

```javascript
const f= () => {};
new f(); // × TypeError: F is not a constructor
f(); // √
```

## 类 class

类是“特殊的函数”，声明后的类只能通过 `new` 关键字调用。

测试：
```javascript
class F {};
F(); // × Uncaught TypeError: Class constructor F cannot be invoked without 'new'
new F(); // √
```
