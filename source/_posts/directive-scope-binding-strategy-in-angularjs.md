---
title: Directive Scope Binding Strategy in AngularJS
date: 2016-01-26 21:07:15
updated: 2016-01-26 21:07:15
tags:
    - JavaScript
    - AngularJS
categories:
    - Notes
---
我们经常想要我们的指令能够在特定的情况下与外界进行数据上的交互，所以在编写指令过程中经常会使用到独立 Scope 选项，这就需要借助绑定策略之手。

绑定策略的三种形式：`@`、 `=`、 `&`。

### `@`
将本地作用域和 DOM 中的属性值（该属性值必须存在于父作用域中）进行绑定。

``` javascript
directive("direct",function() {
   return {
      restrict: 'ECMA',
      template: '<div>指令中：{{ name }}</div>',
      scope:{
        name:'@forName'
      }
   } 
})
.controller("nameController",function($scope) {
      $scope.Name="leone"; 
});
```
<!-- more -->
HTML

``` html
<div ng-controller='nameController'>
    <direct for-name='{{Name}}'></direct>
</div>
```

运行结果就是上面指令 `scope:{name:'@forName'}` 中的 name 与父控制器中的 Name 属性绑定起来了，注意，**属性名使用驼峰命名法**。这里也可以简写为 `name：'@'`，如果这样写的话，就默认 DOM 中的属性名为 name，所以 DOM 中需要将属性修改为 `name='{{Name}}'`。其实，另外两个绑定形式也有这样的简写规则。

### `=`
= 与 @ 不同点在于，@ 主要是针对字符串使用，而 = 主要是针对某个对象的引用。

``` javascript
directive("direct",function() {
   return {
      restrict: 'ECMA',
      template: '<div>指令中：{{ case.name }}</div>',
      scope:{
        case:'='
      }
   }
})
.controller("nameController",function($scope) {
      $scope.data=[{name:"leone"},{name:"wang"}]; 
});
```

HMTL

``` html
<div ng-controller="nameController">
  <direct case="data[0]"></direct>
  <direct case="data[1]"></direct> 
<div>
```

结果是一个 「leone」，一个「wang」。这个例子中，data 是一个对象数组，里面包含了两个对象，我们分别把这两个对象传递给了 DOM 中的 case 属性，case 属性就把这个对象的引用绑定给了指令 scope 中的 case，然后在模板中通过 `{{case.name}}` 进行使用。

### `&`
对父级作用域进行绑定，并将其中的属性值包装成一个函数，即任何类型的属性值都会被包装成一个函数，比如一个单纯的字符串，或是一个对象数组，或是一个函数方法，如果是字符串、对象数组和无参的函数，那么可想而知，它们都会被包装成一个无参的函数，若是有参的函数方法则反之，并且我们需要为其传入一个对象。

``` javascript
myApp.directive("direct",function(){ 
    return{
       restrict: 'ECMA',
       template: '<div><input ng-model="model"/></div>' + '<div><button ng-click="show({name:model})">show</button>',
       scope:{
           show:'&'              
       }                      
    }
})
.controller("nameController",function($scope){
    $scope.showName=function(name){ 
       alert(name); 
    } 
});
```

HTML

``` html
<div ng-controller="nameController">
    <direct show="showName(name)"></direct>
</div>
```

将 DOM 中的 show 属性值 `showName(name)` 函数绑定到指令 scope 中的 show 上，然后通过模板中的点击事件触发 show 函数，并将一个叫做 model 的对象作为 name 参数传递了进去。