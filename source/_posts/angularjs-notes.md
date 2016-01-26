---
title: AngularJS Notes
date: 2016-01-18 20:13:22
updated: 2016-01-18 20:13:22
tags:
    - JavaScript
    - AngularJS
categories:
    - Notes
---

> REF:
[AngularJS 2013 Brad Green](http://baike.baidu.com/link?url=SlAzoxj6IVBVqvcoKI6EExxuU1a4yXzW9CiSMFdN9azJXxovxoD0LCo9ZoMZ58KFC7M-jQkMfZ9GtJeXH2r0M_)
[http://blog.csdn.net/renfufei/article/details/19038123](http://blog.csdn.net/renfufei/article/details/19038123)

## 依赖注解 | Dependency Annotation
injector 怎么知道需要注入何种 service 呢?
为了解决依赖关系,应用程序开发者需要提供 injector 需要的 annotation 信息。在 Angular 中,某些API函数通过使用 injector 来调用,请按照API文档。injector 需要知道注入哪些服务给函数。下面是通过 service name 信息对代码进行注解的三种等价方式。他们都是等价的，可以在适当的地方互换使用.
<!-- more -->
### 推断依赖关系 | Inferring Dependencies
最简单的获取依赖的方式,就是让函数参数名和依赖的名字一致。

``` javascript
function MyController($scope, greeter) {
  ...
}
```

给定一个 function, injector 通过检查函数声明和提取参数名称可以推断出 service 的名称 。在上面的例子中, $scope 和 greeter 是需要注入 function 的两个 services。
虽然简单直接, 但这种方法在 JavaScript 压缩/混淆 时会失效,因为会重命名方法的参数名。这使得这种注解方式只适用于 [pretotyping](http://www.pretotyping.org/), 或者 demo 程序中。

### $inject 注解 | $inject Annotation
在产品级的代码中往往会压缩代码，这会重命名方法参数名，AngularJS 便无法判断出原来所需要的服务和变量，为了能够在重命名方法参数名后依然可以注入正确的 services, 函数需要通过 $inject 属性来注解. $inject 属性是一个数组,包含 需要注入的 service 名字。

``` javascript
var MyController = function(renamed$scope, renamedGreeter) {
  ...
}
MyController['$inject'] = ['$scope', 'greeter'];
```

在这种情况下,$inject数组中的值的顺序必须和要注入的参数的顺序一致。使用上面的代码片段作为一个例子, `$scope` 将注入到 `renamed$scope`, 而 `greeter` 将注入到 `renamedGreeter`。再次提醒注意 $inject 注解必须和 函数声明时的实际参数保持同步(顺序,个数...)。
对于 controller 声明,这种注解方法是很有用的,因为它将注解信息赋给了 function。

### 内联注解 | Inline Annotation
有时候并不方便使用 $inject 注解,比如在注解 directives的时候。比如下面的示例：

``` javascript
someModule.factory('greeter', function($window) {
  ...
});
```

因为需要使用临时变量，导致代码膨胀为：

``` javascript
var greeterFactory = function(renamed$window) {
  ...
};
greeterFactory.$inject = ['$window'];
someModule.factory('greeter', greeterFactory);
```

这也是提供第三种注解的原因，以数组形式注入：

``` javascript
someModule.factory('greeter', ['$window', function(renamed$window) {
  ...
}]);
```

**所有的 annotation 风格都是等价的,在 Angular 中,只有支持注入的地方都可以使用.**

## $http Service Configuration

$http 配置请求模板：
```
$http({
    method: string,
    url: string,
    params: object,
    data: string or object,
    headers: object,
    transformRequest: function transform(data, headerGetter) or an array of functions,
    transformResponse: function transform(data, headerGetter) or an array of functions,
    cache: boolean or Cache object,
    timeout: number,
    withCredentials: boolean
});
```

- `transformRequest` 如果请求的配置对象属性中包含 JS 对象，那么就把这个对象序列化成 JSON 格式。
- `transformResponse` 如果检测到了 XSRF(Cross-site Request Forgery) 前缀，则直接丢弃；如果检测到了 JSON 响应，则使用 JSON 解析器对其反序列化。

假设有一个服务，需要 POST 数据使用 `key1=1&key2=2` 来代替 JSON 格式的 `{key1: 1, key2: 2}`，于是在请求中进行这种转换：

``` javascript
Angular.module('myApp')
    .config(function() {
        $httpProvider.defaults.transformRequest = function(data) {
            // 使用 jQuery 的 param 方法
            // 把 JSON 数据转换成字符串形式
            return $.param(data);
        }   
});
```

## 使用 RESTful 资源

### 声明

``` javascript
$resource('/url/:id',{id: '@userId'});
```

`@userId` 表示如果正在使用一个从服务端返回的对象，当调用这个对象上的任一方法时，对象上的 `userId` 属性都会被赋给 `id` 参数。

``` javascript
CreditCard.get({id: 123}, function(card) {
    card.userId = 345;
    // card 对象中的 userId 将赋给 id
    card.$save();
});
```

### 自定义方法

``` javascript
myModule.factory('CreditCard', ['$resource', function($resource) {
    return $resource('/user/:id', 
        {id: '@userId'},
        {charge: {
            method: 'POST', // 请求类型
            params: {charge: true},  // 传递的参数
            isArray: false // 返回的结果是否是一个数组
        }});
}]);
```

这里指定了一个 `charge` 方法，可以通过一个对象作为 `$resource` 的第三个参数来配置这个方法，对象中的 key 就是需要暴露的方法名称。

> 这是一种灵活的编码风格，根据上面的代码，对于配置对象 `{charge: {method: 'POST', params: {charge: true}, isArray: false}}`，Angular 会将其解析成一个方法，然后把这个方法绑定到返回的 RESTful 对象上。

上面的配置对象解释之后的方法为：

``` javascript
CreditCard.charge = function(charge, isArray) {
    ...
}
```

###  Callback

``` javascript
var card = CreditCard.query();
```

上面的 `query` 调用并没有像 `get` 一样在回调函数中进行赋值，而是直接将信用卡对象赋值给了 card 变量，你可能会担心在对服务器进行异步请求的情况下，这种代码还能正常运行吗？

这种担心是合理的，但事实上，这段代码完全正确，并且能够运行。这里发生的事情是：Angular 赋给了 card 对象一个（所返回对象或者数组的）引用，只有在未来某个时间对服务器的请求返回来之后，这个引用才被真正的赋值。**在此期间，引用对象一直是空的。**

## Directive

从高层面看 Angular 的初始化过程：

1. **加载脚本**
加载 Angular 库，并查找 ng-app 指令，从而找到引用边界。

2. **编译阶段**
在这个阶段中，Angular 将会遍历 DOM 结构，标识出模板中注册的所有指令。对于每一条命令，它会根据指令定义的规则来转换 DOM 结构，如果存在 compile 函数，则调用它。调用 compile 函数将得到一个编译好的 template 函数，它将会调用从指令中搜集而来的 link 函数。

3. **链接阶段**
为了让视图称为动态的，Angular 会对每条指令运行一个 link 函数。link 函数的一般操作是在 DOM 或者模型上创建监听器，监听器会使视图和模型的内容随时保持同步。

总结来说，后两个阶段就是负责转换模板的编译阶段，以及修改视图中数据的链接阶段。

### 指令定义选项

- `compile` 对模板自身进行转换和编译，此函数在生命周期中只调用一次。如使用 ng-repeat 时，会以编程的方式修改 DOM 模板，生成多个 DOM 元素实例。

``` javascript
compile: function compile(tElement, tAttrs, transclude) {
    return  {
        pre: function preLink(scope, iElement, iAttrs, controller) {...}, // 运行于编译阶段之后，指令链接到子元素之前
        post: function postLink(scope, iElement, iAttrs, controller) {...}, // 运行于所有子元素指令都链接之后
    }
}
```

- `link` 负责在模型和视图之间进行动态关联。

``` javascript
function preLink(scope, iElement, iAttrs, controller) {...},
```

作用域在链接阶段才会被绑定到编译之后的 link 函数上，然后再通过数据绑定技术，指令就变成了动态的。如果要修改 DOM 结构必须在 `postLink` 函数中来执行，如果在 `preLink` 中执行，则会破坏数据绑定过程，导致链接出错。

<div class="tip">
<div>**compile 函数和 link 函数不同点**</div>
<div>1. link 函数会访问 scope 对象，而 compile 不会。这是因为，scope 对象在编译阶段还不存在。</div>
<div>2. compile 函数会接收模板元素及其属性列表，而 link 函数会接收到视图实例对象（视图实例由模板创建）。</div>
</div>
