---
title: "Responsive Web Design (One)"
tags:
- CSS3
- HTML5
categories:
- Summarize
date: 2015-07-19 16:45:49
---

> 来自 [gs.statcounter.com](http://gs.statcounter.com/) 的统计数据表明产品从 2010 年 7 月到 2011 年 7 月的 12 个月中，全球手机浏览器的使用量中 2.86% 上升至 7.02%。Internet Explorer 6 的使用率则是从 8.76% 下降到 3.42%。i到 2015 年 7 月，Internet Explorer 6、7、8 三个版本加起来也只不过 10% 的使用率。

越来越多的人使用小屏幕设备上网。这些设备上的浏览器在设计时都考虑到了如何显示好现有网站，手机浏览器会将一个标准网页缩放至与设备可视区域恰好匹配，然后用户在自己需要的内容区域上放大浏览，为了看清楚页面内容，就需要不停的放大、缩小页面，然后为了看到视口外的文字，又要左右拖动，就像我高中时用手机浏览网页一样。
<!-- more -->
于是 **响应式 Web 设计** 就应运而生，使页面能够根据视口宽度的改变而进行自由缩放，例如，先针对大视口设计（用户大多数情况下使用鼠标），将页面宽度加宽、导航链接设计成简单地文字连接，然后使用媒体查询重写样式，为拇指一族提供更大的点击区域并将页面宽度适当减小。

## 使用媒体查询

创建媒体查询时，最常用的是设备的视口宽度（ width ）和屏幕宽度（ device-width ）。下面列出一些比较好玩的特性。
* width: 视口宽度
* height: 视口高度
* device-width: 视口宽度
* device-height: 视口高度
* orientation: 检测设备处于横向还是纵向
* aspect-ratio: 基于视口宽度和高度的宽高比。一个 16:9 比例的显示屏可以这样定义 aspect-ratio: 16/9。
* resolution: 检测屏幕或打印机分辨率，如 min-resolution: 300dpi。还可以接受每厘米像素点数的度量值，如 min-resolution: 118dpcm。
* grid: 检测输出设备室网格设备还是位图设备。

## 加载媒体的最佳方法

现代浏览器虽然可以智能地忽略与自身不匹配的样式文件，但它却不一定不下载这些文件。因此，将不同媒体查询的样式保存到独立的文件中没有太大的好处，使用多个独立的文件会增加用于页面渲染的 HTTP 请求的数量，从而导致页面加载变慢。使用如下语法即可在已有样式表中加入媒体查询：

``` css
// 当视口宽度小于 768px 时，页面的宽度将变为 700px；
@media screen and (max-width: 768px) {
    body {
        width: 700px;
    }
}

// 当视口宽度大于 768px 小于 992px 时，页面宽度将变为 768px；
@media screen and (min-width: 768px) and (max-width: 992px) {
    body {
        width: 768px;
    }
}
```

当然除了上面的媒体查询外还可以使用 CSS 的 `@import` 指令在当前样式表中按条件引入其他样式表。例如下面的代码会给视口最大宽度为 768 像素的显示屏设备加载一个名为 mobile.css 的样式表。

``` css
@import url(mobile.css) screen and (max-width: 768px);
```

> 切记，使用 CSS 的 `@import` 方法会增加 HTTP 请求（这会影响加载速度），所以请谨慎使用该方法。

## 拥抱流式布局

现在来讲讲布局的问题，我们经常需要根据情况对元素宽度、字体大小进行修改，于是乎我们就要对所有的使用了像素点作为单位的属性进行修改，然后便是一项浩大的工程，所以固定布局禁不起未来的考验。

``` css
/* 示例 1 */
body {
    width: 768px;
    font-size: 16px;
}
.left {
    width: 450px;
    font-size: 17px;
}
.right {
    width: 300px;
    font-size: 15px;
}
```

#### 百分比布局

首先来看看元素宽度，如果我们需要将整个页面的宽度加宽 100 px，那么就要将页面的其他各个部分也进行宽度上的调整，使用百分比布局能让我们修改宽度来的更高效。记住百分比转换公式一切迎刃而解，貌似你我数学不至于差到这种程度：

</div>
<div id="wmd-preview-section-212" class="wmd-preview-section preview-content">
<div class="">
<div class="MathJax_SVG_Display"><span id="MathJax-Element-1-Frame" class="MathJax_SVG">百分比宽度目标元素宽度上下文元素宽度</span></div>
</div>
现在我们就将 **示例 1** 修改为百分比布局:

``` css
/* 示例 2 */
body {
    width: 768px;   /* 仅需修改上下文元素宽度即可完成调整 */
    font-size: 16px;
}
.left {
    width: 58.59375%;   /* 450 ÷ 768 */
    font-size: 17px;
}
.right {
    width: 39.0625%;    /* 300 ÷ 768 */
    font-size: 15px;
}
```

#### 用 em 替换 px

过去的几年里，网页设计师使用 em 代替 px 主要是为了文字缩放。因为老版本的 IE 无法缩放像素单位的字体。不过现代浏览器很久以前就支持缩放以像素为单位的文字了。那用 ex 替换 px 还用什么必要性和优越性吗？有两点毋庸置疑的理由：

1.  那些使用了 IE6 的用户也能缩放文字。
2.  可以使设计师和开发人员的生活更加简单。

> **em** 的实际大小是相对于其上下文的字体大小而言的。如果我们给`<body>` 标签设置文字大小为 100%，给其他文字都是用相对单位 em，那这些文字都会受 body 上的初始声明的影响。这样做的好处就是，如果在完成了所有的文字排版后，客户又提出将页面文字统一放大一点，我们就可以只修改 body 的文字大小，其他所有的文字大小也会相应变大。
在这样的情况下，我们如果采用了相对大小，一切就来的简单得多了，依旧使用万能的公式 <span class=""><span id="MathJax-Element-2-Frame" class="MathJax_SVG">百分比尺寸目标元素尺寸上下文元素尺寸</span></span> 我们只需要修改`body` 的 `font-size` 属性即可。将 **示例 2** 字体更改为相对大小，如下：

``` css
/* 示例 3 */
body {
    width: 768px;
    font-size: 16px;    /* 修改此处即可 */
}
.left {
    width: 58.59375%;
    font-size: 1.0625em;    /* 17 ÷ 16 */
}
.right {
    width: 39.0625%;
    font-size: .9375em; /* 15 ÷ 16 */
}
```

## 添加响应式媒体

对于很多人来说，可能第一次听说 HTML5 是在 Apple 公司拒绝在 iOS 设备上支持 Flash 的时候，Flash 作为浏览器的视频服务插件已经占据了主导地位，而其发热的特性也已经是众所周知了，所以 Apple 并没有使用 Adobe 的专有技术，而是决定依靠 HTML5 来处理富媒体渲染。HTML5 本身在这一领域已经有了长足进步，Apple 公司对 HTML5 的公开支持又极大促进了他的发展，并使其媒体工具在公众中赢得了更广泛的青睐（够了，我再也不要听到风扇的声音！）。

#### 用 HTML5 的方法为页面添加视频

看到下面的一行代码，你我会心一笑，简直就像是插入了一张图片，不说了，我要偷着乐会儿。

``` html
<video src="myVideo.ogg"></video>
```

#### 提供备用的媒体源文件

果然我还是太年轻，请把刀给我。最初的 HTML5 规范呼吁所有浏览器内置支持使用 Ogg 格式直接播放视频或音频，但是由于 HTML5 工作组的内部争议，曾经作为基线标准的支持 Ogg 的主张在最近的更新的 HTML5 规范中被放弃。因此，目前一些浏览器支持某一套视频和音频的文件格，而另一些浏览器则支持其他格式。例如，Safari 只允许使用 MP4/H.264/AAC 媒体文件，而 Firefox 和 Opera 则只支持 Ogg 和 WebM。

不过好在有一种方法能够在一个标签内支持多种媒体类型：

``` html
<video width="640" height="480" controls autoplay preload="auto" loop poster="myVideoPoster.jpg">
    <source src="myVideo.ogg" type="video/ogg">
    <source src="myVideo.mp4" type="video/mp4">
    Oops, you need a new browser
</video>
```

如果浏览器支持 Ogg 格式，则使用第一个文件，否则它会继续往下解析下一个`<source>` 标签。

#### 响应式视频

对于 HTML5 式嵌入视频，修正方法很简单，删除视频标签中的 height 和 width 属性，然后再 CSS 中追加：

``` css
video {
    max-width: 100%;
    height: auto;
}
```

但是有时候我们会想要嵌入 Youtube 的视频，而 HTML5 并不能解决嵌入 iframe 视频的响应问题，最简单的方法是用一个 jQuery 小插件：[FitVids](http://fitvidsjs.com/)。

*以上均为学习 Responsive Web Design with HTML5 and CSS3 后的个人总结，如有错误欢迎指出。*
