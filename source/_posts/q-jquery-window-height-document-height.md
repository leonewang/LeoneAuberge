---
title: "Question Shredder: $(window).height() Problem"
tags:
  - HTML5
  - JQuery
  - Question
categories:
  - Develop
date: 2015-08-02 10:05:33
---

这周遇到的一个小问题，弹出框的显示位置莫名偏下，其位置是由 CSS 的 left 与 top 属性决定，top 的值又根据浏览器视口（viewport）高度和弹出框（popup）的高度计算决定：`top = ($(window).height() - popup.height()) / 2;`

<!-- more -->

[![qiestion163 jquery windowWidth = documentWidth d](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-08-02-09.03.30-632x404.png)](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-08-02-09.03.30.png)

由于弹出款明显偏低，**猜测视口高度并非真正的视口高度**，于是输出视口高度 $(window).height() 与文档高度 $(document).height() 进行对比。

[![屏幕快照 2015-08-02 09.01.21](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-08-02-09.01.21-632x315.png)](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-08-02-09.01.21.png)

结果表明，**页面视口高度与文档高度竟然一致**，即 `$(window).height() = $(document).height()`。于是乎调试模式开启发现 jsp 解析出得页面中并没有文档声明 `<!DOCTYPE html>` （我好像知道了点什么），返回代码一查看。如下图，有没有发现什么端倪？

[![屏幕快照 2015-07-30 15.02.00](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-07-30-15.02.00-632x166.png)](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-07-30-15.02.00.png)

没错，头部引用了某个 jsp 文件，导致之后的文档声明没有被解析，所以咯，大家以后都要仔细些。将引用放至 head 标签中，问题解决。

[![屏幕快照 2015-08-02 09.00.58](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-08-02-09.00.58-632x315.png)](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-08-02-09.00.58.png)[![屏幕快照 2015-08-02 10.05.11](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-08-02-10.05.11-632x404.png)](http://7xkj8o.com1.z0.glb.clouddn.com/wp-content/uploads/2015/07/屏幕快照-2015-08-02-10.05.11.png)
