---
layout: post
title: "构造函数与继承"
subtitle: ' "创建对象与实现继承"'
date: 2018-05-12 22:37:13
author: "Shiki"
tags:
  - JavaScript
---

### 构造函数

Javascript 规定，每一个构造函数都有一个 prototype 属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。<br />这意味着，我们可以把那些不变的属性和方法，直接定义在 prototype 对象上。

```javascript
function Person(firstName) {
  this.firstName = firstName;
}

Person.prototype.sayHello = function() {
  alert("Hello, I'm " + this.firstName);
};

var person1 = new Person("Alice");
var person2 = new Person("Bob");

// call the Person sayHello method.
person1.sayHello(); // alerts "Hello, I'm Alice"
person2.sayHello(); // alerts "Hello, I'm Bob"
```

### NEW 操作符

new 命令的作用，就是执行一个构造函数，并且返回一个对象实例。使用`new`命令时，它后面的函数调用就不是正常的调用，而是依次执行下面的步骤。<br />

1. **创建一个空对象，作为将要返回的对象实例。**
1. **将空对象的原型指向了构造函数的 prototype 属性。**
1. **将空对象赋值给构造函数内部的 this 关键字。**
1. **开始执行构造函数内部的代码。**

\*\*　　\*\*也就是说，构造函数内部，this 指向的是一个新生成的空对象，所有针对 this 的操作，都会发生在这个空对象上。构造函数之所谓构造函数，意思是这个函数的目的就是操作一个空对象（即 this 对象），将其构造为需要的样子。
<a name="F2yTq"></a>

### 最优的继承

使用寄生组合模式，对象的属性使用构造函数添加，<br />代码来源于 MDN 的  [继承与原型链](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

```javascript
function A(a) {
  this.varA = a;
}

// 以上函数 A 的定义中，既然 A.prototype.varA 总是会被 this.varA 遮蔽，
// 那么将 varA 加入到原型（prototype）中的目的是什么？
A.prototype = {
  varA: null,
  /*
既然它没有任何作用，干嘛不将 varA 从原型（prototype）去掉 ? 
也许作为一种在隐藏类中优化分配空间的考虑 ?
https://developers.google.com/speed/articles/optimizing-javascript 
如果varA并不是在每个实例中都被初始化，那这样做将是有效果的。
*/
  doSomething: function() {
    // ...
  }
};

function B(a, b) {
  A.call(this, a);
  this.varB = b;
}
B.prototype = Object.create(A.prototype, {
  varB: {
    value: null,
    enumerable: true,
    configurable: true,
    writable: true
  },
  doSomething: {
    value: function() {
      // override
      A.prototype.doSomething.apply(this, arguments);
      // call super
      // ...
    },
    enumerable: true,
    configurable: true,
    writable: true
  }
});
B.prototype.constructor = B;

var b = new B();
b.doSomething();
```

<a name="PEycn"></a>

### instanceof

instanceof 运算符用于测试构造函数的 prototype 属性是否出现在对象的原型链中的任何位置

```javascript
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
var auto = new Car("Honda", "Accord", 1998);

console.log(auto instanceof Car);
// expected output: true

console.log(auto instanceof Object);
// expected output: true
```
