---
title: Javascript Properties are Enumerable Writable and Configurable
date: 2016-03-15 22:35:55
updated: 2016-03-15 22:35:55
tags:
    - JavaScript
categories:
    - Summarize
---
> REF: 翻译自 [Javascript properties are enumerable, writable and configurable](http://arqex.com/967/javascript-properties-enumerable-writable-configurable#comments)

对象是 Javascript 中的核心部分。对象所使用的 JS 语句也非常简洁易用，自然也能轻松地创建对象或 hashmaps 类型的对象。

``` javascript
// 声明对象 ob
var ob = {a: 1};
 
// 访问 ob 的属性 a
ob.a; // => 1
 
// 修改 a 属性值
ob.a = 0;
ob.a; // => 0;
 
// 添加一个新属性 b
ob.b = 2;
ob.b; // => 2
 
// 删除属性 b
delete ob.b;
ob.b; // => undefined
```

看上去很简单，但是容易让人忽略的是上面例子中对象属性所含有的三种特性：可枚举性、可写性和可配置性。

<!-- more -->
- 可枚举性 - **Enumerable**：表示能否通过 `for...in` 循环返回所有属性，且 `Object.keys` 语句能够返回对象属性的所有 key。
- 可写性 - **Writable**：表示能否修改属性值。
- 可配置性 - **Configurable**：表示能否通过 `delete` 操作符删除属性从而重新定义属性，能否修改属性的这三个特性。

很多人都知道 `Object` 属性中的前两个特性，可枚举性和可写性，但是却很少有人知道最后一个 **可配置性**，这个特性让我们可以使用 `Object` 的 `defineProperty` 方法来 **重新定义对象的这三个特性**。

``` javascript
// 使用 Object.defineProperty 方法给 ob 添加一个 c 属性
Object.defineProperty( ob, 'c', {
  value: 3,
  enumerable: false,
  writable: false,
  configurable: false
});
 
ob.c; // => 3
 
Object.getOwnPropertyDescriptor( ob, 'c' ); 
// => {value: 3, enumerable: false, writable: false, configurable: false}
```

<div class="tip">
``` javascript
configurable: false; // 一旦把特性定义为不可配置，就不能再修改为可配置
```
</div>

虽然使用句式并不简洁，但是这些特性在实现一些特殊需求时往往能屡试不爽！定义这些特性的对象被称为描述符 `descriptor`，你可以使用 `Object.getOwnPropertyDescriptor` 方法来获得任何特性的描述符对象，如上例中使用该方法获得了一个描述符对象，该对象中包含四条数据属性：value、enumerable、writable、configurable。

**当给属性进行赋值时，该属性默认的三个特性均为** `false`。

``` javascript
Object.defineProperty(ob, 'a', {value: 0});

Object.getOwnPropertyDescriptor(ob, 'a');
// => Object {value: 0, writable: false, enumerable: false, configurable: false}
```

我们也可以在创建对象时使用 `Object,create(prototype, properties)` 方法来定义它的属性，不过需要一个描述器对象来作为它的第二个参数，示例如下：

``` javascript
var ob = Object.create(Object.prototype, {
  a: { writable:true, enumerable:true, value: 1 },
  b: { enumerable: true, value: 2 }
}});
 
ob; // => {a:1, b:2}
```

## 对象的不可枚举性 | non-enumerable 

前面说到过可枚举性表示能够通过 `for...in` 循环返回所有属性，而不可枚举性自然相反。所以，拥有不可枚举性的属性无法使用关于操作 hashmap 型对象的大部分方法。

- 无法参与 `for...in` 循环的迭代
- 不会在使用了 `Object.keys` 的函数中出现
- 无法通过 `JSON.stringify` 进行序列化

虽然这三种特性是隐藏变量，但是我们依旧能够直接访问。

``` javascript
var ob = {a:1, b:2};
 
ob.c = 3;
Object.defineProperty(ob, 'd', {
  value: 4,
  enumerable: false
});
 
ob.d; // => 4
 
for( var key in ob ) console.log( ob[key] ); 
// 控制台输出
// 1
// 2
// 3
 
Object.keys( ob );  // => ["a", "b", "c"]
 
JSON.stringify( ob ); // => "{a:1,b:2,c:3}"
 
ob.d; // => 4
```

因为对象无法被序列化，所以我们可以用来做些有意思的事情：

``` javascript
// 声明一辆汽车模型，其中包含一些必要信息
var car = {
  id: 123,
  color: red,
  owner: 12
};
 
// 假设从数据库中获取到 id:12 的车主是 Javi
var owner = {
 id: 12,
 name: Javi
}
 
// 给这辆车添加一个 ownerOb 属性
// 把数据库中查询到的数据添加进去，以后没准会用到
Object.defineProperty( car, 'ownerOb', {value: owner} );

// 果然现在需要查询车主信息
car.ownerOb; // => {id:12, name:Javi}
 
// 序列化 car 对象后，车主 Javi 的信息不见了！
JSON.stringify( car ); // => '{id: 123, color: "red", owner: 12}'
```

所以 non-enumerable 在显示数据模型对象时大有裨益。

另外，可以通过 `Object.getOwnPropertyNames` 方法获得对象中的所有属性名。

## 对象的不可写性 | non-writable

在 ES6 之前，对常量来说，`const` 的特性最接近于 non-writable。一旦值被定义，就无法改变。

``` javascript
var ob = {a: 1};
 
Object.defineProperty( ob, 'B', {value: 2, writable:false} );
 
ob.B; // => 2
 
ob.B = 10;
 
ob.B; // => 2
```

上例中直接忽略了 ob.B 的重新赋值，**需要注意的是，在重新赋值时，往往会直接返回该赋值**。如果应用了 `strict mode` 的话，那么 ob.B 将会抛出 `TypeError`，而不是仅仅忽略写入的值。

``` javascript
var ob = {a: 1};
Object.defineProperty( ob, 'B', {value: 2, writable:false} );
 
// 赋值时返回该值
ob.B = 6; // => 6
ob.B = 1000; // => 1000
 
// 但是该属性值依旧未改变
ob.B; => 2;
 
function updateB(){
  'use strict';
  ob.B = 4; // 此处将抛出异常
}
 
updateB(); // 果然抛出异常了吧 O__O
```

**还需要注意的有，如果不可写的属性包含一个对象，虽然这个属性自身不可写，但是该对象中的属性却是可写的**。

``` javascript
var ob = {a: 1};
Object.defineProperty( ob, 'OB', {value: {c:3}, writable:false} );
 
ob.OB.c = 4;
ob.OB.d = 5;
 
ob.OB; // => {c:4, d:5}
 
ob.OB = 'hola';
 
ob.OB; // => {c:4, d:5}
```

如果想创建一个完全不可写的属性，使用`Object.freeze` 方法将阻止所有添加、删除、修改对象属性的操作，并且应用 `strict mode` 时将抛出 `TypeError` 异常。

``` javascript
var ob = { a: 1, b: 2 };
 
ob.c = 3;

Object.freeze( ob ); // => {a:1,b:2,c:3}
 
ob.d = 4;
ob.a = -10;
delete ob.b;
 
Object.defineProperty( 'ob', 'e', {value: 5} );
 
// 所有的更改都会被忽略
ob; // => {a:1,b:2,c:3}
```

## 对象的不可配置性 | non-configurable

可配置性表示能否修改属性的特性，通过 `defineProperty` 方法修改属性的可枚举性和可写性，但是 **如果属性定义的是不可配置性，你就只能将可写性修改成不可写性，其他的操作都将抛出 TypeError 异常**。

``` javascript
var ob = {};
Object.defineProperty( ob, 'a', {configurable:false, writable:true} );
 
Object.defineProperty(ob, 'a', { enumerable: true }); // throws a TypeError
Object.defineProperty(ob, 'a', { value: 12 }); // throws a TypeError
Object.defineProperty(ob, 'a', { writable: false }); // 只有此操作能成功
Object.defineProperty(ob, 'a', { writable: true }); // throws a TypeError
```

还有非常重要的一点是: **不可配置的属性无法通过对象的 `delete` 操作符进行删除**。

``` javascript
var ob = {};
 
Object.defineProperty( ob, 'a', {configurable: true, value: 1} );
 
ob; // => {a:1}
delete ob.a; // => true
ob; // => {}
 
Object.defineProperty( ob, 'a', {configurable: false, value: 1} );
 
ob; // => {a:1}
delete ob.a; // => false
ob; // => {a:1}
```

## 特殊的 var

``` javascript
var a = "hello";
delete a; // => false

b = "hi";
delete b; // => true
```

不用感到奇怪，上面提到，不可配置的属性无法通过对象的 `delete` 操作符进行删除，所以不使用 `var` 定义变量的时候，变量的特性 configurable 的值为 true，可以使用 `delete` 对其操作。 使用 `var` 定义一个全局变量的时候，变量的特性 configurable 的值为 false，不可以使用 `delete` 对其操作。使用下面的代码验证之：

``` javascript
Object.getOwnPropertyDescriptor(window, 'a')
// => Object {value: "hello", writable: true, enumerable: true, configurable: false}

Object.getOwnPropertyDescriptor(window, 'b')
// => Object {value: "hi", writable: true, enumerable: true, configurable: true}
```

## 总结

`Object.defineProperty` 在 ES5 中就已出现，我们可以用它来学习 Javascript 核心对象的工作原理，还可以用来自定义属性的 getters 和 setters。总之，这很有趣。