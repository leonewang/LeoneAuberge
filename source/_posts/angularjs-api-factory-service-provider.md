---
title: Difference between Factory, Service and Provider in AngularJS
date: 2016-01-20 23:10:05
updated: 2016-01-20 23:10:05
tags:
    - JavaScript
    - AngularJS
categories:
    - Notes
---
## 工厂方式 | Factory

如果有一个类或对象，需要首先为他提供一些参数和逻辑，然后才能对它进行初始化，那么就能够使用这里的 Factory 接口。Factory 是一个函数，它负责创建一些特定的值或者对象，下面是一个 Greeter 实例，这个函数需要一条问候语来初始化：

``` javascript
function Greeter(saluation) {
    this.greet = function(name) {
        return saluation + name;
    };
}
```
<!-- more -->
greeter 函数实例：

``` javascript
myapp.factory('greeter', function(sal) {
    return new Greeter(sal);
});
```

然后这样调用它：

``` javascript
var greeter = greeter('Hello');
```

## 服务方式 | Service

想要用 service 实现上面的实例，可以把 greet Factory 改为下面的 greet Service:

``` javascript
myApp.service('greeter', Greeter);
```

Service 与 Factory 的不同点在于，Factory 会直接调用传递给它的函数（ `function(sal) { ... }` ）后返回执行的结果；而 Service 将会通过调用传递给它的构造方法去执行 **new** 操作（ 示例中相当于返回了 `new Greeter` )，然后再返回结果。

## 供应者方式 | Provider

> Provider recipe is syntactically defined as a custom type that implements a `$get` method. This method is a factory function just like the one we use in Factory recipe. In fact, if you define a Factory recipe, an empty Provider type with the $get method set to your factory function is automatically created under the hood.

Provider recipe 是语法定义为一个自定义类型，实现 `$get` 的方法。这个方法是一个工厂方法，就像我们在 Factory recipe 中使用的一样。事实上，如果定义一个 Factory recipe，钩子会自动创建一个包含空 Provider 类型 $get 方法的工厂方法。

> You should use the Provider recipe only when you want to expose an API for application-wide configuration that must be made before the application starts. This is usually interesting only for reusable services whose behavior might need to vary slightly between applications.

只有当希望一个应用程序配置的 API 必须在应用程序启动之前被创建，才应该使用 Provider recipe 。通常只关注可重用服务的行为可能在应用程序间略有不同。

Provider 的使用最为复杂，但是也是可定制性最高的部分，Provider 中既绑定了 Factory 也绑定了 Service，并且在注入系统准备完毕之前，还可以使用模板的 API 创建一个含有注入 Provider 的 config 方法，下面使用 Provider 改造：

``` javascript
myApp.provider('greeter', function() {
    var saluation = 'Hello';
    this.setSaluation = function(s) {
        saluation = s;
    };
    function Greeter(a) {
        this.greet = function(a) {
            return salustion + ' ' + a;
        }
    }
    this.$get = function(a)  {
        return new Greeter(a);  
    };
});
```

在 config 阶段，只有 Provider 能被注入：

``` javascript
myApp.config(function(greeter) {
  //不会运行 -- greeter是一个服务的实例
  //只有服务的provider能被注入到config中
});
```

但是可以通过下面的方法注入，之后 Angular 就会调用这个 Provider 的 `$get` 函数来返回该服务的一个实例：

``` javascript
myApp.config(function(greeterProvider) {
    // It will work
});
```

<div class='tip'>
无论何时为服务定义了一个 Provider，这个 Provider 的名字都是 serviceProvider，service 是服务的名称。
</div>

## 总结 | Conclusion

| Feature / Recipe type |     Factory |   Service   | Provider |
| :-------------------- | :----------:| :---------: |:--------:|
| Can have dependencies - 支持依赖注入 |   √       |  √           | √   |
| Uses type friendly injection - 使用友好的方式注入 |  ×  |  √           | ×    |
| Object available in config phase - 配置阶段可用 |×|  ×            | √   |
| Can create functions / primitives - 可以创建方法、基元 |  √ |  ×       | √   |
