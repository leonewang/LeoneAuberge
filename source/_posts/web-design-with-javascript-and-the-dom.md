---
title: Think About Property Of Javascript
tags:
  - JavaScript
categories:
  - Summarize
date: 2015-09-02 16:19:04
---

> REF:[http://www.liaoxuefeng.com/](http://www.liaoxuefeng.com/) 的面向对象编程章节。

```javascript
function PrimaryStudent(grade) {
    this.grade = grade;
}

function Student(name) {
    this.name = name;
    this.hello = function() {
        console.log('Hello ' + this.name);
    }
}

PrimaryStudent.prototype = new Student('xiaoming');

var xiaoming = new PrimaryStudent('xiaoming');

xiaoming.hello();   // Hello xiaoming
alert(Student.prototype.isPrototypeOf(xiaoming));   // true
```

<!-- more -->

原型链如图所示：

[![屏幕快照 2015-09-02 16.22.51](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/09/屏幕快照-2015-09-02-16.22.51.png)](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/09/屏幕快照-2015-09-02-16.22.51.png)

之前在学习 JavaScript 的时候就对原型对象的理解就已步入误区，首先要明确 `[[prototype]]` 和 `prototype` 是两种不一样的东西。

首先讲讲 `prototype`，每一个函数对象都有一个显式的 `prototype` 属性，代表了对象的原型，更具体的说是代表了函数对象 **（JavaScript 中没有构造函数这一说法）** 所创建的对象的原型。

以上例为例，`PrimaryStudent.prototype` 就是 `xiaoming` 的原型，`xiaoming` 所引用的那个由 `new PrimaryStudent()` 创建出来的对象将从 `PrimaryStudent.prototype` 所引用的对象中继承其属性与方法。

其次是 `[[prototype]]`，每一个对象都有一个隐藏的 `[[prototype]]` 内部属性，上例中，`xiaoming` 的 `[[prototype]]` 指向 `PrimaryStudent.prototype` ，而 `PrimaryStudent.prototype` 也是一个对象，所以它也有一个 `[[prototype]]` 属性指向它的原型对象，这便形成了一个 **原型链**。
