---
title: "Responsive Web Design (Two)"
tags:
- CSS3
- HTML5
categories:
- Summarize
date: 2015-07-20 22:36:10
---
## 私有前缀

在 CSS3 模块标准尚未被 W3C 批准或者标准所提议的特性尚未被浏览器完全实现时，浏览器厂商会使用所谓的 “私有前缀” 来测试 “试验性” 的 CSS3 特性。开发 CSS3 时，要记住添加相关的浏览器前缀以保证最广泛的浏览器兼容。

``` css
.main {
  -ms-border-radius: 10px;    /* Microsoft */
  -o-border-radius: 10px;     /*Opera */
  -moz-border-radius: 10px;   /* Mozilla (Firefox) */
  -webkit-border-radius: 10px;    /* Webkit (Safari,Chrome) */
  border-radius: 10px;    /* W3C 标准 */
}
```

我知道你在想什么，对同一个属性写多个私有前缀声明是否会造成代码臃肿？确实会有那么一点。但无论追加多少属性，相对于使用图片完成类似效果，这仍然是一个快速、优雅而健壮的解决方案。这里推荐给大家一个相关网站[caniuse.com](http://caniuse.com/)，在这里可以知道当前浏览器对特定 HTML5 和 CSS3 特性的支持程度（可按特性搜索）。

<!-- more -->

当然了，私有前缀实在是太长了，我想没有一个人愿意手工敲上去，所以也有可以为 CSS 文件自动追加私有前缀的 JavaScript 方案，如 [-prefix-free](http:)，这样在样式表中就可以只写 W3C 规定的标准代码。

## CSS3

#### 新增选择器

###### 子字符串匹配属性选择器

1. “匹配开头” 的属性选择器
语法 `Element [attribute^="value"]`
例：选择所有 class 属性以 large 开头的图片:
``` css
img [class^="large"] {
    width: 100%;
}
```

2. “匹配包含内容” 的属性选择器
语法 `Element [attribute*="value"]`
例：选择所有 class 属性包含 large 的图片:

``` css
img [class*="large"] {
    width: 100%;
}
```

3. “匹配结尾” 的属性选择器
语法 `Element [attribute$="value"]`
例：选择所有 class 属性以 large 结尾的图片:

``` css
img [class$="large"] {
    width: 100%;
}
```

###### CSS结构伪类

1. `:last-child` 选择器
> 例：`li :last-child` 用以匹配最后一项列表项。

2. `:nth-child` 选择器
> 例：`li :nth-child(even)` 用以匹配偶数项 `li` 元素（可选参数 even, odd, 自定义）。主要说说自定义，比如 `li :nth-child(2n+1)` 用以匹配第 1、3、5 … 项列表项。
> **注意：n 从 0 开始**

3. `:nth-last-of-type` 选择器
> 例：`li .list :nth-last-of-type(n+2)` 表示从第二个匹配元素`li` 开始，匹配每一个类名为 list 的列表项。

4. `:not` 选择器
> 例：`li :not(.list)` 用以匹配类名不是 list 的列表项。

#### 动画过渡的局限性

在使用 CSS3 制作动画时，有些属性无法过渡，尽管规范说它应该可以。例如：background-gradient 属性就无法过渡，但理论上所有的 CSS 属性都是可以过渡的。

#### 元素透明

**为什么不使用 opacity？** 这个问题在初学时困扰了我 long long time。原因在于 opacity 设置的透明度会对整个元素产生影响，而使用 RGBA 或 HSLA 则可以仅让部分有透明效果，这样一个元素就可以拥有透明背景，但内部的文字仍不透明。

#### 用 HSL 代替 RGB、HEX

**为什么要用 HSL 呢？** 首先要知道 HSL 是由色相（Hue）、饱和度（Saturation）、明度（Lightness）所组成的，而 RGB 是由 R（Red）、G（Green）、B（Blue）所组成。在我们使用颜色标准时，我相信没有几个人能够看到 `RGB(12,12,12)` 就知道这是什么颜色，但是当看到 `HSL(12,12,12)`时，我却能知道这大概是什么颜色——介于红色与橘红色之间的颜色。不要太崇拜 `&lt;(￣▽￣)&gt;` 哇哈哈…你也可以：

* **H (Hue)** 分量
> 代表的是人眼所能感知的颜色范围，这些颜色分布在一个平面的色相环上，取值范围是0°到360°的圆心角，每个角度可以代表一种颜色。色相值的意义在于，我们可以在不改变光感的情况下，通过旋转色相环来改变颜色。在实际应用中，我们需要记住色相环上的六大主色，用作基本参照：360°/0°红、60°黄、120°绿、180°青、240°蓝、300°洋红，它们在色相环上按照60°圆心角的间隔排列。

* **S (Saturation)** 分量
> 指的是色彩的饱和度，它用0%至100%的值描述了相同色相、明度下色彩纯度的变化。数值越大，颜色中的灰色越少，颜色越鲜艳，呈现一种从理性(灰度)到感性(纯色)的变化。

* **L (lightness)** 分量
> 指的是色彩的明度，作用是控制色彩的明暗变化。它同样使用了0%至100%的取值范围。数值越小，色彩越暗，越接近于黑色；数值越大，色彩越亮，越接近于白色。

## 离线 Web 应用

必须在每一个准备离线使用的页面的 HTML 标签中都追加 `manifest = "/offline.manifest"` 属性。当然这仅适用于不需要与服务器端打交道的页面，这点我想你应该明白。

*以上均为学习 Responsive Web Design with HTML5 and CSS3 后的个人总结，如有错误欢迎指出。*
